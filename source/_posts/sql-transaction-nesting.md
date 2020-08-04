---
title: sql-transaction-nesting
date: 2020-08-04 15:19:50
tags:
- Mysql
---


##### 事务嵌套

最近在业务代码中遇到了Mysql的事务嵌套的情况，伪代码如下

```
function A(){
    try{
         beginTransaction;
         code 1
         function B();
         code 2
         commit;
      }catch ($e){
         rollBack;
      }
}
function B(){
    try{
         beginTransaction;
         code 3
         commit;
      }catch ($e){
         rollBack;
      }
}
```

疑问：mysql是否支持这种事务嵌套的情况，在遇到这种嵌套情况的时候mysql是如何处理的？

<!-- more -->


##### 导致隐式提交的语句

查找[官方文档](https://dev.mysql.com/doc/refman/8.0/en/implicit-commit.html)发现这样一条说明

> The statements listed in this section (and any synonyms for them) implicitly end any transaction active in the current session, as if you had done a COMMIT before executing the statement.

以下语句的执行相当于执行了一遍commit操作：

* DDL语句
* 隐式使用或修改mysql数据库中表的语句
* 事务控制和锁定语句
.........

可以看到「事务控制和锁定语句」会导致隐式提交，也就是说在B方法的beginTransaction语句时之前的内容就都被commit掉了，那么这个时候数据都已经提交至数据库中了，这样在A方法的code1处的代码如果有数据库操作就无法回滚了，因为已经在B方法中commit了

##### 解决办法：SAVEPOINT和ROLLBACK TO SAVEPOINT

[SAVEPOINT, ROLLBACK TO SAVEPOINT, and RELEASE SAVEPOINT Statements](https://dev.mysql.com/doc/refman/8.0/en/savepoint.html)

> InnoDB supports the SQL statements SAVEPOINT, ROLLBACK TO SAVEPOINT, RELEASE SAVEPOINT and the optional WORK keyword for ROLLBACK.

简而言之就是可以设置一个事务保存点，在提交或者回滚事务的时候也可以指定这个保存点，这样就达成了想要的事务嵌套的效果。

```
function A(){
    try{
         beginTransaction;
         SAVEPOINT p1;
         code 1
         function B();
         code 2
         commit;
      }catch ($e){
         rollBack to p1;
      }
}
```

而实际上我们项目所使用的框架（Laravel）已经替我们处理掉了这部分内容

(Laravel framework ^7.0 相关源码)

```
public function beginTransaction()
    {
        //创建一个事务
        $this->createTransaction();
        //计数器 +1
        $this->transactions++;
        //触发此连接的事件
        $this->fireConnectionEvent('beganTransaction');
    }
protected function createTransaction()
    {
        //如果计数器为0 说明是第一层事务
        if ($this->transactions == 0) {
            $this->reconnectIfMissingConnection();
            //开启事务
            try {
                $this->getPdo()->beginTransaction();
            } catch (Throwable $e) {
                $this->handleBeginTransactionException($e);
            }
        } elseif ($this->transactions >= 1 && $this->queryGrammar->supportsSavepoints()) {
            //如果计数器大于等于 1 且数据库引擎支持SAVEPOINT（innodb）
            //创建一个事务保存点
            $this->createSavepoint();
        }
    }
protected function createSavepoint()
    {
        //计数器+1
        $this->getPdo()->exec(
            $this->queryGrammar->compileSavepoint('trans'.($this->transactions + 1))
        );
    }
public function commit()
    {
        //提交时判断如果=1说明只有一层事务直接提交即可
        if ($this->transactions == 1) {
            $this->getPdo()->commit();
        }
        //如果大于1则取这一层事务提交
        $this->transactions = max(0, $this->transactions - 1);
        //触发committed相关事件
        $this->fireConnectionEvent('committed');
    }
public function rollBack($toLevel = null)
    {
        //直接回滚该层事务或回滚指定level层事务
        $toLevel = is_null($toLevel)
                    ? $this->transactions - 1
                    : $toLevel;
        if ($toLevel < 0 || $toLevel >= $this->transactions) {
            return;
        }
        //回滚
        try {
            $this->performRollBack($toLevel);
        } catch (Throwable $e) {
            $this->handleRollBackException($e);
        }
        //回滚一次更新当前层级
        $this->transactions = $toLevel;
        //触发rollingBack相关事件
        $this->fireConnectionEvent('rollingBack');
    }

```

所以按第一个代码片段的写法也是可以的，不过这样的话回滚逻辑就是B方法接收到异常只回滚B方法的这部分操作，不会影响到code1和code2的操作了。








