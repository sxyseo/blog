---
title: MySQL
date: 2018-03-11 11:58:00
tags: interview
categories: interview
toc: true
---

<!-- GFM-TOC -->
* [存储引擎](#存储引擎)
    * [1. InnoDB](#1-innodb)
    * [2. MyISAM](#2-myisam)
    * [3. InnoDB 与 MyISAM 的比较](#3-innodb-与-myisam-的比较)
* [数据类型](#数据类型)
    * [1. 整型](#1-整型)
    * [2. 浮点数](#2-浮点数)
    * [3. 字符串](#3-字符串)
    * [4. 时间和日期](#4-时间和日期)
* [索引](#索引)
    * [1. 索引分类](#1-索引分类)
        * [1.1 B-Tree 索引](#11-b-tree-索引)
        * [1.2 哈希索引](#12-哈希索引)
        * [1.3. 空间索引（R-Tree）](#13-空间索引r-tree)
        * [1.4 全文索引](#14-全文索引)
    * [2. 索引的优点](#2-索引的优点)
    * [3. 索引优化](#3-索引优化)
        * [3.1 独立的列](#31-独立的列)
        * [3.2 前缀索引](#32-前缀索引)
        * [3.3 多列索引](#33-多列索引)
        * [3.4 索引列的顺序](#34-索引列的顺序)
        * [3.5 聚簇索引](#35-聚簇索引)
        * [3.6 覆盖索引](#36-覆盖索引)
    * [4. B-Tree 和 B+Tree 原理](#4-b-tree-和-b+tree-原理)
        * [4. 1 B-Tree](#4-1-b-tree)
        * [4.2 B+Tree](#42-b+tree)
        * [4.3 带有顺序访问指针的 B+Tree](#43-带有顺序访问指针的-b+tree)
        * [4.4 为什么使用 B-Tree 和 B+Tree](#44-为什么使用-b-tree-和-b+tree)
* [查询性能优化](#查询性能优化)
    * [1. Explain](#1-explain)
    * [2. 减少返回的列](#2-减少返回的列)
    * [3. 减少返回的行](#3-减少返回的行)
    * [4. 拆分大的 DELETE 或 INSERT 语句](#4-拆分大的-delete-或-insert-语句)
* [分库与分表](#分库与分表)
* [故障转移和故障恢复](#故障转移和故障恢复)
    * [1. 故障转移](#1-故障转移)
    * [2. 故障恢复](#2-故障恢复)
* [参考资料](#参考资料)
<!-- GFM-TOC -->


# 存储引擎

## 1. InnoDB

InnoDB 是 MySQL 默认的事务型存储引擎，只有在需要 InnoDB 不支持的特性时，才考虑使用其它存储引擎。

采用 MVCC 来支持高并发，并且实现了四个标准的隔离级别，默认级别是可重复读。

表是基于聚簇索引建立的，它对主键的查询性能有很高的提升。

内部做了很多优化，包括从磁盘读取数据时采用的可预测性读、能够自动在内存中创建哈希索引以加速读操作的自适应哈希索引、能够加速插入操作的插入缓冲区等。

通过一些机制和工具支持真正的热备份。

<!-- more -->

## 2. MyISAM

MyISAM 提供了大量的特性，包括全文索引、压缩、空间函数（GIS）等。但 MyISAM 不支持事务和行级锁，而且崩溃后无法安全恢复。

只能对整张表加锁，而不是针对行。

可以手工或者自动执行检查和修复操作，但是和事务恢复以及崩溃恢复不同，可能导致一些数据丢失，而且修复操作是非常慢的。

可以包含动态或者静态的行。

如果指定了 DELAY_KEY_WRITE 选项，在每次修改执行完成时，不会立即将修改的索引数据写入磁盘，而是会写到内存中的键缓冲区，只有在清理键缓冲区或者关闭表的时候才会将对应的索引块写入磁盘。这种方式可以极大的提升写入性能，但是在数据库或者主机崩溃时会造成索引损坏，需要执行修复操作。

如果表在创建并导入数据以后，不会再进行修改操作，那么这样的表适合采用 MyISAM 压缩表。

对于只读数据，或者表比较小、可以容忍修复操作，则依然可以继续使用 MyISAM。

MyISAM 设计简单，数据以紧密格式存储，所以在某些场景下性能很好。

## 3. InnoDB 与 MyISAM 的比较

**事务** 

InnoDB 是事务型的。

**备份** 

InnoDB 支持在线热备份。

**崩溃恢复** 

MyISAM 崩溃后发生损坏的概率比 InnoDB 高很多，而且恢复的速度也更慢。

**并发** 

MyISAM 只支持表级锁，而 InnoDB 还支持行级锁。

**其它特性** 

MyISAM 支持全文索引，地理空间索引。

# 数据类型

## 1. 整型

TINYINT, SMALLINT, MEDIUMINT, INT, BIGINT 分别使用 8, 16, 24, 32, 64 位存储空间，一般情况下越小的列越好。

INT(11) 中的数字只是规定了交互工具显示字符的个数，对于存储和计算来说是没有意义的。

## 2. 浮点数

FLOAT 和 DOUBLE 为浮点类型，DECIMAL 为高精度小数类型。CPU 原生支持浮点运算，但是不支持 DECIMAl 类型的计算，因此 DECIMAL 的计算比浮点类型需要更高的代价。

FLOAT、DOUBLE 和 DECIMAL 都可以指定列宽，例如 DECIMAL(18, 9) 表示总共 18 位，取 9 位存储小数部分，剩下 9 位存储整数部分。

## 3. 字符串

主要有 CHAR 和 VARCHAR 两种类型，一种是定长的，一种是变长的。

VARCHAR 这种变长类型能够节省空间，因为只需要存储必要的内容。但是在执行 UPDATE 时可能会使行变得比原来长，当超出一个页所能容纳的大小时，就要执行额外的操作。MyISAM 会将行拆成不同的片段存储，而 InnoDB 则需要分裂页来使行放进页内。

VARCHAR 会保留字符串末尾的空格，而 CHAR 会删除。

## 4. 时间和日期

MySQL 提供了两种相似的日期时间类型：DATATIME 和 TIMESTAMP。

**DATATIME** 

能够保存从 1001 年到 9999 年的日期和时间，精度为秒，使用 8 字节的存储空间。

它与时区无关。

默认情况下，MySQL 以一种可排序的、无歧义的格式显示 DATATIME 值，例如“2008-01-16 22:37:08”，这是 ANSI 标准定义的日期和时间表示方法。

**TIMESTAMP** 

和 UNIX 时间戳相同，保存从 1970 年 1 月 1 日午夜（格林威治时间）以来的秒数，使用 4 个字节，只能表示从 1970 年 到 2038 年。

它和时区有关。

MySQL 提供了 FROM_UNIXTIME() 函数把 UNIX 时间戳转换为日期，并提供了 UNIX_TIMESTAMP() 函数把日期转换为 UNIX 时间戳。

默认情况下，如果插入时没有指定 TIMESTAMP 列的值，会将这个值设置为当前时间。

应该尽量使用 TIMESTAMP，因为它比 DATETIME 空间效率更高。

# 索引

索引是在存储引擎层实现的，而不是在服务器层实现的，所以不同存储引擎具有不同的索引类型和实现。

索引能够轻易将查询性能提升几个数量级。

对于非常小的表、大部分情况下简单的全表扫描比建立索引更高效。对于中到大型的表，索引就非常有效。但是对于特大型的表，建立和使用索引的代价将会随之增长。这种情况下，需要用到一种技术可以直接区分出需要查询的一组数据，而不是一条记录一条记录地匹配，例如可以使用分区技术。

## 1. 索引分类

### 1.1 B-Tree 索引

B-Tree 索引是大多数 MySQL 存储引擎的默认索引类型。

因为不再需要进行全表扫描，只需要对树进行搜索即可，因此查找速度快很多。

可以指定多个列作为索引列，多个索引列共同组成键。B-Tree 索引适用于全键值、键值范围和键前缀查找，其中键前缀查找只适用于最左前缀查找。

除了用于查找，还可以用于排序和分组。

如果不是按照索引列的顺序进行查找，则无法使用索引。

### 1.2 哈希索引

基于哈希表实现，优点是查找非常快。

在 MySQL 中只有 Memory 引擎显式支持哈希索引。

InnoDB 引擎有一个特殊的功能叫“自适应哈希索引”，当某个索引值被使用的非常频繁时，会在 B-Tree 索引之上再创建一个哈希索引，这样就让 B-Tree 索引具有哈希索引的一些优点，比如快速的哈希查找。

限制：哈希索引只包含哈希值和行指针，而不存储字段值，所以不能使用索引中的值来避免读取行。不过，访问内存中的行的速度很快，所以大部分情况下这一点对性能影响并不明显；无法用于分组与排序；只支持精确查找，无法用于部分查找和范围查找；如果哈希冲突很多，查找速度会变得很慢。

### 1.3. 空间索引（R-Tree）

MyISAM 存储引擎支持空间索引，可以用于地理数据存储。

空间索引会从所有维度来索引数据，可以有效地使用任意维度来进行组合查询。

### 1.4 全文索引

MyISAM 存储引擎支持全文索引，用于查找文本中的关键词，而不是直接比较索引中的值。

使用 MATCH AGAINST，而不是普通的 WHERE。

## 2. 索引的优点

- 大大减少了服务器需要扫描的数据量；

- 帮助服务器避免进行排序和创建临时表；

- 将随机 I/O 变为顺序 I/O。

## 3. 索引优化

### 3.1 独立的列

在进行查询时，索引列不能是表达式的一部分，也不能是函数的参数，否则无法使用索引。

例如下面的查询不能使用 actor_id 列的索引：

```sql
SELECT actor_id FROM sakila.actor WHERE actor_id + 1 = 5;
```

### 3.2 前缀索引

对于 BLOB、TEXT 和 VARCHAR 类型的列，必须使用前缀索引，只索引开始的部分字符。

对于前缀长度的选取需要根据  **索引选择性**  来确定：不重复的索引值和记录总数的比值。选择性越高，查询效率也越高。最大值为 1 ，此时每个记录都有唯一的索引与其对应。

### 3.3 多列索引

在需要使用多个列作为条件进行查询时，使用多列索引比使用多个单列索引性能更好。例如下面的语句中，最好把 actor_id 和 file_id 设置为多列索引。

```sql
SELECT file_id, actor_ id FROM sakila.film_actor
WhERE actor_id = 1 OR film_id = 1;
```

### 3.4 索引列的顺序

让选择性最强的索引列放在前面，例如下面显示的结果中 customer_id 的选择性比 staff_id 更高，因此最好把 customer_id 列放在多列索引的前面。

```sql
SELECT COUNT(DISTINCT staff_id)/COUNT(*) AS staff_id_selectivity,
COUNT(DISTINCT customer_id)/COUNT(*) AS customer_id_selectivity,
COUNT(*)
FROM payment;
```

```html
   staff_id_selectivity: 0.0001
customer_id_selectivity: 0.0373
               COUNT(*): 16049
```

### 3.5 聚簇索引

<br><div align="center"> <img src="../pics/b9e9ae8c-e216-4c01-b267-a50dbeb98fa4.jpg"/> </div><br>

聚簇索引并不是一种索引类型，而是一种数据存储方式。

术语“聚簇”表示数据行和相邻的键值紧密地存储在一起，InnoDB 的聚簇索引的数据行存放在 B-Tree 的叶子页中。

因为无法把数据行存放在两个不同的地方，所以一个表只能有一个聚簇索引。

**优点** 

1. 可以把相关数据保存在一起，减少 I/O 操作；
2. 因为数据保存在 B-Tree 中，因此数据访问更快。

**缺点** 

1. 聚簇索引最大限度提高了 I/O 密集型应用的性能，但是如果数据全部放在内存，就没必要用聚簇索引。
2. 插入速度严重依赖于插入顺序，按主键的顺序插入是最快的。
3. 更新操作代价很高，因为每个被更新的行都会移动到新的位置。
4. 当插入到某个已满的页中，存储引擎会将该页分裂成两个页面来容纳该行，页分裂会导致表占用更多的磁盘空间。
5. 如果行比较稀疏，或者由于页分裂导致数据存储不连续时，聚簇索引可能导致全表扫描速度变慢。

### 3.6 覆盖索引

索引包含所有需要查询的字段的值。

## 4. B-Tree 和 B+Tree 原理

### 4. 1 B-Tree

<br><div align="center"> <img src="../pics/5ed71283-a070-4b21-85ae-f2cbfd6ba6e1.jpg"/> </div><br>

为了描述 B-Tree，首先定义一条数据记录为一个二元组 [key, data]，key 为记录的键，data 为数据记录除 key 外的数据。

B-Tree 是满足下列条件的数据结构：

- 所有叶节点具有相同的深度，也就是说 B-Tree 是平衡的；
- 一个节点中的 key 从左到右非递减排列；
- 如果某个指针的左右相邻 key 分别是 key<sub>i</sub> 和 key<sub>i+1</sub>，且不为 null，则该指针指向节点的所有 key 大于 key<sub>i</sub> 且小于 key<sub>i+1</sub>。

在 B-Tree 中按 key 检索数据的算法非常直观：首先从根节点进行二分查找，如果找到则返回对应节点的 data，否则对相应区间的指针指向的节点递归进行查找，直到找到节点或找到 null 指针，前者查找成功，后者查找失败。

由于插入删除新的数据记录会破坏 B-Tree 的性质，因此在插入删除时，需要对树进行一个分裂、合并、转移等操作以保持 B-Tree 性质。

### 4.2 B+Tree

<br><div align="center"> <img src="../pics/63cd5b50-d6d8-4df6-8912-ef4a1dd5ba13.jpg"/> </div><br>

与 B-Tree 相比，B+Tree 有以下不同点：

- 每个节点的指针上限为 2d 而不是 2d+1；
- 内节点不存储 data，只存储 key，叶子节点不存储指针。

### 4.3 带有顺序访问指针的 B+Tree

<br><div align="center"> <img src="../pics/1ee5f0a5-b8df-43b9-95ab-c516c54ec797.jpg"/> </div><br>

一般在数据库系统或文件系统中使用的 B+Tree 结构都在经典 B+Tree 基础上进行了优化，在叶子节点增加了顺序访问指针，做这个优化的目的是为了提高区间访问的性能。

### 4.4 为什么使用 B-Tree 和 B+Tree

红黑树等数据结构也可以用来实现索引，但是文件系统及数据库系统普遍采用 B-/+Tree 作为索引结构。

页是计算机管理存储器的逻辑块，硬件及操作系统往往将主存和磁盘存储区分割为连续的大小相等的块，每个存储块称为一页（在许多操作系统中，页得大小通常为 4k），主存和磁盘以页为单位交换数据。

一般来说，索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储的磁盘上。为了减少磁盘 I/O，磁盘往往不是严格按需读取，而是每次都会预读。这样做的理论依据是计算机科学中著名的局部性原理：当一个数据被用到时，其附近的数据也通常会马上被使用。数据库系统的设计者巧妙利用了磁盘预读原理，将一个节点的大小设为等于一个页，这样每个节点只需要一次 I/O 就可以完全载入。B-Tree 中一次检索最多需要 h-1 次 I/O（根节点常驻内存），渐进复杂度为 O(h)=O(logdN)。一般实际应用中，出度 d 是非常大的数字，通常超过 100，因此 h 非常小（通常不超过 3）。而红黑树这种结构，h 明显要深的多。并且于逻辑上很近的节点（父子）物理上可能很远，无法利用局部性，效率明显比 B-Tree 差很多。

B+Tree 更适合外存索引，原因和内节点出度 d 有关。由于 B+Tree 内节点去掉了 data 域，因此可以拥有更大的出度，拥有更好的性能。

# 查询性能优化

## 1. Explain

用来分析 SQL 语句，分析结果中比较重要的字段有：

- select_type : 查询类型，有简单查询、联合查询和子查询

- key : 使用的索引

- rows : 扫描的行数

## 2. 减少返回的列

慢查询主要是因为访问了过多数据，除了访问过多行之外，也包括访问过多列。

最好不要使用 SELECT * 语句，要根据需要选择查询的列。

## 3. 减少返回的行

最好使用 LIMIT 语句来取出想要的那些行。

还可以建立索引来减少条件语句的全表扫描。例如对于下面的语句，不适用索引的情况下需要进行全表扫描，而使用索引只需要扫描几行记录即可，使用 Explain 语句可以通过观察 rows 字段来看出这种差异。

```sql
SELECT * FROM sakila.film_actor WHERE film_id = 1;
```

## 4. 拆分大的 DELETE 或 INSERT 语句

如果一次性执行的话，可能一次锁住很多数据、占满整个事务日志、耗尽系统资源、阻塞很多小的但重要的查询。

```sql
DELEFT FROM messages WHERE create < DATE_SUB(NOW(), INTERVAL 3 MONTH);
```
```sql
rows_affected = 0
do {
    rows_affected = do_query(
    "DELETE FROM messages WHERE create  < DATE_SUB(NOW(), INTERVAL 3 MONTH) LIMIT 10000")
} while rows_affected > 0
```

# 分库与分表

**1. 分表与分区的不同** 

分表，就是讲一张表分成多个小表，这些小表拥有不同的表名；而分区是将一张表的数据分为多个区块，这些区块可以存储在同一个磁盘上，也可以存储在不同的磁盘上，这种方式下表仍然只有一个。

**2. 使用分库与分表的原因** 

随着时间和业务的发展，数据库中的表会越来越多，并且表中的数据量也会越来越大，那么读写操作的开销也会随着增大。

**3. 垂直切分** 

将表按功能模块、关系密切程度划分出来，部署到不同的库上。例如，我们会建立商品数据库 payDB、用户数据库 userDB 等，分别用来存储项目与商品有关的表和与用户有关的表。

**4. 水平切分** 

把表中的数据按照某种规则存储到多个结构相同的表中，例如按 id 的散列值、性别等进行划分，

**5. 垂直切分与水平切分的选择** 

如果数据库中的表太多，并且项目各项业务逻辑清晰，那么垂直切分是首选。

如果数据库的表不多，但是单表的数据量很大，应该选择水平切分。

**6. 水平切分的实现方式** 

最简单的是使用 merge 存储引擎。

**7. 分库与分表存在的问题** 

(1) 事务问题

在执行分库分表之后，由于数据存储到了不同的库上，数据库事务管理出现了困难。如果依赖数据库本身的分布式事务管理功能去执行事务，将付出高昂的性能代价；如果由应用程序去协助控制，形成程序逻辑上的事务，又会造成编程方面的负担。

(2) 跨库跨表连接问题

在执行了分库分表之后，难以避免会将原本逻辑关联性很强的数据划分到不同的表、不同的库上。这时，表的连接操作将受到限制，我们无法连接位于不同分库的表，也无法连接分表粒度不同的表，导致原本只需要一次查询就能够完成的业务需要进行多次才能完成。

# 故障转移和故障恢复

故障转移也叫做切换，当主库出现故障时就切换到备库，使备库成为主库。故障恢复顾名思义就是从故障中恢复过来，并且保证数据的正确性。

## 1. 故障转移

**1.1 提升备库或切换角色** 

提升一台备库为主库，或者在一个主-主复制结构中调整主动和被动角色。

**1.2 虚拟 IP 地址和 IP 托管** 

为 MySQL 实例指定一个逻辑 IP 地址，当 MySQL 实例失效时，可以将 IP 地址转移到另一台 MySQL 服务器上。

**1.3 中间件解决方案** 

通过代理，可以路由流量到可以使用的服务器上。

<br><div align="center"> <img src="../pics/fabd5fa0-b75e-48d0-9e2c-31471945ceb9.jpg"/> </div><br>

**1.4 在应用中处理故障转移** 

将故障转移整合到应用中可能导致应用变得太过笨拙。

## 2. 故障恢复


# 参考资料

- 高性能 MySQL
- [MySQL 索引背后的数据结构及算法原理 ](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)
- [MySQL 索引优化全攻略 ](http://www.runoob.com/w3cnote/mysql-index.html)
- [20+ 条 MySQL 性能优化的最佳经验 ](https://www.jfox.info/20-tiao-mysql-xing-nen-you-hua-de-zui-jia-jing-yan.html)

---

本来来自 [Interview-Notebook](https://github.com/CyC2018/Interview-Notebook/)

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/3.0/cn/88x31.png" /></a>

本作品采用 <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/">知识共享署名-非商业性使用-相同方式共享 3.0 中国大陆许可协议</a> 进行许可。