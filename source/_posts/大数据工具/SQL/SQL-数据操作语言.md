---
title: SQL-数据操作语言(DML)
date: 2020-04-16 18:05:27
categories:    
- 计算机基础
- 数据库
tags:
- SQL
- 数据库
toc: true
---
# **INSERT INTO**-向数据库表中插入数据
**INSERT**语句有两种语法形式，分别是**INSERT...VALUES**语句和**INSERT...SET**语句。

**1. INSERT...VALUES**的语法格式为：
>INSERT INTO <表名> [ <列名1> [ , … <列名n>] ]
VALUES (值1) [… , (值n) ];

语法说明如下：
* <表名>：指定被操作的表名。
* <列名>：指定需要插入数据的列名。若向表中的所有列插入数据，则全部的列名均可以省略，直接采用 INSERT<表名>VALUES(…) 即可。
* VALUES 或 VALUE 子句：该子句包含要插入的数据清单。数据清单中数据的顺序要和列的顺序相对应。

**2. INSERT...SET**的语法格式为：
>INSERT INTO <表名>
SET <列名1> = <值1>,
        <列名2> = <值2>,
        …

此语句用于给表中的某些列指定对应的列值，即要插入的数据的列名在**SET**子句中指定，col_name为指定的列名，等号后面为指定的数据，而对于未指定的列，列值会指定为该列的默认值。**INSERT**语句的两种形式可是看出：
* 使用 INSERT…VALUES 语句可以向表中插入一行数据，也可以插入多行数据；
* 使用 INSERT…SET 语句可以指定插入行中每列的值，也可以指定部分列的值；
* INSERT…SELECT 语句向表中插入其他表的数据。
* 采用 INSERT…SET 语句可以向表中插入部分列的值，这种方式更为灵活；
* NSERT…VALUES 语句可以一次插入多条数据。

## 向表中的全部字段添加值
插入数据时，指定表的所有字段，为每一个字段插入新的值。
```sql
mysql> INSERT INTO tb_courses
    -> (course_id, course_name, course_grade, course_info)
    -> values(1, 'Math', 3, 'Computer Math');
Query OK, 1 row affected (0.04 sec)

mysql> select * from tb_courses;
+-----------+-------------+--------------+---------------+
| course_id | course_name | course_grade | course_info   |
+-----------+-------------+--------------+---------------+
|         1 | Math        |            3 | Computer Math |
+-----------+-------------+--------------+---------------+
1 row in set (0.00 sec)
```
使用**INSERT**插入数据，允许列名称顺序与定义不同，必须保证值顺序与列字段顺序相同。允许列名称为空，插入顺序必须与数据表中定义的顺序相同。

## 表中指定字段添加值
表的指定字段插入数据，是在 INSERT 语句中只向部分字段中插入值，而其他字段的值为表定义时的默认值，示例如下：
```sql
mysql> insert into tb_courses
    -> (course_name, course_grade, course_info)
    -> values('System',3,'Operation System');
Query OK, 1 row affected (0.04 sec)

mysql> select * from tb_courses;
+-----------+-------------+--------------+------------------+
| course_id | course_name | course_grade | course_info      |
+-----------+-------------+--------------+------------------+
|         1 | Math        |            3 | Computer Math    |
|         2 | System      |            3 | Operation System |
+-----------+-------------+--------------+------------------+
2 rows in set (0.00 sec)
```
## **INSERT INTO…FROM**语句复制表数据
**SELECT**子句返回的是一个查询到的结果集，**INSERT**语句将这个结果集插入指定表中，结果集中的每行数据的字段数、字段的数据类型都必须与被操作的表完全一致。使用示例如下：
```sql
mysql> INSERT INTO tb_courses_new 
    -> (course_id, course_name, course_grade, course_info)
    -> SELECT course_id, course_name, course_grade, course_info
    -> FROM tb_courses;
Query OK, 2 rows affected (0.05 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> select * from tb_courses_new;
+-----------+-------------+--------------+------------------+
| course_id | course_name | course_grade | course_info      |
+-----------+-------------+--------------+------------------+
|         1 | Math        |            3 | Computer Math    |
|         2 | System      |            3 | Operation System |
+-----------+-------------+--------------+------------------+
2 rows in set (0.00 sec)
```
# **DELETE**-从数据库表中删除数据
SQL中使用**DELETE**语句来删除表的一行或者多行数据。
## 删除单个表中的数据
**DELETE**语句从单个表中删除数据
>语法格式:DELETE FROM <表名> [WHERE 子句] [ORDER BY 子句] [LIMIT 子句]

语法说明如下：
* <表名>：指定要删除数据的表名。
* ORDER BY 子句：可选项。表示删除时，表中各行将按照子句中指定的顺序进行删除。
* WHERE 子句：可选项。表示为删除操作限定删除条件，若省略该子句，则代表删除该表中的所有行。
* LIMIT 子句：可选项。用于告知服务器在控制命令被返回到客户端前被删除行的最大值。
  
使用示例如下：
```sql
mysql> DELETE FROM tb_courses
    -> WHERE course_id = 2;
Query OK, 1 row affected (0.04 sec)

mysql> select * from tb_courses;
+-----------+-------------+--------------+---------------+
| course_id | course_name | course_grade | course_info   |
+-----------+-------------+--------------+---------------+
|         1 | Math        |            3 | Computer Math |
+-----------+-------------+--------------+---------------+
1 row in set (0.00 sec)
```

# **UPDATE**-更新数据库表中的数据
UPDATE 语句修改单个表，语法格式为
>UPDATE <表名> SET 字段 1=值 1 [,字段 2=值 2… ] [WHERE 子句 ]
[ORDER BY 子句] [LIMIT 子句]

语法说明如下：
* <表名>：用于指定要更新的表名称
* SET 子句：用于指定表中要修改的列名及其列值。其中，每个指定的列值可以是表达式，也可以是该列对应的默认值。如果指定的是默认值，可用关键字 DEFAULT 表示列值。
* WHERE 子句：可选项。用于限定表中要修改的行。若不指定，则修改表中所有的行。
* ORDER BY 子句：可选项。用于限定表中的行被修改的次序。
* LIMIT 子句：可选项。用于限定被修改的行数。

## 修改表中的数据
使用示例如下：
```sql
mysql> UPDATE tb_courses SET course_grade=4;
Query OK, 1 row affected (0.05 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from tb_courses;
+-----------+-------------+--------------+---------------+
| course_id | course_name | course_grade | course_info   |
+-----------+-------------+--------------+---------------+
|         1 | Math        |            4 | Computer Math |
+-----------+-------------+--------------+---------------+
1 row in set (0.00 sec)
```
## 根据条件修改表中的数据
使用示例如下：
```sql
mysql> UPDATE tb_courses
    -> SET course_name='DB', course_grade=3.5
    -> WHERE course_id=1;
Query OK, 1 row affected (0.05 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from tb_courses;
+-----------+-------------+--------------+---------------+
| course_id | course_name | course_grade | course_info   |
+-----------+-------------+--------------+---------------+
|         1 | DB          |          3.5 | Computer Math |
+-----------+-------------+--------------+---------------+
1 row in set (0.01 sec)

```
# **SELECT**-从数据库表中获取数据
数据表查询见[SQL-查询](https://forwardpeng.github.io/2020/04/16/SQL-%E7%B4%A2%E5%BC%95/)
