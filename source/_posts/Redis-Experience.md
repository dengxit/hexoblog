---
title: Redis-Experience
date: 2020-06-16 15:16:48
tags:
---

#### First Acquaintance Redis

* What is Redis
> Redis is an open source, in-memory data structure store, used as a database, cache and message broker. It supports data structures such as strings, hashes, lists, sets, sorted sets with range queries etc. Redis has built-in replication, Lua scripting, LRU eviction, transactions and different levels of on-disk persistence, and provides high availability via Redis Sentinel and automatic partitioning with Redis Cluster
* Why use Redis
> Blazingly fast
> High performance & low latency
> Simplicity while developing complex functionality
> Compatibility
> Being used by tech-giants
> Open source and stable
* How to use Redis

  * [Redis文档（英）](https://redis.io/documentation)
  * [Redis文档（中）](http://redisdoc.com/)

<!-- more -->

#### 业务场景一 ：预约业务

用户提交预约时候重复提交或是网络延迟导致的反复提交，可能会生成脏数据；解决办法有很多，例如前端通过触发提交操作时的按钮短暂锁定或是弹层确认，不过设计接口的一个主要准则就是后端不应当完全信任前端提交的数据，因此从后端层面也要加上相应的限制。

使用Redis的Setnx命令：


>只在键 key 不存在的情况下， 将键 key 的值设置为 value 。
若键 key 已经存在， 则 SETNX 命令不做任何动作。
SETNX 是『SET if Not exists』(如果不存在，则 SET)的简写。
命令在设置成功时返回 1 ， 设置失败时返回 0 。

```
#伪代码（Laravel）
#以业务逻辑相关参数作为key
try{
   DB::beginTransaction();
   //看是否被锁，没有则锁上
   $lock = Redis::setnx($key, 1); # value根据需求设置 
   if (!$lock) {
      //to：该预约已提交 请务重复提交
   }
   //其他业务存储逻辑
   DB::commit();
   //解锁
   Redis::del($key);
}catch(\Exception $e){
   //Log
   DB::rollBack();
   //解锁
   Redis::del($key);
}
```

#### 业务场景二 ：消息通知
在特定业务操作下触发消息通知，记录触发者和被通知者，该业务操作非常频繁，并且消息的具体内容数据库有留存，消息通知的记录不需要留底记录，即阅即销；消息需要按照一定的规则进行排序显示一定数量（比如按时间显示最早3条）;

使用Redis有序集合

>将一个或多个 member 元素及其 score 值加入到有序集 key 当中。
如果某个 member 已经是有序集的成员，那么更新这个 member 的 score 值，并通过重新插入这个 member 元素，来保证该 member 在正确的位置上。
score 值可以是整数值或双精度浮点数。
如果 key 不存在，则创建一个空的有序集并执行 ZADD 操作。
当 key 存在但不是有序集类型时，返回一个错误。

```
#伪代码（Laravel）

#每当业务触发生成消息
Redis::zadd($key,$sort,$value);
#$key可以以用户ID加特定前缀表示，一个用户的消息频道是一个key集合
#$sort 为设置的排序字段例如时间戳
#$value可以存放关键信息用于查询消息具体内容

#获取某个用户的消息频道最新三条消息
public function getLastThirdMessages()
   {
       $data = [];
       $user = Auth::user();
       $key = getKey(CacheHelper::CACHE_PREFIX,$user->id);  #特定用户的key
       $result = zrange($key,0,2,'withscores');  #按sort排序取出集合中最新三条

       //redis 查询
       if(count($result) > 0){
           $data['count'] = zcard($key);      # 统计该用户消息总数
           foreach ($result as $key => $v){
               $data['data'][] = self::getDataByRedisKey($key);
           }
       }else{
           //数据库 查询
               /**
               *  
               *   这里查询出来的key再加入缓存 #TODO
               *
               **/
       }
       return $data;
   }
   
# 对消息确认或者删除
Redis::zRem($key,$value));
```
#### 缓存击穿

上文在key查询不到时走DB查询再存入缓存，这样可能会出现 缓存击穿 问题

> key对应的数据在DB存在，但在redis中不存在，若有大量请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候请求过大可能会瞬间把后端DB压垮

解决办法：就是在缓存失效的时候,先使用SETNX尝试能否加缓存，当操作成功时，再进行读取数据库回设缓存

```
#上文TODO代码中的简单实现示例

if(Redis::setnx($mutex_key, $value)){
    # geyByDB
    # set 缓存
    # del($mutex_key)
}else{  //其他线程setnx成功过了，等待时间从新获取缓存即可
    sleep(1);
    getByKey;
}

```



