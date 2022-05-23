# Database 数据库

- 删除数据慎用 DROP, TRUNCATE 因为不会进 rollback segment，从而无法备份以防万一；
- MYSQL 默认事务隔离级别 RR，ORACLE 默认事务隔离级别 RC；

## MySQL

- 引擎

### Index 索引

索引的优点：快速检索，减少IO（可以加快分组和排序）。

索引的注意点：
- index 本身也是表，会占用空间；
- index 的创建和维护需要时间成本，并且会随着数据量的上升而上升；
- 会影响到 table 的修改效率，因为修改数据也会修改到 index；

__Type__

普通索引 index
唯一索引 unique index
主键索引 primary index
全文索引 fulltext index
组合索引 group index ?

> 如果对数据列 A B C 设置组合索引，那么生效的仅仅会是 ABC, AB, A，对 AC 是不生效的。

__Theory__

常见的实现原理有：B Tree, B+ Tree，具体可以参考[这篇内容](./Java.Collection.md)。

__When and When NOT__

- 经常出现在 where, order by 中的列，可以建立索引；
- 需要排序的列，可以建立索引；
- 存在高并发的列，可以建立组合索引？;

- 经常增删改的列，不要设置索引；
- 有大量重复数据的列，不要设置索引；
- 记录太少的列，也可以暂且不设置索引；

## Locks 数据库锁

- 常见的数据库锁有：行锁，间隙锁和表锁；
- 数据被锁上的现象会导致请求数据进入等待，除非锁被释放；

```sql
-- lock one row
SELECT * FROM table WHERE id = 1 FOR UPDATE;

-- gap lock for id > 100
SELECT * FROM table WHERE id > 100 FOR UPDATE;

-- table lock ?
SELECT * FROM table WHERE id NOT NULL FOR UPDATE;

```

## Transaction 事务

### @Transactional 注解

-  __Isolation__ 可以配置事务隔离级别，默认会采用不同数据库的默认配置；
- __readOnly__ 意味着不可以在方法中进行更新操作，否则报错；
- __rollbackFor__ 指对特定的 Exception 类型进行回滚；
- __Propagation__ 指事务的穿透行为，常见可以配置: REQUIRED, REQUIRED_NEW, NESTED;
  + REQUIRED 开启事务，如果已经开启则沿用当前事务；
  + REQUIRED_NEW 开启新的事务，会与当前已经存在的事务独立开来；
  + NESTED 开启事务或者沿用当前事务，但是只会对内部操作回滚，不影响外部操作；
  + 有的数据库不支持 NESTED，建议使用 REQUIRED_NEW；
 
### Transaction Isolation Policy 数据库事务隔离级别

- 事务的隔离级别和SQL Session 紧密相关，__每个事务可以拥有各自的隔离级别__；
  + 隔离级别还和 Consistent Read, Nonlocking/locking Read 相关；
- __READ UNCOMMITTED(RU)__ 是指一个事务可以读取到 __未提交的数值__；
- __READ COMMITTED(RC)__ 是指一个事务职能读取到 __已经提交的数值__；
- __REPEATABLE READ(RR)__ 是指在一个 Session 中的事务可以 __重复读取到一样的数值__；
- __SERIALIZABLE__ 是指事务之间串行化执行；

#### Scenario Table

| Transaction 1 RR | Transaction 2 RC | Transaction 3 RC | Transaction 4 RU |
|-|-|-|-|
| Read 100 |   Read 100  |   Read 100   |   Read 100 |
|||     -50      |    Read 50    |
|  Read 100  |  Read 50    |    Commit 50     ||
|                  | -30 |                  | Read 20 |
||Rollback to 50| -30 ||
||| Commit 20 | Read 20 |
| -60 | Read 20 | Read 20 | Read 20 ? |
| Rollback to 20 ||||
| Read 20 | Read 20 | Read 20 | Read 20 |

```sql
-- check current transaction isolation level
SELECT @@transaction_ISOLATION;

-- set current session transaction isolation level to RC/RR
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;

SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- start new transaction
START TRANSACTION;

SELECT * FROM Employee;

-- commit and rollback
COMMIT;

ROLLBACK;
```

#### 隔离级别和数据库锁

假设数据库有两个字段 id和 name，其中 id是主键，name是普通字段，一共有 8条记录(id 1-8)。

在 隔离级别是RC 模式下，如果第一个 txn 成功上锁记录，无论是通过id还是name，第二个 txn 都可以正常锁住 其他 id记录，但是无法锁住任何 name记录；

具体来说：
- txn 锁住 id 1，第二个 txn 可以锁住 2/3/4..，但是无法锁住 name (尝试上锁会被block)；
- txn 锁住 id > 6, 第二个 txn 可以锁住 1-6，无法锁住 7/8，无法锁住 name，可以插入新数据；
- txn 锁住 id > 10, 因为这时 第一个 txn 等于没有给任何记录上锁，所以 第二个 txn 可以使用 id/name 锁住任何记录，同时也可以插入新记录；(这也就说明 RC 模式下 没有间隙锁);

txn 锁住 name = "xxx"，第二个 txn 可以锁住 其他 id记录，但是不能锁住 任何 name，同时 第二个 txn 可以插入新记录；

>对于 RC 下的上锁，尽可能使用 id 去做条件限制，搭配 name使用 更加高效，而不容易触碰 block，另外尽可能针对所有查询字段增加 index；
>比如对 name增加了 index之后，就可以通过 where name = "XXX" 来正常上锁，同时只要 name不一样，不同的事务就可以正常上锁，同时不会block了；

在 隔离级别是 RR 模式下，由于存在间隙锁，除了 对 id上锁，对 id 范围上锁会导致间隙锁block，对 name上锁会导致block访问任何记录；

具体来说：
txn 锁住 id = 1, 第二个 txn 依然可以锁住 2/3/4..，但是无法锁住 name；
txn 锁住 id > 6, 第二个 txn 可以锁住 id 1-6，无法锁住 7/8， 无法锁住 name，__无法插入新数据__；
txn 锁住 id > 8，数据只有 8条，第二个 txn 可以通过id/name 锁住任何记录，但是 __无法插入新数据__；

txn 锁住 name，第二个 txn 无法通过任何方式 锁住任何记录，哪怕 name 不能存在，第二个 txn 也无法锁住任何一条记录；

>对于RR下的上锁更加严格，所以必须要对所有查询字段增加 index，以确保不会太容易被block；

## SQL

### SQL 常见语法与注意点

__自交会产生重复记录__

|id|name|
|-|-|
|1|p1|
|2|p2|
|3|p3|
|4|p1|
|5|p2|

Self-Join 自交后：

|id|name|id|name|
|-|-|-|-|
|1|p1|1|p1|
|1|p1|4|p1|
|1|p1|6|p1|
|2|p2|2|p2|
|2|p2|5|p2|
|3|p3|3|p3|
|4|p1|1|p1|
|4|p1|4|p1|
|5|p2|2|p2|
|5|p2|5|p2|

为了避免冲突项，可以增加条件 id1 < id2，这样可以保证右侧id都是较大的；
增加条件 id1 != id2，可以保证 id 都不相同；

__GROUP BY 的 Column可以选中，未 group 的column可以使用 聚合函数__

|id|name|
|-|-|
|1|p1|
|2|p2|
|3|p1|

- GROUP BY name 后，SELECT id 会报错要注意，因为对于相同的 name，id会有多个；
- SELECT name，可以展示唯一的 name，这里就是 p1, p2;
- 对于未选中的column，可以使用聚合函数，比如: 
  + SELECT SUM(id), MIN(id), MAX(id), AVG(id), COUNT(id)；
  
__DELETE 与 JOIN 连用需要明确表名__

DELETE 在和 JOIN 连用的情况下需要明确表名，如 
```sql
DELETE FROM table;

DELETE t1 FROM t1 
INNER JOIN t2 ON ...
```

__不可以在查询的时候进行更新/删除操作__

```sql
-- Incorrect SQL
DELETE FROM table
WHERE id NOT IN (
  SELECT id FROM table
  WHERE ...
);
```

- 以上SQL会报错，因为查询的时候又在进行更新；
- 解决办法就是引入临时/中间表；

```SQL
DELETE FROM table
WHERE id NOT IN (
  -- temp table
  SELECT id FROM (
    SELECT id FROM table
    WHERE ...
  ) as sub
);
```

__WHERE... AND... 是逐条filter(全)满足的，而不是一部分在一部分满足条件__

__Like 的用法是 LIKE %s% or LIKE \_S%__

__DATEDIFF, CASE, IF__

DATE(date1, date2)，用来计算日期间隔，= date1 - date2;

```sql
-- CASE usage
SELECT id, 
(CASE WHEN xxx THEN "xxx"
     WHEN xxx THEN "yyy"
     ELSE "zzz";) as name
FROM table
WHERE ...
;

-- IF() usage
SELECT id, IF(sex = 'F', "woman", "man")
FROM table 
WHERE ...
;
```

IFNULL(name, "default") 类似 IF(condition, true, false)，但是要注意只有在有表内容的时候起作用，否则返回只有表头的空表；


### SQL 优化

- 避免使用 "!=", "<>"，会导致放弃使用索引；
- 避免 NULL判断，会导致放弃索引；
  + 建议设置数据字段默认值，而不用 NULL；

- ? 建议用 EXISTS 来替代 IN 
- ? 建议用 WHERE 来替代 HAVING，HAVING 会检索所有记录；
