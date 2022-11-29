---
title: SQL-查询(一)
date: 2020-04-18 17:49:11
categories:    
- 计算机基础
- 数据库
tags:
- SQL
- 数据库
toc: true
---
# 查询数据表
SELECT 语句来查询数据。查询数据是指从数据库中根据需求，使用不同的查询方式来获取不同的数据，是使用频率最高、最重要的操作。

>语法格式如下：
`SELECT
{* | <字段列名>}
[
FROM <表 1>, <表 2>…
[WHERE <表达式>
[GROUP BY <group by definition>
[HAVING <expression> [{<operator> <expression>}…]]
[ORDER BY <order by definition>]
[LIMIT[<offset>,] <row count>]
]`

其中，各条子句说明如下：
* {*|<字段列名>}包含星号通配符的字段列表，表示所要查询字段的名称。
* <表1>，<表2>…，表1和表2表示查询数据的来源，可以是单个或多个。
* **WHERE**<表达式>是可选项，如果选择该项，将限定查询数据必须满足该查询条件。
* **GROUP BY**<字段>，该子句告诉数据库如何显示查询出来的数据，并按照指定的字段分组。
* **ORDER BY**< 字段 >]，按什么样的顺序显示查询出来的数据，可以进行的排序有升序（ASC）和降序（DESC），默认情况下是升序。
* **`[LIMIT[<offset>，]<row count>]`**，该子句告诉 MySQL 每次显示查询出来的数据条数。
## 查询表中所有字段
**1.** 使用"*"查询表的所有字段
>语法格式如下：SELECT * FROM 表名;

**2.** 列出表的所有字段
**SELECT**关键字后面的字段名为需要查找的字段，可以使用 DESC 命令查看表的结构。
## 查询表中指定的字段
查询表中的某一个字段的语法格式为：
>SELECT < 列名 > FROM < 表名 >;
```sql
mysql> SELECT name FROM tb_students_info;
+--------+
| name   |
+--------+
| Dany   |
| Thomas |
| Tom    |
+--------+
10 rows in set (0.00 sec)
```
# 去重(过滤重复数据)
使用**DISTINCT**关键字指示 数据库消除重复的记录值。
>语法格式为：SELECT DISTINCT <字段名> FROM <表名>;

# 设置别名
## 为表指定别名
当数据表名很长或者执行一些特殊查询的时候，为了方便操作，可以为表指定一个别名，
>语法格式为：<表名> [AS] <别名>

使用示例如下：
```sql
mysql> SELECT stu.name,stu.height FROM tb_students_info AS stu;
+--------+--------+
| name   | height |
+--------+--------+
| Dany   |    160 |
| Green  |    158 |
2 rows in set (0.04 sec)
```
## 为字段指定别名
每个 SELECT 后面指定输出的字段。有时为了显示结果更加直观，可以为字段指定一个别名。
>基本语法格式：<字段名> [AS] <别名>

使用示例如下：
```sql
mysql> SELECT name AS student_name, age AS student_age FROM tb_students_info;
+--------------+-------------+
| student_name | student_age |
+--------------+-------------+
| Dany         |          25 |
| Green        |          23 |
```
# 限制查询结果的记录条数
**SELECT**语句时往往返回的是所有匹配的行，有些时候我们仅需要返回第一行或者前几行，这时候就需要用到数据库**LIMT**子句
>基本的语法格式：`<LIMIT> [<位置偏移量>,] <行数>`

**LIMIT**接受一个或两个数字参数。参数必须是一个整数常量。如果给定两个参数，第一个参数指定第一个返回记录行的偏移量，第二个参数指定返回记录行的最大数目。
```sql
mysql> select * from tb_students_info limit 1, 2;
+----+------+---------+------+------+--------+---------------------+
| id | name | dept_id | age  | sex  | height | login_date          |
+----+------+---------+------+------+--------+---------------------+
|  2 | jun  |       2 |   12 | F    |    222 | 1000-01-01 00:00:01 |
+----+------+---------+------+------+--------+---------------------+
1 row in set (0.00 sec)
```
# 对查询结果进行排序
ORDER BY 子句主要用来将结果集中的数据按照一定的顺序进行排序。
>语法格式：ORDER BY {<列名> | <表达式> | <位置>} [ASC|DESC]

语法说明如下：
* **列名**：指定用于排序的列。可以指定多个列，列名之间用逗号分隔。
* **表达式**：指定用于排序的表达式。
* **位置**：指定用于排序的列在SELECT语句结果集中的位置，通常是正整数。
* **ASC|DESC**：关键字 ASC 表示按升序分组，关键字 DESC 表示按降序分组，其中 ASC 为默认值。这两个关键字必须位于对应的列名、表达式、列的位置之后。 

使用示例如下：
```sql
mysql> select * from tb_students_info ORDER by age;
+----+------+---------+------+------+--------+---------------------+
| id | name | dept_id | age  | sex  | height | login_date          |
+----+------+---------+------+------+--------+---------------------+
|  2 | jun  |       2 |   12 | F    |    222 | 1000-01-01 00:00:01 |
|  1 | peng |       2 |   25 | F    |    222 | 1000-01-01 00:00:00 |
+----+------+---------+------+------+--------+---------------------+
2 rows in set (0.00 sec)
mysql> SELECT name,height FROM tb_student_info ORDER BY height DESC,name ASC;
+--------+--------+
| name   | height |
+--------+--------+
| Henry  |    185 |
| Thomas |    178 |
```
注意：DESC 关键字只对前面的列进行降序排列，在这里只对 height 排序，而并没有对 name 进行排序，因此，height 按降序排序，而 name 仍按升序排序，如果要对多列进行降序排序，必须要在每一列的后面加 DESC 关键字。

# 条件查询
使用 WHERE 子句来指定查询条件，从 FROM 子句的中间结果中选取适当的数据行，达到数据过滤的效果。
>语法格式：WHERE <查询条件> {<判定运算1>，<判定运算2>，…}

运算的语法分类如下：
* <表达式1>{=|<|<=|>|>=|<=>|<>|！=}<表达式2>
* <表达式1>[NOT]LIKE<表达式2>
* <表达式1>[NOT][REGEXP|RLIKE]<表达式2>
* <表达式1>[NOT]BETWEEN<表达式2>AND<表达式3>
* <表达式1>IS[NOT]NULL
## 单一条件的查询语句
语句采用简单的相等过滤，使用示例如下：
```sql
mysql> select name, age
    -> from tb_students_info
    -> where age<=25;
+------+------+
| name | age  |
+------+------+
| peng |   25 |
| jun  |   12 |
+------+------+
2 rows in set (0.00 sec)
```
## 多条件的查询语句
SQL在**WHERE**子句中使用**AND**操作符限定只有满足所有查询条件的记录才会被返回。使用示例如下：
```sql
mysql> select * from tb_students_info
    -> where age<35 and height >170;
+----+------+---------+------+------+--------+---------------------+
| id | name | dept_id | age  | sex  | height | login_date          |
+----+------+---------+------+------+--------+---------------------+
|  1 | peng |       2 |   25 | F    |    222 | 1000-01-01 00:00:00 |
|  2 | jun  |       2 |   12 | F    |    222 | 1000-01-01 00:00:01 |
|  4 | Feng |       2 |   32 | F    |    175 | 1000-01-01 00:00:01 |
+----+------+---------+------+------+--------+---------------------+
3 rows in set (0.00 sec)
```
## 使用**LIKE**的模糊查询
>字符串匹配语法格式：<表达式1> [NOT] LIKE <表达式2>

相互间进行匹配运算的对象可以是CHAR、VARCHAR、TEXT、DATETIME 等数据类型。运算返回的结果是 TRUE 或 FALSE。通配符如下：

**1.百分号(%)**

百分号可以表示任何字符串，并且该字符串可以出现任意次。使用示例如下：
```sql
mysql> SELECT name FROM tb_students_info
    -> WHERE name LIKE '%e%';
+-------+
| name  |
+-------+
| Green |
| Henry |
| Jane  |
+-------+
3 rows in set (0.00 sec)
# 查找所有包含“e”字母的学生姓名
```
* sql默认不区分大小写，除非更换字符集的校对规则
* 百分号不匹配空值
* 百分号可以代表搜索模式中给定位置的 0 个、1 个或多个字符。
* 尾空格可能会干扰通配符的匹配，一般可以在搜索模式的最后附加一个百分号。

**2.下划线(_)**
下画线只匹配单个字符，而不是多个字符，也不是 0 个字符。
```sql
mysql> SELECT name FROM tb_students_info
    -> WHERE name LIKE '____y';
+-------+
| name  |
+-------+
| Henry |
+-------+
1 row in set (0.00 sec)
# 查找所有以字母“y”结尾，且“y”前面只有 4 个字母
```
## 日期字段作为条件的查询语句
以日期字段作为条件，可以使用比较运算符设置查询条件，也可以使用 BETWEEN AND 运算符查询某个范围内的值。使用示例如下：
```sql
mysql> SELECT * FROM tb_students_info
    -> WHERE login_date<'2016-01-01';
+----+-------+---------+------+------+--------+------------+
| id | name  | dept_id | age  | sex  | height | login_date |
+----+-------+---------+------+------+--------+------------+
|  1 | Dany  |       1 |   25 | F    |    160 | 2015-09-10 |
|  3 | Henry |       2 |   23 | M    |    185 | 2015-05-31 |
```

# 内连接查询(INNER JOIN)
利用条件表达式来消除交叉连接的某些数据行。关键字 INNER JOIN 连接两张表，并使用 ON 子句来设置连接条件。如果没有任何条件，INNER JOIN 和 CROSS JOIN 在语法上是等同的。
>语法格式：SELECT <列名1，列名2 …>
FROM <表名1> INNER JOIN <表名2> [ ON子句]

语法说明如下：
* <列名1，列名2…>：需要检索的列名。
* <表名1><表名2>：进行内连接的两张表的表名。
```sql
mysql> SELECT id,name,age,dept_name
    -> FROM tb_students_info,tb_departments
    -> WHERE tb_students_info.dept_id=tb_departments.dept_id;
+----+--------+------+-----------+
| id | name   | age  | dept_name |
+----+--------+------+-----------+
|  1 | Dany   |   25 | Computer  |
|  2 | Green  |   23 | Chinese   |
|  3 | Henry  |   23 | Math      
```
SELECT后面指定的列分别属于两个不同的表，id、name、age在表tb_students_info中，dept_name在表tb_departments。
```sql
mysql> SELECT id,name,age,dept_name
    -> FROM tb_students_info INNER JOIN tb_departments
    -> WHERE tb_students_info.dept_id=tb_departments.dept_id;
+----+--------+------+-----------+
| id | name   | age  | dept_name |
+----+--------+------+-----------+
|  1 | Dany   |   25 | Computer  |
|  2 | Green  |   23 | Chinese   |
|  3 | Henry  |   23 | Math      |
# 使用INNER JOIN语法进行内连接查询
```
# 外连接查询(LEFT/RIGHT JOIN)
外连接更加注重两张表之间的关系。按照连接表的顺序，可以分为左外连接和右外连接。左外连接又称为左连接，在 FROM 子句中使用关键字 LEFT OUTER JOIN 或者 LEFT JOIN，用于接收该关键字左表（基表）的所有行，并用这些行与该关键字右表（参考表）中的行进行匹配，即匹配左表中的每一行及右表中符合条件的行。

除了匹配的行之外，还包括左表中有但在右表中不匹配的行，对于这样的行，从右表中选择的列的值被设置为 NULL，即左外连接的结果集中的 NULL 值表示右表中没有找到与左表相符的记录。
```sql
mysql> SELECT name,dept_name
    -> FROM tb_students_info s
    -> LEFT OUTER JOIN tb_departments d
    -> ON s.dept_id=d.idept_id;
+------+-----------+
| name | dept_name |
+------+-----------+
| peng | Chinese   |
| jun  | Chinese   |
| Gui  | Computer  |
| Feng | Chinese   |
| Qi   | NULL      |
+------+-----------+
5 rows in set (0.00 sec)
# 学生Qi在tb_departments表中取出的值为 NULL。
```
右外连接又称为右连接，在 FROM 子句中使用 RIGHT OUTER JOIN 或者 RIGHT JOIN。与左外连接相反，右外连接以右表为基表，连接方法和左外连接相同。在右外连接的结果集中，除了匹配的行外，还包括右表中有但在左表中不匹配的行，对于这样的行，从左表中选择的值被设置为 NULL。
```sql
mysql> Select name,dept_name from tb_students_info s right outer join tb_departments d on s.dept_id=d.idept_id;
+------+-----------+
| name | dept_name |
+------+-----------+
| peng | Chinese   |
| jun  | Chinese   |
| Gui  | Computer  |
| Feng | Chinese   |
| NULL | Math      |
| NULL | Economy   |
| NULL | History   |
+------+-----------+
7 rows in set (0.00 sec)
# 从 tb_students_info 表中取出的值为 NULL。
```
