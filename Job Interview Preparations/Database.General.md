# Database 数据库

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
组合索引 group?? index

> 如果对数据列 A B C 设置组合索引，那么生效的仅仅会是 ABC, AB, A，对 AC 是不生效的。

__Theory__

常见的实现原理有：B Tree, B+ Tree.

???

__When and When NOT__

- 经常出现在 where, order by 中的列，可以建立索引；
- 需要排序的列，可以建立索引；
- 存在高并发的列，可以建立组合索引？;

- 经常增删改的列，不要设置索引；
- 有大量重复数据的列，不要设置索引；
- 记录太少的列，也可以暂且不设置索引；