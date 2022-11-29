---
title: SQL-存储
date: 2020-04-21 17:11:25
categories:    
- 计算机基础
- 数据库
tags:
- SQL
- 数据库
toc: true
---
# 存储引擎

## 什么是存储引擎
数据库存储引擎是数据库底层软件组件，数据库管理系统使用数据引擎进行创建、查询、更新和删除数据操作。不同的存储引擎提供不同的存储机制、索引技巧、锁定水平等功能，使用不同的存储引擎还可以获得特定的功能。

<font color=red>InnoDB 事务型数据库的首选引擎，支持事务安全表（ACID），支持行锁定和外键。MySQL 5.5.5 之后，InnoDB 作为默认存储引擎。</font>

MyISAM 是基于 ISAM 的存储引擎，并对其进行扩展，是在 Web、数据仓储和其他应用环境下最常使用的存储引擎之一。MyISAM 拥有较高的插入、查询速度，但不支持事务
MEMORY 存储引擎将表中的数据存储到内存中，为查询和引用其他数据提供快速访问。

## MySQL 5.7 支持的存储引擎
MySQL 5.7 支持的存储引擎有 InnoDB、MyISAM、Memory、Merge、Archive、Federated、CSV、BLACKHOLE 等。可以使用SHOW ENGINES语句查看系统所支持的引擎类型，结果如下：
```sql
mysql> SHOW ENGINES;
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
9 rows in set (0.00 sec)
# Support 列的值表示某种引擎是否能使用，YES表示可以使用，NO表示不能使用，DEFAULT表示该引擎为当前默认的存储引擎
```
## 选择MySQL存储引擎
不同的存储引擎都有各自的特点，以适应不同的需求。
| 功能         | MyISAM | MEMORY | InnoDB | Archive |
| ------------ | ------ | ------ | ------ | ------- |
| 存储限制     | 256TB  | RAM    | 64TB   | None    |
| 支持事务     | No     | No     | Yes    | No      |
| 支持全文索引 | Yes    | No     | No     | No      |
| 支持树索引   | Yes    | Yes    | Yes    | No      |
| 支持哈希索引 | No     | Yes    | No     | No      |
| 支持数据缓存 | No     | N/A    | Yes    | No      |
| 支持外键     | No     | No     | Yes    | No      |
选择MySQL存储引擎：
* 如果要提供提交、回滚和恢复的事务安全（ACID 兼容）能力，并要求实现并发控制，InnoDB 是一个很好的选择。
* 如果数据表主要用来插入和查询记录，则 MyISAM 引擎提供较高的处理效率。
* 如果只是临时存放数据，数据量不大，并且不需要较高的数据安全性，可以选择将数据保存在内存的 MEMORY 引擎中，MySQL 中使用该引擎作为临时表，存放查询的中间结果。
* 如果只有 INSERT 和 SELECT 操作，可以选择Archive 引擎，Archive 存储引擎支持高并发的插入操作，但是本身并不是事务安全的。Archive 存储引擎非常适合存储归档数据，如记录日志信息可以使用 Archive 引擎

修改数据库临时的默认存储引擎:
>SET default_storage_engine=< 存储引擎名 >

# 存储过程简介
存储过程是一组为了完成特定功能的 SQL 语句集合。使用存储过程的目的是将常用或复杂的工作预先用 SQL 语句写好并用一个指定名称存储起来，这个过程经编译和优化后存储在数据库服务器中，因此称为存储过程。当以后需要数据库提供与已定义好的存储过程的功能相同的服务时，只需调用“CALL存储过程名字”即可自动完成。优点如下：

**1.封装性**
存储过程被创建后，可以在程序中被多次调用，而不必重新编写该存储过程的 SQL 语句，并且数据库专业人员可以随时对存储过程进行修改，而不会影响到调用它的应用程序源代码。

**2.可增强 SQL 语句的功能和灵活性**
存储过程可以用流程控制语句编写，有很强的灵活性，可以完成复杂的判断和较复杂的运算。

**3.可减少网络流量**
由于存储过程是在服务器端运行的，且执行速度快，因此当客户计算机上调用该存储过程时，网络中传送的只是该调用语句，从而可降低网络负载。

**4.高性能**
存储过程执行一次后，产生的二进制代码就驻留在缓冲区，在以后的调用中，只需要从缓冲区中执行二进制代码即可，从而提高了系统的效率和性能。

**5.提高数据库的安全性和数据完整性**
使用存储过程可以完成所有数据库操作，并且可以通过编程的方式控制数据库信息访问的权限。

# 创建存储过程(CREATE PROCEDURE)
使用 CREATE PROCEDURE 语句创建存储过程，语法格式如下：
>CREATE PROCEDURE <过程名> ( [过程参数[,…] ] ) <过程体>
[过程参数[,…] ] 格式
[ IN | OUT | INOUT ] <参数名> <类型>

语法说明如下：

**1.过程名**

存储过程的名称，默认在当前数据库中创建。若需要在特定数据库中创建存储过程，则要在名称前面加上数据库的名称，即 db_name.sp_name。需要注意的是，名称应当尽量避免选取与 MySQL 内置函数相同的名称，否则会发生错误。

**3.过程参数**

存储过程的参数列表。其中，<参数名>为参数名，<类型>为参数的类型（可以是任何有效的 MySQL 数据类型）。当有多个参数时，参数列表中彼此间用逗号分隔。存储过程可以没有参数（此时存储过程的名称后仍需加上一对括号），也可以有 1 个或多个参数。

MySQL 存储过程支持三种类型的参数，即输入参数、输出参数和输入/输出参数，分别用 IN、OUT 和 INOUT 三个关键字标识。其中，输入参数可以传递给一个存储过程，输出参数用于存储过程需要返回一个操作结果的情形，而输入/输出参数既可以充当输入参数也可以充当输出参数。需要注意的是，参数的取名不要与数据表的列名相同，否则尽管不会返回出错信息，但是存储过程的 SQL 语句会将参数名看作列名，从而引发不可预知的结果。

**3.过程体**

存储过程的主体部分，也称为存储过程体，包含在过程调用的时候必须执行的 SQL 语句。这个部分以关键字 BEGIN 开始，以关键字 END 结束。若存储过程体中只有一条SQL语句，则可以省略BEGIN-END标志。通常可使用 DELIMITER 命令将结束命令修改为其他字符。
>语法格式：DELIMITER $$

语法说明：
* \$$ 是用户定义的结束符，通常这个符号可以是一些特殊的符号，如两个“?”或两个“￥”等。
* 当使用 DELIMITER 命令时，应该避免使用反斜杠“\”字符，因为它是MySQL的转义字符。
  
<font color=red>DELIMITER 和分号“;”之间一定要有一个空格。在创建存储过程时，必须具有 CREATE ROUTINE 权限。可以使用 SHOW PROCEDURE STATUS 命令查看数据库中存在哪些存储过程，若要查看某个存储过程的具体信息，则可以使用 SHOW CREATE PROCEDURE <存储过程名>。</font>

## 创建不带参数的存储过程
实例1：创建名称为 ShowStuScore 的存储过程，存储过程的作用是从学生成绩信息表中查询学生的成绩信息
```sql
mysql> DELIMITER //
mysql> CREATE PROCEDURE ShowStuScore()
    -> BEGIN
    -> SELECT * FROM tb_students_score;
    -> END //
Query OK, 0 rows affected (0.05 sec)

mysql> DELIMITER ;
mysql> CALL ShowStuScore();
+----+--------------+---------------+
| id | student_name | student_score |
+----+--------------+---------------+
|  1 | Dany         |            90 |
|  2 | Green        |            99 |
|  3 | Henry        |            95 |
|  4 | Jane         |            98 |
|  5 | Jim          |            88 |
|  6 | John         |            94 |
|  7 | Lily         |           100 |
|  8 | Susan        |            96 |
|  9 | Thomas       |            93 |
+----+--------------+---------------+
9 rows in set (0.00 sec)
Query OK, 0 rows affected (0.00 sec)
```
## 创建带参数的存储过程
实例2：创建名称为 GetScoreByStu 的存储过程，输入参数是学生姓名。存储过程的作用是通过输入的学生姓名从学生成绩信息表中查询指定学生的成绩信息。
```sql
mysql> DELIMITER //
mysql> CREATE PROCEDURE GetScoreByStu
    -> (IN name VARCHAR(30))
    -> BEGIN
    -> SELECT student_score FROM tb_students_score
    -> WHERE student_name=name;
    -> END //
Query OK, 0 rows affected (0.00 sec)
mysql> CALL GetScoreByStu('Lily');
+---------------+
| student_score |
+---------------+
|           100 |
+---------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```
# 修改存储过程(ALTE PROCEDURE)
通过 ALTER PROCEDURE 语句来修改存储过程，语法格式如下：
>ALTER PROCEDURE 存储过程名[特征...]

特征指定存储过程的特征，可能取值有：
* CONTAINS SQL 表示子程序包含 SQL 语句，但不包含读或写数据的语句。
* NO SQL 表示子程序中不包含 SQL 语句。
* READS SQL DATA 表示子程序中包含读数据的语句。
* MODIFIES SQL DATA 表示子程序中包含写数据的语句。
* SQL SECURITY { DEFINER |INVOKER } 指明谁有权限来执行。
* DEFINER 表示只有定义者自己才能够执行。
* INVOKER 表示调用者可以执行。
* COMMENT 'string' 表示注释信息。

实例1：修改存储过程 showstuscore 的定义，将读写权限改为 MODIFIES SQL DATA。
```sql
mysql> ALTER PROCEDURE showstuscore MODIFIES SQL DATA SQL SECURITY INVOKER;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW CREATE PROCEDURE showstuscore \G
*************************** 1. row ***************************
           Procedure: showstuscore
            sql_mode: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
    Create Procedure: CREATE DEFINER=`root`@`localhost` PROCEDURE `showstuscore`()
    MODIFIES SQL DATA
    SQL SECURITY INVOKER
BEGIN
SELECT * FROM tb_students_score;
END
character_set_client: utf8
collation_connection: utf8_general_ci
  Database Collation: utf8_general_ci
1 row in set (0.00 sec)
# 访问数据的权限已经变成了 MODIFIES SQL DATA，安全类型也变成了 INVOKE。
```
<font color=red>提示：ALTER PROCEDURE 语句用于修改存储过程的某些特征。如果要修改存储过程的内容，可以先删除原存储过程，再以相同的命名创建新的存储过程；如果要修改存储过程的名称，可以先删除原存储过程，再以不同的命名创建新的存储过程。</font>

# 删除存储过程(DROP PROCEDURE)
使用 DROP PROCEDURE 语句来删除数据库中已经存在的存储过程。语法格式如下：
>DROP {PROCEDURE | FUNCTION}[IF EXISTS] <过程名>

语法说明如下：
* 过程名：指定要删除的存储过程的名称。
* IF EXISTS：指定这个关键字，用于防止因删除不存在的存储过程而引发的错误。

<font color=red>注意：存储过程名称后面没有参数列表，也没有括号，在删除之前，必须确认该存储过程没有任何依赖关系，否则会导致其他与之关联的存储过程无法运行。</font>

实例1：删除存储过程 showstuscore，删除后，可以通过查询 information_schema 数据库下的 routines 表来确认上面的删除是否成功。
```sql
mysql> DROP PROCEDURE showstuscore;
Query OK, 0 rows affected (0.00 sec)
mysql> SELECT * FROM information_schema.routines WHERE routine_name='showstuscore';
Empty set (0.03 sec)

```
