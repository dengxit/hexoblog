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

用户提交预约时候重复提交或是网络延迟导致的反复提交，可能会生成脏数据；解决办法有很多，例如前端通过触发提交操作时的按钮短暂锁定或是弹层确认，不过设计接口的一个主要准则就是后端不应当完全信任前端提交的数据，因此从后端层面也要加上相应的限制。这里就可以使用Redis的Setnx命令。


>只在键 key 不存在的情况下， 将键 key 的值设置为 value 。
>若键 key 已经存在， 则 SETNX 命令不做任何动作。
>SETNX 是『SET if Not exists』(如果不存在，则 SET)的简写。
>命令在设置成功时返回 1 ， 设置失败时返回 0 。

```
#伪代码（Laravel）
#以业务逻辑相关参数作为key
try{
   DB::beginTransaction();
   $lock = Redis::setnx($key, 1); # value根据需求设置 
   if (!$lock) {
      //to：该预约已提交 请务重复提交
   }
   //其他业务存储逻辑
   DB::commit();
   Redis::del($key);
}catch(\Exception $e){
   //Log
   DB::rollBack();
   Redis::del($key);
}
```