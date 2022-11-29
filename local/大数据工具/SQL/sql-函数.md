---
title: SQL-函数
date: 2020-04-21 21:03:54
categories:    
- 计算机基础
- 数据库
tags:
- SQL
- 数据库
toc: true
---
# 内建函数
## Aggregate函数
SQL Aggregate 函数计算从列中取得的值，返回一个单一的值。

有用的 Aggregate 函数：
### AVG()函数
用于返回数值列的平均值
>语法格式：SELECT AVG(column_name) FROM table_name
### COUNT()函数
COUNT() 函数返回匹配指定条件的行数。

#### SQL COUNT(column_name) 语法
COUNT(column_name) 函数返回指定列的值的数目（NULL 不计入）：
>SELECT COUNT(column_name) FROM table_name;
#### SQL COUNT(*) 语法
COUNT(*) 函数返回表中的记录数：

>SELECT COUNT(*) FROM table_name;
####  SQL COUNT(DISTINCT column_name) 语法
COUNT(DISTINCT column_name) 函数返回指定列的不同值的数目：

>SELECT COUNT(DISTINCT column_name) FROM table_name;

### MAX()函数
MAX()函数返回指定列的最大值。
>SQL MAX() 语法
SELECT MAX(column_name) FROM table_name;
### MIN()函数
MIN() 函数返回指定列的最小值。

>SELECT MIN(column_name) FROM table_name;

### SUM()函数
SUM() 函数返回数值列的总数。
>SELECT SUM(column_name) FROM table_name;

## Scalar函数
SQL Scalar 函数基于输入值，返回一个单一的值。
### UCASE() 函数
UCASE() 函数把字段的值转换为大写。

>SELECT UCASE(column_name) FROM table_name;

### LCASE() 函数
LCASE()函数把字段的值转换为小写。

>SELECT LCASE(column_name) FROM table_name;

### MID() 函数
MID()函数用于从文本字段中提取字符。
>SELECT MID(column_name,start[,length]) FROM table_name;
### LENGTH() 函数
LENGTH() 函数返回文本字段中值的长度。
>SELECT LENGTH(column_name) FROM table_name;
### ROUND()函数
ROUND() 函数用于把数值字段舍入为指定的小数位数。

>SELECT ROUND(column_name,decimals) FROM table_name;
### NOW()函数
NOW() 函数返回当前系统的日期和时间。
>SELECT NOW() FROM table_name;
### FORMAT()函数
FORMAT() 函数用于对字段的显示进行格式化。

>SELECT FORMAT(column_name,format) FROM table_name;
# 自定义函数
自定义函数是一种与存储过程十分相似的过程式数据库对象。它与存储过程一样，都是由SQL语句和过程式语句组成的代码片段，并且可以被应用程序和其他SQL语句调用。

自定义函数与存储过程之间存在几点区别：
* 自定义函数不能拥有输出参数，这是因为自定义函数自身就是输出参数；而存储过程可以拥有输出参数。
* 自定义函数中必须包含一条RETURN语句，而这条特殊的SQL语句不允许包含于存储过程中。
* 可以直接对自定义函数进行调用而不需要使用CALL语句，而对存储过程的调用需要使用CALL语句。

## 创建并使用自定义函数
使用 CREATE FUNCTION 语句创建自定义函数，语法格式如下：
>CREATE FUNCTION <函数名> ( [ <参数1> <类型1> [ , <参数2> <类型2>] ] … )
  RETURNS <类型>
  <函数主体>

语法说明如下：
* <函数名>：指定自定义函数的名称。注意，自定义函数不能与存储过程具有相同的名称。
* <参数><类型>：用于指定自定义函数的参数。这里的参数只有名称和类型，不能指定关键字 IN、OUT 和 INOUT。
* RETURNS<类型>：用于声明自定义函数返回值的数据类型。其中，<类型>用于指定返回值的数据类型。
* <函数主体>：自定义函数的主体部分，也称函数体。所有在存储过程中使用的 SQL 语句在自定义函数中同样适用，包括前面所介绍的局部变量、SET 语句、流程控制语句、游标等。除此之外，自定义函数体还必须包含一个 RETURN<值> 语句，其中<值>用于指定自定义函数的返回值。

<font color=red>若要查看数据库中存在哪些自定义函数，可以使用 SHOW FUNCTION STATUS 语句；若要查看数据库中某个具体的自定义函数，可以使用 SHOW CREATE FUNCTION<函数名> 语句，其中<函数名>用于指定该自定义函数的名称

注意：当使用 DELIMITER 命令时，应该避免使用反斜杠“\”字符，因为反斜杠是 MySQL 的转义字符。
</font>

函数调用的语法格式如下：
>SELECT <自定义函数名> ([<参数> [,...]])

实例1：创建存储函数，名称为 StuNameById，该函数返回 SELECT 语句的查询结果，数值类型为字符串类型
```sql
mysql> CREATE FUNCTION StuNameById()
    -> RETURNS VARCHAR(45)
    -> RETURN
    -> (SELECT name FROM tb_students_info WHERE id=1);
Query OK, 0 rows affected (0.00 sec)
# 调佣自定义函数
mysql> SELECT StuNameById();
+---------------+
| StuNameById() |
+---------------+
| peng          |
+---------------+
1 row in set (0.00 sec)
```

## 修改自定义函数
可以使用 ALTER FUNCTION 语句来修改自定义函数的某些相关特征。若要修改自定义函数的内容，则需要先删除该自定义函数，然后重新创建。

## 删除自定义函数
>语法格式：DROP FUNCTION [ IF EXISTS ] <自定义函数名>

语法说明：
* <自定义函数名>：指定要删除的自定义函数的名称。
* IF EXISTS：指定关键字，用于防止因误删除不存在的自定义函数而引发错误。