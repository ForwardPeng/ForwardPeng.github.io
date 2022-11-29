---
title: SQL-索引
date: 2020-04-16 20:02:11
categories:    
- 计算机基础
- 数据库
tags:
- SQL
- 数据库
toc: true
---
# 索引简介
索引是 MySQL 中一种十分重要的数据库对象。它是数据库性能调优技术的基础，常用于实现数据的快速检索。索引就是根据表中的一列或若干列按照一定顺序建立的列值与记录行之间的对应关系表，实质上是一张描述索引列的列值与原表中记录行之间一一对应关系的有序表。

**1.顺序访问**

顺序访问是在表中实行全表扫描，从头到尾逐行遍历，直到在无序的行数据中找到符合条件的目标数据。这种方式实现比较简单，但是当表中有大量数据的时候，效率非常低下。例如，在几千万条数据中查找少量的数据时，使用顺序访问方式将会遍历所有的数据，花费大量的时间，显然会影响数据库的处理性能。

**2.索引访问**

索引访问是通过遍历索引来直接访问表中记录行的方式。使用这种方式的前提是对表建立一个索引，在列上创建了索引之后，查找数据时可以直接根据该列上的索引找到对应记录行的位置，从而快捷地查找到数据。索引存储了指定列数据值的指针，根据指定的排序顺序对这些指针排序。

## 索引的分类
索引的类型和存储引擎有关，每种存储引擎所支持的索引类型不一定完全相同。根据存储方式的不同，MySQL中常用的索引在物理上分为以下两类。

**B-树索引**

B-树索引又称为 BTREE 索引，目前大部分的索引都是采用 B-树索引来存储的。B-树索引是一个典型的数据结构，其包含的组件主要有以下几个：
* 叶子节点：包含的条目直接指向表里的数据行。叶子节点之间彼此相连，一个叶子节点有一个指向下一个叶子节点的指针。
* 分支节点：包含的条目指向索引里其他的分支节点或者叶子节点。
* 根节点：一个 B-树索引只有一个根节点，实际上就是位于树的最顶端的分支节点。

表中的每一行都会在索引上有一个对应值。因此，在表中进行数据查询时，可以根据索引值一步一步定位到数据所在的行。B-树索引可以进行全键值、键值范围和键值前缀查询，也可以对查询结果进行 ORDER BY排序。但B-树索引必须遵循左边前缀原则，要考虑以下几点约束：
* 查询必须从索引的最左边的列开始。
* 查询不能跳过某一索引列，必须按照从左到右的顺序进行匹配。
* 存储引擎不能使用索引中范围条件右边的列。

**哈希索引**

哈希通过散列算法变换成固定长度的输出，该输出就是散列值。哈希索引也称为散列索引或 HASH 索引。MySQL目前仅有MEMORY存储引擎和HEAP存储引擎支持这类索引。其中，MEMORY存储引擎可以支持B-树索引和HASH索引，且将HASH当成默认索引。

HASH 索引不是基于树形的数据结构查找数据，而是根据索引列对应的哈希值的方法获取表的记录行。哈希索引的最大特点是访问速度快，但也存在下面的一些缺点：
* 需要读取表中索引列的值来参与散列计算，散列计算是一个比较耗时的操作。也就是说，相对于 B- 树索引来说，建立哈希索引会耗费更多的时间。
* 不能使用HASH索引排序
* HASH 索引只支持等值比较，如“=”“IN()”或“<=>”
* HASH索引不支持键的部分匹配，因为在计算HASH值的时候是通过整个索引值来计算的

MySQL中的索引在逻辑上分为以下5 类：

* **普通索引**：普通索引是最基本的索引类型，唯一任务是加快对数据的访问速度，没有任何限制。创建普通索引时，通常使用的关键字是INDEX或 KEY。
* **唯一性索引**：唯一性索引是不允许索引列具有相同索引值的索引。如果能确定某个数据列只包含彼此各不相同的值，在为这个数据列创建索引的时候就应该用关键字 UNIQUE 把它定义为一个唯一性索引。<font color=red>创建唯一性索引的目的往往不是为了提高访问速度，而是为了避免数据出现重复。</font>
* **主键索引**：主键索引是一种唯一性索引，即不允许值重复或者值为空，并且每个表只能有一个主键。主键可以在创建表的时候指定，也可以通过修改表的方式添加，必须指定关键字 PRIMARY KEY。<font color=red>每个表只能有一个主键</font>
* **空间索引**：空间索引主要用于地理空间数据类型 GEOMETRY。
* **全文索引**：全文索引只能在 VARCHAR 或 TEXT 类型的列上创建，并且只能在 MyISAM 表中创建。索引在逻辑上分为以上 5 类，但在实际使用中，索引通常被创建成单列索引和组合索引。单列索引就是索引只包含原表的一个列；组合索引也称为复合索引或多列索引，相对于单列索引来说，组合索引是将原表的多个列共同组成一个索引。

为了提高索引的应用性能，MySQL中的索引可以根据具体应用采用不同的索引策略。这些索引策略所对应的索引类型有聚集索引、次要索引、覆盖索引、复合索引、前缀索引、唯一索引等

## 索引的使用原则和注意事项
虽然索引可以加快查询速度，提高 MySQL 的处理性能，但是过多地使用索引也会造成以下弊端：
* 创建索引和维护索引要耗费时间，这种时间随着数据量的增加而增加。
* 除了数据表占数据空间之外，每一个索引还要占一定的物理空间。如果要建立聚簇索引，那么需要的空间就会更大。 
* 当对表中的数据进行增加、删除和修改的时候，索引也要动态地维护，这样就降低了数据的维护速度。
><font color=red> 索引可以在一些情况下加速查询，但是在某些情况下，会降低效率。</font>

索引只是提高效率的一个因素，因此在建立索引的时候应该遵循以下原则：
* 在经常需要搜索的列上建立索引，可以加快搜索的速度。
* 在作为主键的列上创建索引，强制该列的唯一性，并组织表中数据的排列结构。
* 在经常使用表连接的列上创建索引，这些列主要是一些外键，可以加快表连接的速度。
* 在经常需要根据范围进行搜索的列上创建索引，因为索引已经排序，所以其指定的范围是连续的。
* 在经常需要排序的列上创建索引，因为索引已经排序，所以查询时可以利用索引的排序，加快排序查询
* 在经常使用WHERE子句的列上创建索引，加快条件的判断速度。

在某些应用场合下建立索引不能提高 MySQL的工作效率，甚至降低了数据库的工作效率，一般来说不适合创建索引的环境如下：
* 对于那些在查询中很少使用或参考的列不应该创建索引。因为这些列很少使用到，所以有索引或者无索引并不能提高查询速度。相反，由于增加了索引，反而降低了系统的维护速度，并增大了空间要求。
* 对于那些只有很少数据值的列也不应该创建索引。因为这些列的取值很少，例如人事表的性别列。查询结果集的数据行占了表中数据行的很大比例，增加索引并不能明显加快检索速度。
* 对于那些定义为 TEXT、IMAGE 和 BIT 数据类型的列不应该创建索引。因为这些列的数据量要么相当大，要么取值很少。
* 当修改性能远远大于检索性能时，不应该创建索引。因为修改性能和检索性能是互相矛盾的。当创建索引时，会提高检索性能，降低修改性能。当减少索引时，会提高修改性能，降低检索性能。因此，当修改性能远远大于检索性能时，不应该创建索引。

# 创建索引(CREATE INDEX)
索引的建立对于数据库的高效运行是很重要的，索引可以大大提升SQL的检索速度。
## 基本语法
MySQL提供三种创建索引的方法：

**1.使用CREATE INDEX语句**

使用专门用于创建索引的 CREATE INDEX 语句在一个已有的表上创建索引，但该语句不能创建主键。
>语法格式：CREATE <索引名> ON <表名> (<列名> [<长度>] [ ASC | DESC])

语法说明：
* <索引名>：指定索引名。一个表可以创建多个索引，但每个索引在该表中的名称是唯一的.
* <表名>：指定要创建索引的表名。
* <列名>：指定要创建索引的列名。通常可以考虑将查询语句中在 JOIN 子句和 WHERE 子句里经常出现的列作为索引列。
* <列名>：指定要创建索引的列名。通常可以考虑将查询语句中在 JOIN 子句和 WHERE 子句里经常出现的列作为索引列。
* ASC|DESC：可选项。ASC指定索引按照升序来排列，DESC指定索引按照降序来排列，默认为ASC.

**2.使用CREATE TABLE语句**

索引也可以在创建表（CREATE TABLE）的同时创建。在 CREATE TABLE 语句中添加以下语句。语法格式：
>CONSTRAINT PRIMARY KEY [索引类型] (<列名>,…)

在CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的主键。
>语法格式1：KEY | INDEX [<索引名>] [<索引类型>] (<列名>,…)

在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的索引。
>语法格式2：UNIQUE [ INDEX | KEY] [<索引名>] [<索引类型>] (<列名>,…)

在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的唯一性索引。

>语法格式3：FOREIGN KEY <索引名> <列名>

在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的外键。

**3.使用ALTER TABLE语句**

CREATE INDEX 语句可以在一个已有的表上创建索引，ALTER TABLE 语句也可以在一个已有的表上创建索引。在使用 ALTER TABLE 语句修改表的同时，可以向已有的表添加索引。具体的做法是在 ALTER TABLE 语句中添加以下语法成分的某一项或几项。
>语法格式1：ADD INDEX [<索引名>] [<索引类型>] (<列名>,…)

在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加索引。
>语法格式2：ADD PRIMARY KEY [<索引类型>] (<列名>,…)

在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加主键。
>语法格式3：ADD UNIQUE [ INDEX | KEY] [<索引名>] [<索引类型>] (<列名>,…)

在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加唯一性索引。
>语法格式4：ADD FOREIGN KEY [<索引名>] (<列名>,…)

在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加外键

## 创建一般索引
实例1：创建一个表 tb_stu_info，在该表的 height 字段创建一般索引。输入的 SQL 语句和执行过程如下所示。
```sql
mysql> create table tb_stu_info
    -> (
    -> id INT NOT NULL,
    -> nam CHAR(45) DEFAULT NULL,
    -> dept_id INT DEFAULT NULL\
    -> ,age INT DEFAULT NULL,
    -> height INT DEFAULT NULL,
    -> INDEX(height)
    -> );
Query OK, 0 rows affected (0.30 sec)

mysql> show create table tb_stu_info\G
*************************** 1. row ***************************
       Table: tb_stu_info
Create Table: CREATE TABLE `tb_stu_info` (
  `id` int(11) NOT NULL,
  `nam` char(45) DEFAULT NULL,
  `dept_id` int(11) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `height` int(11) DEFAULT NULL,
  KEY `height` (`height`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```
## 创建唯一索引
实例2：创建一个表 tb_stu_info2，在该表的 id 字段上使用 UNIQUE 关键字创建唯一索引。
```sql
mysql> CREATE TABLE tb_stu_info2
    -> (
    -> id INT NOT NULL,
    -> name CHAR(45) DEFAULT NULL,
    -> dept_id INT DEFAULT NULL,
    -> age INT DEFAULT NULL,
    -> height INT DEFAULT NULL,
    -> UNIQUE INDEX(height)
    -> );
Query OK, 0 rows affected (0.29 sec)

mysql> SHOW CREATE TABLE tb_stu_info2\G
*************************** 1. row ***************************
       Table: tb_stu_info2
Create Table: CREATE TABLE `tb_stu_info2` (
  `id` int(11) NOT NULL,
  `name` char(45) DEFAULT NULL,
  `dept_id` int(11) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `height` int(11) DEFAULT NULL,
  UNIQUE KEY `height` (`height`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```
## 查看索引
查看已创建的索引的情况，可以使用 SHOW INDEX 语句查看表中创建的索引。
>语法格式：SHOW INDEX FROM <表名> [ FROM <数据库名>]

语法说明：
* <表名>：要显示索引的表。
* <数据库名>：要显示的表所在的数据库。
```sql
mysql> SHOW INDEX FROM course FROM mytest;
# 显示数据库mytest的表course的索引情况
```
显示字段说明如下：
* Table：表的名称。
* Non_unique：用于显示该索引是否是唯一索引。若不是唯一索引，则该列的值显示为 1；若是唯一索引，则该列的值显示为 0。
* Key_name：索引的名称。
* Seq_in_index：索引中的列序列号，从 1 开始计数。
* Column_name：列名称。
* Collation：显示列以何种顺序存储在索引中。在 MySQL 中，升序显示值“A”（升序），若显示为 NULL，则表示无分类。
* Cardinality：显示索引中唯一值数目的估计值。基数根据被存储为整数的统计数据计数，所以即使对于小型表，该值也没有必要是精确的。基数越大，当进行联合时，MySQL 使用该索引的机会就越大。
* Sub_part：若列只是被部分编入索引，则为被编入索引的字符的数目。若整列被编入索引，则为 NULL。
* Packed：指示关键字如何被压缩。若没有被压缩，则为 NULL。
Null：用于显示索引列中是否包含 NULL。若列含有 NULL，则显示为 YES。若没有，则该列显示为 NO。
* Index_type：显示索引使用的类型和方法（BTREE、FULLTEXT、HASH、RTREE）。
Comment：显示评注。

实例：使用 SHOW INDEX 语句查看表 tb_stu_info2 的索引信息
```sql
mysql> SHOW INDEX FROM tb_stu_info2\G
*************************** 1. row ***************************
        Table: tb_stu_info2
   Non_unique: 0
     Key_name: height
 Seq_in_index: 1
  Column_name: height
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: BTREE
      Comment: 
Index_comment: 
1 row in set (0.00 sec)
```
# 修改和删除索引(DROP INDEX)
当不再需要索引时，可以使用 DROP INDEX 语句或 ALTER TABLE 语句来对索引进行删除。
**1.使用DROP INDEX语句**
>语法格式：DROP INDEX <索引名> ON <表名>

语法说明：
* <索引名>：要删除的索引名
* <表名>：指定该索引所在的表名

**2.使用ALTER TABLE语句**
根据 ALTER TABLE 语句的语法可知，该语句也可以用于删除索引。具体使用方法是将 ALTER TABLE 语句的语法中部分指定为以下子句中的某一项。
* DROP PRIMARY KEY：表示删除表中的主键。一个表只有一个主键，主键也是一个索引。
* DROP INDEX index_name：表示删除名称为 index_name 的索引。
* DROP FOREIGN KEY fk_symbol：表示删除外键。
  
## 删除索引
实例：删除表tb_stu_info中的索引
```sql
mysql> DROP INDEX height
    -> on tb_stu_info;
Query OK, 0 rows affected (0.20 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show create table tb_stu_info\G
*************************** 1. row ***************************
       Table: tb_stu_info
Create Table: CREATE TABLE `tb_stu_info` (
  `id` int(11) NOT NULL,
  `nam` char(45) DEFAULT NULL,
  `dept_id` int(11) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `height` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```
