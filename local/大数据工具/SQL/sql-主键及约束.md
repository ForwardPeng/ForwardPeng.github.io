---
title: SQL-主键及约束
date: 2020-04-21 13:14:36
categories:    
- 计算机基础
- 数据库
tags:
- SQL
- 数据库
toc: true
---
# 主键(PRIMARY KEY)
主键约束是一个列或者列的组合，其值能唯一地标识表中的每一行。这样的一列或多列称为表的主键，通过它可以强制表的实体完整性。

## 选取设置主键约束的字段
主键约束即在表中定义一个主键来唯一确定表中每一行数据的标识符。主键可以是表中的某一列或者多列的组合，其中由多列组合的主键称为复合主键。主键应该遵守下面的规则：
* 每个表只能定义一个主键。
* 主键值必须唯一标识表中的每一行，且不能为 NULL，即表中不可能存在两行数据有相同的主键值。这是唯一性原则。
* 一个列名只能在复合主键列表中出现一次。
* 复合主键不能包含不必要的多余列。当把复合主键的某一列删除后，如果剩下的列构成的主键仍然满足唯一性原则，那么这个复合主键是不正确的。这是最小化原则。

## 在创建表时设置主键约束
在 CREATE TABLE 语句中，主键是通过 PRIMARY KEY 关键字来指定的。
>语法规则：<字段名> <数据类型> PRIMARY KEY [默认值]

实例1：在 test_db 数据库中创建 tb_emp 3 数据表，其主键为 id
```sql
mysql> CREATE TABLE tb_emp3
    -> (
    -> id INT(11) PRIMARY KEY,
    -> name VARCHAR(25),
    -> deptId INT(11),
    -> salary FLOAT
    -> );
Query OK, 0 rows affected (0.37 sec)

mysql> DESC tb_emp3
    -> ;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | NO   | PRI | NULL    |       |
| name   | varchar(25) | YES  |     | NULL    |       |
| deptId | int(11)     | YES  |     | NULL    |       |
| salary | float       | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```
>指定主键的语法格式：[CONSTRAINT <约束名>] PRIMARY KEY [字段名]

实例2：在 test_db 数据库中创建 tb_emp 4 数据表，其主键为 id
```sql
mysql> CREATE TABLE tb_emp4
    -> (
    -> id INT(11),
    -> name VARCHAR(25),
    -> deptId INT(11),
    -> salary FLOAT,
    -> PRIMARY KEY(id)
    -> );
Query OK, 0 rows affected (0.34 sec)

mysql> DESC tb_emp4;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | NO   | PRI | NULL    |       |
| name   | varchar(25) | YES  |     | NULL    |       |
| deptId | int(11)     | YES  |     | NULL    |       |
| salary | float       | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```
## 在创建表时设置复合主键
主键由多个字段联合组成，语法规则如下：
>PRIMARY KEY [字段1，字段2，…,字段n]

实例3：创建数据表 tb_emp5，假设表中没有主键 id，为了唯一确定一个员工，可以把 name、deptId 联合起来作为主键。
```sql
mysql> CREATE TABLE tb_emp5 ( name VARCHAR(25), deptId INT(11), salary FLOAT, PRIMARY KEY(name, deptId) );
Query OK, 0 rows affected (0.26 sec)

mysql> DESC tb_emp5;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| name   | varchar(25) | NO   | PRI | NULL    |       |
| deptId | int(11)     | NO   | PRI | NULL    |       |
| salary | float       | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```
## 在修改表时添加主键约束
修改数据表时添加主键约束的语法规则为：
>ALTER TABLE <数据表名> ADD PRIMARY KEY(<列名>);

实例4：修改数据表 tb_emp2，将字段 id 设置为主键。
```sql
mysql> ALTER TABLE tb_emp3 ADD PRIMARY KEY(id);
Query OK, 0 rows affected (0.41 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC tb_emp3;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | NO   | PRI | NULL    |       |
| name   | varchar(25) | YES  |     | NULL    |       |
| deptId | int(11)     | YES  |     | NULL    |       |
| salary | float       | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```

# 外键约束(FOREIGN KEY)
外键约束（FOREIGN KEY）用来在两个表的数据之间建立链接，它可以是一列或者多列。一个表可以有一个或多个外键。外键对应的是参照完整性，一个表的外键可以为空值，若不为空值，则每一个外键的值必须等于另一个表中主键的某个值。

外键是表的一个字段，不是本表的主键，但对应另一个表的主键。定义外键后，不允许删除另一个表中具有关联关系的行。外键的主要作用是保持数据的一致性、完整性。例如，部门表 tb_dept 的主键是 id，在员工表 tb_emp5 中有一个键 deptId 与这个 id 关联。
* 主表（父表）：对于两个具有关联关系的表而言，相关联字段中主键所在的表就是主表。
* 从表（子表）：对于两个具有关联关系的表而言，相关联字段中外键所在的表就是从表。

## 选取设置MySQL外键约束的字段
定义一个外键时，需要遵守下列规则:
* 父表必须已经存在于数据库中，或者是当前正在创建的表。如果是后一种情况，则父表与子表是同一个表，这样的表称为自参照表，这种结构称为自参照完整性。
* 必须为父表定义主键。
* 主键不能包含空值，但允许在外键中出现空值。也就是说，只要外键的每个非空值出现在指定的主键中，这个外键的内容就是正确的。
* 在父表的表名后面指定列名或列名的组合。这个列或列的组合必须是父表的主键或候选键。
* 外键中列的数目必须和父表的主键中列的数目相同。
* 外键中列的数据类型必须和父表主键中对应列的数据类型相同。

## 在创建表时设置外键约束
在数据表中创建外键使用 FOREIGN KEY 关键字，具体的语法规则如下：
>[CONSTRAINT <外键名>] FOREIGN KEY 字段名 [，字段名2，…]
REFERENCES <主表名> 主键列1 [，主键列2，…]

其中：外键名为定义的外键约束的名称，一个表中不能有相同名称的外键；字段名表示子表需要添加外健约束的字段列；主表名即被子表外键所依赖的表的名称；主键列表示主表中定义的主键列或者列组合。

实例1：在 test_db 数据库中创建一个部门表 tb_dept1,表结构如下：
| 字段名称 | 数据类型    | 备注     |
| -------- | ----------- | -------- |
| id       | INT(11)     | 部门编号 |
| name     | VARCHAR(22) | 部门名称 |
| location | VARCHAR(22) | 部门位置 |
```sql
# 创建tb_dept1的SQL语句
mysql> CREATE TABLE tb_dept1
    -> (
    -> id INT(11) PRIMARY KEY,
    -> name VARCHAR(22) NOT NULL,
    -> location VARCHAR(50)
    -> );
Query OK, 0 rows affected (0.27 sec)
# 创建数据表 tb_emp6，并在表 tb_emp6 上创建外键约束，让它的键 deptId 作为外键关联到表 tb_dept1 的主键 id
mysql> CREATE TABLE tb_emp6 ( id INT(11) PRIMARY KEY, name VARCHAR(25), deptId INT(11), salary FLOAT, CONSTRAINT fk_emp_dept1 FOREIGN KEY(deptId) REFERENCES tb_dept1(id) );
Query OK, 0 rows affected (0.32 sec)

mysql> DESC tb_emp6;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | NO   | PRI | NULL    |       |
| name   | varchar(25) | YES  |     | NULL    |       |
| deptId | int(11)     | YES  | MUL | NULL    |       |
| salary | float       | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
4 rows in set (0.01 sec
```
<font color=red>提示：关联指的是关系数据库中，相关表之间的联系。它是通过相同的属性或属性组来表示的。子表的外键必须关联父表的主键，且关联字段的数据类型必须匹配，如果类型不一样，则创建子表时会出现错误“ERROR 1005(HY000)：Can't create table'database.tablename'(errno：150)”</font>

## 在修改表时添加外键约束
>语法规则：ALTER TABLE <数据表名> ADD CONSTRAINT <索引名>
FOREIGN KEY(<列名>) REFERENCES <主表名> (<列名>);

实例2：修改数据表 tb_emp2，将字段 deptId 设置为外键，与数据表 tb_dept1 的主键 id 进行关联
```sql
mysql> ALTER TABLE tb_emp3 ADD CONSTRAINT fk_tb_dept1 FOREIGN KEY(deptId) REFERENCES tb_dept1(id);
Query OK, 0 rows affected (0.70 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> ALTER TABLE tb_emp3 ADD CONSTRAINT fk_tb_dept1 FOREIGN KEY(deptId) REFERENCES tb_dept1(id);
Query OK, 0 rows affected (0.70 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

## 删除外键约束
对于数据库中定义的外键，如果不再需要，可以将其删除。外键一旦删除，就会解除主表和从表间的关联关系，MySQL 中删除外键的语法格式如下：
>ALTER TABLE <表名> DROP FOREIGN KEY <外键约束名>;

实例3：删除数据表 tb_emp2 中的外键约束 fk_tb_dept1。
```sql
mysql> ALTER TABLE tb_emp3
    -> DROP FOREIGN KEY fk_tb_dept1;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SHOW CREATE TABLE tb_emp3\G
*************************** 1. row ***************************
       Table: tb_emp3
Create Table: CREATE TABLE `tb_emp3` (
  `id` int(11) NOT NULL,
  `name` varchar(25) DEFAULT NULL,
  `deptId` int(11) DEFAULT NULL,
  `salary` float DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `fk_tb_dept1` (`deptId`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```
# 唯一约束(UNIQUE KEY)
唯一约束（Unique Key）要求该列唯一，允许为空，但只能出现一个空值。唯一约束可以确保一列或者几列不出现重复值。
## 创建表时设置唯一约束
定义完列之后直接使用 UNIQUE 关键字指定唯一约束，语法规则如下：
><字段名> <数据类型> UNIQUE

实例1：创建数据表 tb_dept2，指定部门的名称唯一。
```sql
mysql> CREATE TABLE tb_dept2
    -> (
    -> id INT(11) PRIMARY KEY,
    -> name VARCHAR(22) UNIQUE,
    -> location VARCHAR(50)
    -> );
Query OK, 0 rows affected (0.42 sec)

mysql> DESC tb_dept2;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(11)     | NO   | PRI | NULL    |       |
| name     | varchar(22) | YES  | UNI | NULL    |       |
| location | varchar(50) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```
<font color=red>UNIQUE 和 PRIMARY KEY 的区别：一个表可以有多个字段声明为 UNIQUE，但只能有一个 PRIMARY KEY 声明；声明为 PRIMAY KEY 的列不允许有空值，但是声明为 UNIQUE 的字段允许空值的存在。
</font>

## 在修改表时添加唯一约束
修改表添加唯一约束的语法格式为：
>ALTER TABLE <数据表名> ADD CONSTRAINT <唯一约束名> UNIQUE(<列名>);

实例2：修改数据表 tb_dept1，指定部门的名称唯一
```sql
mysql> ALTER TABLE tb_dept1
    -> ADD CONSTRAINT unique_name UNIQUE(name);
Query OK, 0 rows affected (0.22 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC tb_dept1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(11)     | NO   | PRI | NULL    |       |
| name     | varchar(22) | NO   | UNI | NULL    |       |
| location | varchar(50) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```
## 删除唯一约束
>语法格式：ALTER TABLE <表名> DROP INDEX <唯一约束名>;

实例3：删除数据表 tb_dept1 中的唯一约束 unique_name。
```sql
mysql> ALTER TABLE tb_dept1
    -> DROP INDEX unique_name;
Query OK, 0 rows affected (0.50 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC tb_dept1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(11)     | NO   | PRI | NULL    |       |
| name     | varchar(22) | NO   |     | NULL    |       |
| location | varchar(50) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```
# 检查约束(CHECK)
检查约束（CHECK）可以通过CREATE TABLE或ALTER TABLE语句实现，根据用户实际的完整性要求来定义。它可以分别对列或表实施 CHECK 约束。
## 选取设置检查约束的字段
检查约束使用 CHECK 关键字，具体的语法格式如下：
>CHECK <表达式>

其中：<表达式>指的就是 SQL 表达式，用于指定需要检查的限定条件。

若将 CHECK 约束子句置于表中某个列的定义之后，则这种约束也称为基于列的 CHECK 约束。在更新表数据的时候，系统会检查更新后的数据行是否满足 CHECK 约束中的限定条件。MySQL 可以使用简单的表达式来实现 CHECK 约束，也允许使用复杂的表达式作为限定条件，例如在限定条件中加入子查询。

<font color=red>将 CHECK 约束子句置于所有列的定义以及主键约束和外键定义之后，则这种约束也称为基于表的 CHECK 约束。该约束可以同时对表中多个列设置限定条件。</font>

## 创建表时设置检测约束
>语法规则：CHECK(<检查约束>)

实例1：在 test_db 数据库中创建 tb_emp7 数据表，要求 salary 字段值大于 0 且小于 10000
```sql
mysql> CREATE TABLE tb_emp7
    -> (
    -> id INT(11) PRIMARY KEY,
    -> name VARCHAR(25),
    -> deptId INT(11),
    -> salary FLOAT,
    -> CHECK(salary>0 AND salary<100),FOREIGN KEY(deptId) REFERENCES tb_dept1(id));
Query OK, 0 rows affected (0.28 sec)
```
## 修改表时添加检查约束
>语法规则：ALTER TABLE tb_emp7 ADD CONSTRAINT <检查约束名> CHECK(<检查约束>)

实例2：修改 tb_dept 数据表，要求 id 字段值大于 0
```sql
mysql> ALTER TABLE tb_emp7
    -> ADD CONSTRAINT check_id
    -> CHECK(id>0);
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
## 删除检查约束
>语法规则：ALTER TABLE <数据表名> DROP CONSTRAINT <检查约束名>;

# 默认值约束(DEFAULT)
默认值约束用来指定某列的默认值。
## 创建表时设置默认值约束
创建表时可以使用 DEFAULT 关键字设置默认值约束，具体的语法规则如下：
><字段名> <数据类型> DEFAULT <默认值>;

实例1：创建数据表 tb_dept3，指定部门位置默认为 Beijing
```sql
mysql> CREATE TABLE tb_dept3
    -> (
    -> id INT(11) PRIMARY KEY,
    -> name VARCHAR(22),
    -> location VARCHAR(50) DEFAULT 'Beijing');
Query OK, 0 rows affected (0.26 sec)

mysql> DESC tb_dept3;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(11)     | NO   | PRI | NULL    |       |
| name     | varchar(22) | YES  |     | NULL    |       |
| location | varchar(50) | YES  |     | Beijing |       |
+----------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```
## 修改表时添加默认值约束
>语法规则：ALTER TABLE<数据表名> CHANGE COLUMN<字段名><数据类型>DEFAULT<默认值>

实例2：修改数据表 tb_dept3，将部门位置的默认值修改为 Shanghai。
```sql
mysql> ALTER TABLE tb_dept3
    -> CHANGE COLUMN location
    -> location VARCHAR(50) DEFAULT 'Shanghai';
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC tb_dept3;
+----------+-------------+------+-----+----------+-------+
| Field    | Type        | Null | Key | Default  | Extra |
+----------+-------------+------+-----+----------+-------+
| id       | int(11)     | NO   | PRI | NULL     |       |
| name     | varchar(22) | YES  |     | NULL     |       |
| location | varchar(50) | YES  |     | Shanghai |       |
+----------+-------------+------+-----+----------+-------+
3 rows in set (0.00 sec)
```
## 删除默认值约束
修改表时删除默认值约束的语法规则如下：
>ALTER TABLE <数据库名>
CHANGE COLUMN <字段名><字段名><数据类型> DEFAULT NULL;

实例3：修改数据表 tb_dept3，将部门位置的默认值约束删除。
```sql
mysql> ALTER TABLE tb_dept3 CHANGE COLUMN location location VARCHAR(50) DEFAULT NULL;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC tb_dept3;                                                           +----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(11)     | NO   | PRI | NULL    |       |
| name     | varchar(22) | YES  |     | NULL    |       |
| location | varchar(50) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```
# 非空约束(NOT NULL)
非空约束可以通过 CREATE TABLE 或 ALTER TABLE 语句实现。在表中某个列的定义后加上关键字 NOT NULL 作为限定词，来约束该列的取值不能为空。非空约束（Not Null Constraint）指字段的值不能为空。对于使用了非空约束的字段，如果用户在添加数据时没有指定值，数据库系统就会报错。

## 创建表时设置非空约束
创建表时可以使用 NOT NULL 关键字设置非空约束，具体的语法规则如下：
><字段名> <数据类型> NOT NULL;

实例1：创建数据表 tb_dept4，指定部门名称不能为空。
```sql
mysql> CREATE TABLE tb_dept4
    -> (id INT(11) PRIMARY KEY, name VARCHAR(22) NOT NULL, location VARCHAR(50));
Query OK, 0 rows affected (0.36 sec)
mysql> DESC tb_dept4;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(11)     | NO   | PRI | NULL    |       |
| name     | varchar(22) | NO   |     | NULL    |       |
| location | varchar(50) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```
## 修改表时添加非空约束
>语法规则：ALTER TABLE <数据表名>
CHANGE COLUMN <字段名>
<字段名> <数据类型> NOT NULL;

实例2：修改数据表 tb_dept4，指定部门位置不能为空。
```sql
mysql> ALTER TABLE tb_dept4
    -> CHANGE COLUMN location
    -> location VARCHAR(50) NOT NULL;
Query OK, 0 rows affected (0.62 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC tb_dept4;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(11)     | NO   | PRI | NULL    |       |
| name     | varchar(22) | NO   |     | NULL    |       |
| location | varchar(50) | NO   |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```
## 删除非空约束
>语法规则：ALTER TABLE <数据表名>
CHANGE COLUMN <字段名> <字段名> <数据类型> NULL;

实例3：修改数据表 tb_dept4，将部门位置的非空约束删除。
```sql
mysql> ALTER TABLE tb_dept4
    -> CHANGE COLUMN location
    -> location VARCHAR(50) NULL;
Query OK, 0 rows affected (0.49 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC tb_dept4;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(11)     | NO   | PRI | NULL    |       |
| name     | varchar(22) | NO   |     | NULL    |       |
| location | varchar(50) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```
## 查看表中的约束
>语法格式：SHOW CREATE TABLE <数据表名>;
```sql
mysql> SHOW CREATE TABLe tb_dept4\G
*************************** 1. row ***************************
       Table: tb_dept4
Create Table: CREATE TABLE `tb_dept4` (
  `id` int(11) NOT NULL,
  `name` varchar(22) NOT NULL,
  `location` varchar(50) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```

