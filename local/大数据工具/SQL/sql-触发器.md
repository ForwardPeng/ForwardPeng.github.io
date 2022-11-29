---
title: SQL-触发器
date: 2020-04-21 18:16:10
categories:    
- 计算机基础
- 数据库
tags:
- SQL
- 数据库
toc: true
---
# 触发器简介
数据库中触发器是一个特殊的存储过程，不同的是执行存储过程要使用 CALL 语句来调用，而触发器的执行不需要使用 CALL 语句来调用，也不需要手工启动，只要一个预定义的事件发生就会被 MySQL自动调用。引发触发器执行的事件一般如下：
* 增加一条学生记录时，会自动检查年龄是否符合范围要求。
* 每当删除一条学生信息时，自动删除其成绩表上的对应记录。
* 每当删除一条数据时，在数据库存档表中保留一个备份副本。

触发程序的优点：
* 触发程序的执行是自动的，当对触发程序相关表的数据做出相应的修改后立即执行。
* 触发程序可以通过数据库中相关的表层叠修改另外的表。
* 触发程序可以实施比 FOREIGN KEY 约束、CHECK 约束更为复杂的检查和操作。

**1.INSERT触发器**

在 INSERT 语句执行之前或之后响应的触发器。使用 INSERT 触发器需要注意以下几点：
* 在 INSERT 触发器代码内，可引用一个名为 NEW（不区分大小写）的虚拟表来访问被插入的行。
* 在 BEFORE INSERT 触发器中，NEW 中的值也可以被更新，即允许更改被插入的值（只要具有对应的操作权限）。
* 对于 AUTO_INCREMENT 列，NEW 在 INSERT 执行之前包含的值是 0，在 INSERT 执行之后将包含新的自动生成值。

**2.UPDATE触发器**
在UPDATE语句执行之前或之后响应的触发器。使用UPDATE触发器需要注意以下几点：
* 在 UPDATE 触发器代码内，可引用一个名为 NEW（不区分大小写）的虚拟表来访问更新的值。
* 在 UPDATE 触发器代码内，可引用一个名为 OLD（不区分大小写）的虚拟表来访问 UPDATE 语句执行前的值。
* 在 BEFORE UPDATE 触发器中，NEW 中的值可能也被更新，即允许更改将要用于 UPDATE 语句中的值（只要具有对应的操作权限）。
* OLD中的值全部是只读的，不能被更新。

<font color=red>注意：当触发器设计对触发表自身的更新操作时，只能使用 BEFORE 类型的触发器，AFTER 类型的触发器将不被允许。</font>

**DELETE触发器**

DELETE 语句执行之前或之后响应的触发器。使用 DELETE 触发器需要注意以下几点:
* 在 DELETE 触发器代码内，可以引用一个名为 OLD（不区分大小写）的虚拟表来访问被删除的行。
* OLD 中的值全部是只读的，不能被更新。

总体来说，触发器使用的过程中，MySQL会按照以下方式来处理错误。若对于事务性表，如果触发程序失败，以及由此导致的整个语句失败，那么该语句所执行的所有更改将回滚；对于非事务性表，则不能执行此类回滚，即使语句失败，失败之前所做的任何更改依然有效。若 BEFORE 触发程序失败，则MySQL将不执行相应行上的操作。若在BEFORE或AFTER触发程序的执行过程中出现错误，则将导致调用触发程序的整个语句失败。仅当BEFORE触发程序和行操作均已被成功执行，MySQL才会执行AFTER触发程序。

# 创建触发器(CREATE TRIGGER)
触发器是与MySQL数据表有关的数据库对象，在满足定义条件时触发，并执行触发器中定义的语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性。
>语法格式：CREATE <触发器名> < BEFORE | AFTER >
<INSERT | UPDATE | DELETE >
ON <表名> FOR EACH Row<触发器主体>

语法说明如下：

**1.触发器名**：触发器的名称，触发器在当前数据库中必须具有唯一的名称。如果要在某个特定数据库中创建，名称前面应该加上数据库的名称。

**2.INSERT | UPDATE | DELETE**：触发事件，用于指定激活触发器的语句的种类。

<font color=red> 注意：三种触发器的执行时间如下。
* INSERT：将新行插入表时激活触发器。例如，INSERT的BEFORE触发器不仅能被MySQL的INSERT语句激活，也能被LOAD DATA 语句激活。
* DELETE： 从表中删除某一行数据时激活触发器，例如DELETE和REPLACE语句。
* UPDATE：更改表中某一行数据时激活触发器，例如UPDATE语句。
</font>

**3.BEFORE | AFTER**：BEFORE 和 AFTER，触发器被触发的时刻，表示触发器是在激活它的语句之前或之后触发。若希望验证新数据是否满足条件，则使用 BEFORE 选项；若希望在激活触发器的语句执行之后完成几个或更多的改变，则通常使用 AFTER 选项。

**4.表名**：与触发器相关联的表名，此表必须是永久性表，不能将触发器与临时表或视图关联起来。在该表上触发事件发生时才会激活触发器。同一个表不能拥有两个具有相同触发时刻和事件的触发器。例如，对于一张数据表，不能同时有两个 BEFORE UPDATE 触发器，但可以有一个 BEFORE UPDATE 触发器和一个 BEFORE INSERT 触发器，或一个 BEFORE UPDATE 触发器和一个 AFTER UPDATE 触发器。

**5.触发器主体**：触发器动作主体，包含触发器激活时将要执行的 MySQL 语句。如果要执行多个语句，可使用 BEGIN…END 复合语句结构。

**6.FOR EACH ROW**：一般是指行级触发，对于受触发事件影响的每一行都要激活触发器的动作。例如，使用 INSERT 语句向某个表中插入多行数据时，触发器会对每一行数据的插入都执行相应的触发器动作。

<font color=red>注意：每个表都支持 INSERT、UPDATE 和DELETE的BEFORE 与AFTER，因此每个表最多支持6个触发器。每个表的每个事件每次只允许有一个触发器。单一触发器不能与多个事件或多个表关联。</font>

## 创建BEFORE类型触发器
实例1：创建一个名为 SumOfSalary 的触发器，触发的条件是向数据表 tb_emp8 中插入数据之前，对新插入的 salary 字段值进行求和计算。
```sql
mysql> CREATE TRIGGER SumOfSalary
    -> BEFORE INSERT ON tb_emp8
    -> FOR EACH ROW
    -> SET @sum=@sum+NEW.salary;
Query OK, 0 rows affected (0.08 sec)

mysql> SET @sum=0;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO tb_emp8
    -> VALUES(1,'A',1,1000),(2,'B',1,500);
Query OK, 2 rows affected (0.04 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT @sum;
+------+
| @sum |
+------+
| 1500 |
+------+
1 row in set (0.00 sec)
```
## 创建AFTER类型触发器
实例2：创建一个名为 double_salary 的触发器，触发的条件是向数据表 tb_emp6 中插入数据之后，再向数据表 tb_emp7 中插入相同的数据，并且 salary 为 tb_emp6 中新插入的 salary 字段值的 2 倍。输入的 SQL 语句和执行过程如下所示。
```sql
mysql> CREATE TRIGGER double_salary
    -> AFTER INSERT ON tb_emp6
    -> FOR EACH ROW
    -> INSERT INTO tb_emp7
    -> VALUE (NEW.id, NEW.name, deptId, 2*NEW.salary);
Query OK, 0 rows affected (0.09 sec)
mysql> insert into tb_emp6 values(1,'A',1,1000),(2,'B',1,500);
Query OK, 2 rows affected (0.04 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM tb_emp6;
+----+------+--------+--------+
| id | name | deptId | salary |
+----+------+--------+--------+
|  1 | A    |      1 |   1000 |
|  2 | B    |      1 |    500 |
+----+------+--------+--------+
2 rows in set (0.00 sec)

mysql> SELECT * FROM tb_emp7;
+----+------+--------+--------+
| id | name | deptId | salary |
+----+------+--------+--------+
|  1 | A    |   NULL |   2000 |
|  2 | B    |   NULL |   1000 |
+----+------+--------+--------+
2 rows in set (0.00 sec)
```
# 修改和删除触发器(DROP TRIGGER)
>语法格式：DROP TRIGGER [ IF EXISTS ] [数据库名] <触发器名>

语法说明：
* 触发器名：要删除的触发器名称。
* 数据库名：可选项。指定触发器所在的数据库的名称。若没有指定，则为当前默认的数据库。
* 权限：执行 DROP TRIGGER 语句需要 SUPER 权限。
* IF EXISTS：避免在没有触发器的情况下删除触发器。

<font color=red>注意：删除一个表的同时，也会自动删除该表上的触发器。另外，触发器不能更新或覆盖，为了修改一个触发器，必须先删除它，再重新创建。</font>

## 删除触发器
实例：删除 double_salary 触发器。
```sql
mysql> INSERT INTO tb_emp6
    -> VALUES (3,'C', 1, 200);
Query OK, 1 row affected (0.04 sec)

mysql> SELECT * FROm tb_emp6;
+----+------+--------+--------+
| id | name | deptId | salary |
+----+------+--------+--------+
|  1 | A    |      1 |   1000 |
|  2 | B    |      1 |    500 |
|  3 | C    |      1 |    200 |
+----+------+--------+--------+
3 rows in set (0.01 sec)

mysql> SELECT * FROm tb_emp7;
+----+------+--------+--------+
| id | name | deptId | salary |
+----+------+--------+--------+
|  1 | A    |   NULL |   2000 |
|  2 | B    |   NULL |   1000 |
+----+------+--------+--------+
2 rows in set (0.00 sec)
```
