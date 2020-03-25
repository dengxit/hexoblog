---
layout: mysql
title: Sql-mode :ONLY_FULL_GROUP_BY
date: 2020-03-20 14:52:26
tags:
 - Mysql
---
##### ONLY_FULL_GROUP_BY 引起的一个问题
```
Column 'XXXX' is invalid in the select list because it is not contained in 
either an aggregate function or the GROUP BY clause.
```
这是由于Mysql的 Sql-mode 设置ONLY_FULL_GROUP_BY导致的，在解决这个问题前首先要了解Mysql的Sql-mode
<!-- more -->
##### Sql-mode 介绍 
* [MySql 5.7 Sql-mode](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sql-mode-changes)
* [MySql Handling of GROUP BY](https://dev.mysql.com/doc/refman/5.7/en/group-by-handling.html)

###### 在文档中我们可以看到
以下的Sql语句在ONLY_FULL_GROUP_BY模式下会出错
````
SELECT name, address, MAX(age) FROM t GROUP BY name;

ERROR 1055 (42000): Expression #2 of SELECT list is not in GROUP
BY clause and contains nonaggregated column 'mydb.t.address' which
is not functionally dependent on columns in GROUP BY clause; this
is incompatible with sql_mode=ONLY_FULL_GROUP_BY
````
原因是 address 没有位于 GROUP BY 列中，也没有使用聚合函数；但是如果 name 是t表的主键或者是唯一的NOT NULL 列，那么这个查询是有效的，因为Mysql可以认识到select所选择的列在功能上取决于group by的列，name若为主键，则它可以确定唯一的对应address，这样就不会产生随机性。


#### 解决办法

* 确定业务逻辑上 name 对应唯一的 address 可以使用 ANY_VALUE

````
SELECT name, ANY_VALUE(address), MAX(age) FROM t GROUP BY name;
````

* 根据业务逻辑可以 为 address 这样的字段 加上聚合函数

* 关闭 ONLY_FULL_GROUP_BY。

````
SELECT @@sql_mode;
SET sql_mode ='xxxxxx,xxxxxx' #去除ONLY_FULL_GROUP_BY
````