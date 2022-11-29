---
title: SQL-数据定义语言(DDL)
date: 2020-04-16 10:21:26
categories:    
- 计算机基础
- 数据库
tags:
- SQL
- 数据库
toc: true
---
# 数据库
## **CREATE DATABASE**-创建新数据库
>语法格式：CREATE DATABASE [IF NOT EXISTS] <数据库名>
[[DEFAULT] CHARACTER SET <字符集名>] 
[[DEFAULT] COLLATE <校对规则名>];

[]中的内容是可选的。语法说明如下：
* <数据库名>：创建数据库的名称。MySQL 的数据存储区将以目录方式表示 MySQL 数据库，因此数据库名称必须符合操作系统的文件夹命名规则，不能以数字开头，尽量要有实际意义。注意在 MySQL 中不区分大小写。
* IF NOT EXISTS：在创建数据库之前进行判断，只有该数据库目前尚不存在时才能执行操作。此选项可以用来避免数据库已经存在而重复创建的错误。
* [DEFAULT] CHARACTER SET：指定数据库的字符集。指定字符集的目的是为了避免在数据库中存储的数据出现乱码的情况。如果在创建数据库时不指定字符集，那么就使用系统的默认字符集。
* [DEFAULT] COLLATE：指定字符集的默认校对规则。

创建数据库的基本示例：
```sql
mysql> CREATE DATABASE IF NOT EXISTS test_db_char
    -> DEFAULT CHARACTER SET utf8
    -> DEFAULT COLLATE utf8_unicode_ci;
Query OK, 1 row affected (0.03 sec)
```
## **ALTER DATABASE**-修改数据库
>语法格式：ALTER DATABASE [数据库名] { 
[ DEFAULT ] CHARACTER SET <字符集名> |
[ DEFAULT ] COLLATE <校对规则名>}

语法说明如下：
* ALTER DATABASE 用于更改数据库的全局特性。
* 使用 ALTER DATABASE 需要获得数据库 ALTER 权限。
* 数据库名称可以忽略，此时语句对应于默认数据库。
* CHARACTER SET 子句用于更改默认的数据库字符集。

修改数据库的示例如下：
```sql
mysql> ALTER CREATE test_db_char
  -> DEFAULT CHARACTER SET utf8
  -> DEFAULT COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)
```
## **SHOW DATABASE**-查看数据库
>语法格式：SHOW DATABASES [LIKE '数据库名'];

语法说明如下：
* LIKE 从句是可选项，用于匹配指定的数据库名称。LIKE 从句可以部分匹配，也可以完全匹配。
* 数据库名使用单引号' '。

查看数据库示例如下：
```sql
mysql> SHOW DATABSES LIKE '%test%';
+-------------------+
| Database (%test%) |
+-------------------+
| test              |
| test_db_char      |
+-------------------+
2 rows in set (0.00 sec)
```

## **DROP DATABASE**-删除数据库
>语法格式：DROP DATABASE [ IF EXISTS ] <数据库名>

语法说明如下：
* <数据库名>：指定要删除的数据库名。
* IF EXISTS：用于防止当数据库不存在时发生错误。
* DROP DATABASE：删除数据库中的所有表格并同时删除数据库。使用此语句时要非常小心，以免错误删除。如果要使用 DROP DATABASE，需要获得数据库 DROP 权限。
```sql
mysql> DROP DATABASE test;
Query OK, 0 rows affected (0.19 sec)
```
# 数据表
## **CREATE TABLE**-创建新数据库表
>语法格式：CREATE TABLE <表名> ([表定义选项])[表选项][分区选项];

语法说明如下：
* CREATE TABLE：用于创建给定名称的表，必须拥有表CREATE的权限。
* <表名>：指定要创建表的名称，在 CREATE TABLE 之后给出，必须符合标识符命名规则。表名称被指定为 db_name.tbl_name，以便在特定的数据库中创建表。无论是否有当前数据库，都可以通过这种方式创建。在当前数据库中创建表时，可以省略 db-name。如果使用加引号的识别名，则应对数据库和表名称分别加引号。例如，'mydb'.'mytbl' 是合法的，但 'mydb.mytbl' 不合法。
* <表定义选项>：表创建定义，由列名（col_name）、列的定义（column_definition）以及可能的空值说明、完整性约束或表索引组成。
* 默认的情况是，表被创建到当前的数据库中。若表已存在、没有当前数据库或者数据库不存在，则会出现错误。
```sql
mysql> CREATE TABLE tb_test
  -> (
  -> id INT(11),
  -> name VARCHAR(25),
  -> deptId INT(11),
  -> salary FLOAT
  ->);
Query OK, 0 rows affected（0.37 sec）
```
>DESCRIBE(DESC)<表名>; 用于查看表的字段信息
```sql
mysql> DESCRIBE join_test1;
+-------+-----------+------+-----+---------+-------+
| Field | Type      | Null | Key | Default | Extra |
+-------+-----------+------+-----+---------+-------+
| id    | int(11)   | YES  |     | NULL    |       |
| name  | char(255) | YES  |     | NULL    |       |
+-------+-----------+------+-----+---------+-------+
2 rows in set (0.06 sec)
```
>SHOW CREATE TABLE<表名>\G;
```sql
mysql> show create table join_test1\G
*************************** 1. row ***************************
       Table: join_test1
Create Table: CREATE TABLE `join_test1` (
  `id` int(11) DEFAULT NULL,
  `name` char(255) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```

## **ALTER TABLE**-修改数据库表
>语法格式：ALTER TABLE <表名> [修改选项]

修改选项的语法格式如下：
>{ ADD COLUMN <列名> <类型>
| CHANGE COLUMN <旧列名> <新列名> <新列类型>
| ALTER COLUMN <列名> { SET DEFAULT <默认值> | DROP DEFAULT }
| MODIFY COLUMN <列名> <类型>
| DROP COLUMN <列名>
| RENAME TO <新表名> }

### 添加字段的语法格式如下：
>ALTER TABLE <表名> ADD <新字段名> <数据类型> [约束条件] [FIRST|AFTER 已存在的字段名];  #FIRST|AFTER用于指定新字段在表中的位置。

添加字段的使用示例：
```sql
mysql> ALTER TABLE test ADD COLUMN col2 INT FIRST;
Query OK, 0 rows affected (0.41 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc test;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| col2  | int(11)     | YES  |     | NULL    |       |
| col1  | int(11)     | YES  |     | NULL    |       |
| id    | int(11)     | YES  |     | NULL    |       |
| name  | varchar(25) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```

### 修改字段的数据类型
>语法格式：ALTER TABLE <表名> MODIFY <字段名> <数据类型>

其中，**表名**指要修改数据类型的字段所在表的名称，**字段名**指需要修改的字段，**数据类型**指修改后字段的新数据类型。

修改字段的使用示例：
```sql
mysql> ALTER TABLE test
    -> MODIFY name varchar(30);
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC test;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| col2  | int(11)     | YES  |     | NULL    |       |
| col1  | int(11)     | YES  |     | NULL    |       |
| id    | int(11)     | YES  |     | NULL    |       |
| name  | varchar(30) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```
### 删除字段
>语法格式：ALTER TABLE <表名> DROP <字段名>；

删除字段的使用示例如下：
```sql
mysql> ALTER TABLE test DROP col1;
Query OK, 0 rows affected (0.45 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC test;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | YES  |     | NULL    |       |
| name  | varchar(30) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```
### 修改字段名称
>ALTER TABLE <表名> CHANGE <旧字段名> <新字段名> <新数据类型>；

其中，**旧字段名**指修改前的字段名；**新字段名**指修改后的字段名；**新数据类型**指修改后的数据类型，如果不需要修改字段的数据类型，可以将新数据类型设置成与原来一样，但数据类型不能为空。

修改字段名称的使用示例：
```sql
mysql> ALTER TABLE test
    -> CHANGE col1 col3 CHAR(50);
Query OK, 0 rows affected (0.81 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC test;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| col3  | char(50)    | YES  |     | NULL    |       |
| id    | int(11)     | YES  |     | NULL    |       |
| name  | varchar(30) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
3 rows in set (0.01 sec)
```
### 修改表名
>语法格式：ALTER TABLE <旧表名> RENAME [TO] <新表名>；

修改表名使用示例：
```sql
mysql> ALTER TABLE test
    -> RENAME TO test_re;
Query OK, 0 rows affected (0.13 sec)

mysql> show tables;
+----------------+
| Tables_in_test |
+----------------+
| join_test1     |
| test_re        |
+----------------+
2 rows in set (0.00 sec)
```

## **DROP TABLE**-删除表
>基本语法：DROP TABLE [IF EXISTS] 表名1 [ ,表名2, 表名3 ...]

语法格式的说明：
* 表名1, 表名2, 表名3 ...表示要被删除的数据表的名称。DROP TABLE 可以同时删除多个表，只要将表名依次写在后面，相互之间用逗号隔开即可。
* IF EXISTS 用于在删除数据表之前判断该表是否存在。如果不加 IF EXISTS，当数据表不存在时 MySQL 将提示错误，中断 SQL 语句的执行；加上 IF EXISTS 后，当数据表不存在时 SQL 语句可以顺利执行，但是会发出警告（warning）。
* 用户必须拥有执行DROP的权限，否则无法删除。表删除后，用户在该表上的权限不会自动删除

删除表的使用示例：
```sql
mysql> DROP TABLE test_re;
Query OK, 0 rows affected (0.15 sec)
```
**CREATE INDEX**-创建索引、**DROP INDEX**-删除索引。见[SQL-索引](https://forwardpeng.github.io/2020/04/16/SQL-%E7%B4%A2%E5%BC%95/)

