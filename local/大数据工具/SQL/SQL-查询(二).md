---
title: SQL-查询(二)
date: 2020-04-19 22:14:35
categories:    
- 计算机基础
- 数据库
tags:
- SQL
- 数据库
toc: true
---
# 子查询
子查询指一个查询语句嵌套在另一个查询语句内部的查询，子查询结果作为外层另一个查询的过滤条件，查询可以基于一个表或者多个表。可以添加到 SELECT、UPDATE 和 DELETE 语句中，而且可以进行多层嵌套。子查询也可以使用比较运算符，如“<”、“<=”、“>”、“>=”、“！=”等。

## 子查询中常见的运算符
**1.IN子查询**

关键字 IN 所使用的子查询主要用于判断一个给定值是否存在于子查询的结果集中。其语法格式为：
><表达式> [NOT] IN <子查询>

语法说明：
* <表达式>：用于指定表达式。当表达式与子查询返回的结果集中的某个值相等时，返回 TRUE，否则返回 FALSE；若使用关键字 NOT，则返回的值正好相反。
* <子查询>：用于指定子查询。这里的子查询只能返回一列数据。对于比较复杂的查询要求，可以使用 SELECT 语句实现子查询的多层嵌套。

**2.比较运算符子查询**

比较运算符所使用的子查询主要用于对表达式的值和子查询返回的值进行比较运算，语法格式为：
><表达式> {= | < | > | >= | <= | <=> | < > | != }
{ ALL | SOME | ANY} <子查询>

语法说明如下：
* <子查询>：用于指定子查询。
* <表达式>：用于指定要进行比较的表达式。
* ALL、SOME 和 ANY：可选项。用于指定对比较运算的限制。其中，关键字 ALL 用于指定表达式需要与子查询结果集中的每个值都进行比较，当表达式与每个值都满足比较关系时，会返回 TRUE，否则返回 FALSE；关键字 SOME 和 ANY 是同义词，表示表达式只要与子查询结果集中的某个值满足比较关系，就返回 TRUE，否则返回 FALSE。
  
**3.EXIST子查询**
关键字 EXIST 所使用的子查询主要用于判断子查询的结果集是否为空。其语法格式为：
>EXIST <子查询>

若子查询的结果集不为空，则返回 TRUE；否则返回 FALSE。
## 子查询的应用
实例1：先执行内层子查询，再执行外层查询，内层子查询的结果作为外部查询的比较条件。
```sql
mysql> select name from tb_students_info where dept_id in (select idept_id from tb_departments where dept_type='A');
+------+
| name |
+------+
| peng |
| jun  |
| Gui  |
| Feng |
+------+
4 rows in set (0.00 sec)。
```
实例2：在 tb_departments 表中查询 dept_name 等于“Computer”的学院 id，然后在 tb_students_info 表中查询所有该学院的学生的姓名
```sql
mysql> select name from tb_students_info
    -> where dept_id=
    -> (select idept_id
    -> from tb_departments
    -> where dept_name='Computer');
+------+
| name |
+------+
| Gui  |
+------+
1 row in set (0.00 sec)
```
实例3：查询的dept_name不等于'Computer'的学院id，然后查询学生姓名。
```sql
mysql> select name from tb_students_info
    -> where dept_id <>
    -> (select idept_id
    -> from tb_departments
    -> where dept_name='Computer');
+------+
| name |
+------+
| peng |
| jun  |
| Feng |
| Qi   |
+------+
4 rows in set (0.00 sec)
```
# 分组查询(GROUP BY)
 使用 GROUP BY 子句，将结果集中的数据行根据选择列的值进行逻辑分组，以便能汇总表内容的子集，实现对每个组而不是对整个结果集进行整合。
 >语法格式：GROUP BY { <列名> | <表达式> | <位置> } [ASC | DESC]

语法说明：
* <列名>：指定用于分组的列。可以指定多个列，彼此间用逗号分隔。
* <表达式>：指定用于分组的表达式。通常与聚合函数一块使用，例如可将表达式 COUNT(*)AS'人数' 作为 SELECT 选择列表清单的一项。
* <位置>：指定用于分组的选择列在 SELECT 语句结果集中的位置，通常是一个正整数。例如，GROUP BY 2 表示根据 SELECT 语句列清单上的第 2 列的值进行逻辑分组。
* ASC|DESC：关键字 ASC 表示按升序分组，关键字 DESC 表示按降序分组，其中 ASC 为默认值，注意这两个关键字必须位于对应的列名、表达式、列的位置之后。

使用GROUP BY子句时注意：
* GROUP BY 子句可以包含任意数目的列，使其可以对分组进行嵌套，为数据分组提供更加细致的控制。
* GROUP BY 子句列出的每个列都必须是检索列或有效的表达式，但不能是聚合函数。若在 SELECT 语句中使用表达式，则必须在 GROUP BY 子句中指定相同的表达式。
* 除聚合函数之外，SELECT 语句中的每个列都必须在 GROUP BY 子句中给出。
* 若用于分组的列中包含有 NULL 值，则 NULL 将作为一个单独的分组返回；若该列中存在多个 NULL 值，则将这些 NULL 值所在的行分为一组。
* 
实例：据 dept_id 对 tb_students_info 表中的数据进行分组，将每个学院的学生姓名显示出来
```sql
mysql> select dept_id,GROUP_CONCAT(name) AS names FROM tb_students_info group by dept_id;
+---------+---------------+
| dept_id | names         |
+---------+---------------+
|       0 | Qi            |
|       1 | Gui           |
|       2 | peng,jun,Feng |
+---------+---------------+
3 rows in set (0.00 sec)
```
# 指定过滤条件(HAVING)
 使用 HAVING 子句过滤分组，在结果集中规定了包含哪些分组和排除哪些分组。
 >语法格式：HAVING <条件>

HAVING 子句和 WHERE 子句非常相似，HAVING 子句支持 WHERE 子句中所有的操作符和语法，存在的差异如下：
* WHERE 子句主要用于过滤数据行，而 HAVING 子句主要用于过滤分组，即 HAVING 子句基于分组的聚合值而不是特定行的值来过滤数据，主要用来过滤分组。
* WHERE 子句不可以包含聚合函数，HAVING 子句中的条件可以包含聚合函数。
* HAVING 子句是在数据分组后进行过滤，WHERE 子句会在数据分组前进行过滤。WHERE 子句排除的行不包含在分组中，可能会影响 HAVING 子句基于这些值过滤掉的分组。

实例：根据 dept_id 对 tb_students_info 表中的数据进行分组，并显示学生人数大于1的分组信息，
```sql
mysql> SELECT dept_id,group_concat(name) as names from tb_students_info group by dept_id having count(name)>1;
+---------+---------------+
| dept_id | names         |
+---------+---------------+
|       2 | peng,jun,Feng |
+---------+---------------+
1 row in set (0.00 sec)
```
# 正则表达式查询(REGEXP)
正则表达式通常被用来检索或替换符合某个模式的文本内容，根据指定的匹配模式匹配文中符合要求的特殊字符串。
| 选项       | 说明                                  | 例子                                        | 匹配值实例                  |
| ---------- | ------------------------------------- | ------------------------------------------- | --------------------------- |
| ^          | 匹配文本的开始字符                    | '^b' 匹配以字母 b 开头 的字符串             | book、big、banana、 bike    |
| $          | 匹配文本的结果字符                    | 'st$’ 匹配以 st 结尾的字 符串中等文本       | test、resist、persist       |
| .          | 匹配任何单个字符                      | 'b.t’ 匹配任何 b 和 t 之间有一个字符        | bit、bat、but、bite         |
| *          | 匹配零个或多个在它前面的字符          | 'f*n’ 匹配字符 n 前面有 任意个字符 f        | fn、fan、faan、abcn         |
| +          | 匹配前面的字符 1 次或多次             | ''ba+’ 匹配以 b 开头，后 面至少紧跟一个 a   | ba、bay、bare、battle       |
| <字符串>   | 匹配包含指定字符的文本                | 'fa’                                        | fan、afa、faad              |
| [字符集合] | 匹配字符集合中的任何一个字 符         | '[xz]'匹配 x 或者 z                         | dizzy、zebra、x-ray、 extra |
| [^]        | 匹配不在括号中的任何字符              | '[^abc]’ 匹配任何不包 含 a、b 或 c 的字符串 | desk、fox、f8ke             |
| 字符串{n,} | 匹配前面的字符串至少 n 次             | b{2} 匹配 2 个或更多 的 b                   | bbb、 bbbb、 bbbbbbb        |
| {n,m}      | 匹配前面的字符串至少 n 次， 至多 m 次 | b{2,4} 匹配最少 2 个， 最多 4 个 b          | bbb、 bbbb                  |

**实例1**：字符“^”匹配以特定字符或者字符串开头的文本。查询 dept_name 字段以字母“C”开头的记录
```sql
mysql> select * from tb_departments
    -> where dept_name REGEXP '^C';
+----------+-----------+-----------+
| idept_id | dept_name | dept_type |
+----------+-----------+-----------+
|        1 | Computer  | A         |
|        2 | Chinese   | A         |
+----------+-----------+-----------+
2 rows in set (0.00 sec)
```
**实例2**：用符号“.”代替字符串中的任意一个字符。查询 dept_name 字段值包含字母“o”与字母“y”，且两个字母之间只有一个字母的记录
```sql
mysql> select * from tb_departments
    -> where dept_name regexp 'o.y';
+----------+-----------+-----------+
| idept_id | dept_name | dept_type |
+----------+-----------+-----------+
|        4 | Economy   | B         |
|        5 | History   | A         |
+----------+-----------+-----------+
2 rows in set (0.00 sec)
```

**实例3**：查询 dept_name 字段值包含字符串“in”或者“on”的记录
```sql
mysql> SELECT * FROM tb_departments
    -> WHERE dept_name REGEXP 'in|on';
+---------+-----------+-----------+-----------+
| dept_id | dept_name | dept_call | dept_type |
+---------+-----------+-----------+-----------+
|       3 | Chinese   | 3     | B         |
|       4 | Economy   | 4     | B         |
+---------+-----------+-----------+-----------+
2 rows in set (0.00 sec
```
**实例3**：匹配指定字符串。查询 dept_name 字段值包含字符串“in”或者“on”的记录。
```sql
mysql> SELECT * FROM tb_departments
    -> WHERE dept_name REGEXP 'in|on';
+---------+-----------+-----------+-----------+
| dept_id | dept_name | dept_call | dept_type |
+---------+-----------+-----------+-----------+
|       3 | Chinese   | 33333     | B         |
|       4 | Economy   | 44444     | B         |
+---------+-----------+-----------+-----------+
2 rows in set (0.00 sec)
```
**实例4**:匹配不在指定集合中的任何字符。查询 dept_name 字段值包含字母 a~t 以外的字符的记录。
```sql
mysql> SELECT * FROM tb_departments
    -> WHERE dept_name REGEXP '[^a-t]';
+---------+-----------+-----------+-----------+
| dept_id | dept_name | dept_call | dept_type |
+---------+-----------+-----------+-----------+
|       1 | Computer  | 11111     | A         |
|       4 | Economy   | 44444     | B         |
|       5 | History   | 55555     | B         |
+---------+-----------+-----------+-----------+
3 rows in set (0.00 sec)
```