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

## SQL 优化

- 避免使用 "!=", "<>"，会导致放弃使用索引；
- 避免 NULL判断，会导致放弃索引；
  + 建议设置数据字段默认值，而不用 NULL；

- ? 建议用 EXISTS 来替代 IN 
- ? 建议用 WHERE 来替代 HAVING，HAVING 会检索所有记录；
