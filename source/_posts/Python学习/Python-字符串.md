---
title: Python-字符串
date: 2020-04-27 11:06:19
categories: 
- 计算机基础
- Python
tags:
- Python
toc: true
---
# split()分割字符串
split() 方法可以实现将一个字符串按照指定的分隔符切分成多个子串，这些子串会被保存到列表中（不包含分隔符），作为方法的返回值反馈回来。该方法的基本语法格式如下：
>str.split(sep,maxsplit)

此方法中各部分参数的含义分别是：
* str：表示要进行分割的字符串；
* sep：用于指定分隔符，可以包含多个字符。此参数默认为 None，表示所有空字符，包括空格、换行符“\n”、制表符“\t”等。
* maxsplit：可选参数，用于指定分割的次数，最后列表中子串的个数最多为 maxsplit+1。如果不指定或者指定为 -1，则表示分割次数没有限制。'

# join()方法，合并字符串
join() 方法的语法格式如下：
>newstr = str.join(iterable)

此方法中各参数的含义如下：
* newstr：表示合并后生成的新字符串；
* str：用于指定合并时的分隔符；
* iterable：做合并操作的源字符串数据，允许以列表、元组等形式提供。

# 统计字符串出现的次数
count 方法用于检索指定字符串在另一字符串中出现的次数，如果检索的字符串不存在，则返回 0，否则返回出现的次数。count方法的语法格式如下：
>str.count(sub[,start[,end]])

此方法中，各参数的具体含义如下：
* str：表示原字符串；
* sub：表示要检索的字符串；
* start：指定检索的起始位置，也就是从什么位置开始检测。如果不指定，默认从头开始检索；
* end：指定检索的终止位置，如果不指定，则表示一直检索到结尾。

```python
>>> str = "c.biancheng.net"
>>> str.count('.')
```
# find()方法，检测字符串中是否包含某子串
find() 方法的语法格式如下：
>str.find(sub[,start[,end]])

此格式中各参数的含义如下：
* str：表示原字符串；
* sub：表示要检索的目标字符串；
* start：表示开始检索的起始位置。如果不指定，则默认从头开始检索；
* end：表示结束检索的结束位置。如果不指定，则默认一直检索到结尾。

```python
>>> str.find('.') # 首次出现
1
>>> str.find('.', 2) # 起始索引位置
11
>>> str.find('.', 2,4) # 区间
-1
```
<font color=red>注意，Python 还提供了 rfind() 方法，与 find() 方法最大的不同在于，rfind()是从字符串右边开始检索。</font>

# index()方法：检测字符串中是否包含某子串
index() 方法也可以用于检索是否包含指定的字符串，不同之处在于，当指定的字符串不存在时，index() 方法会抛出异常。index()方法的语法格式如下：
>str.index(sub[,start[,end]])

此格式中各参数的含义分别是：
* str：表示原字符串；
* sub：表示要检索的子字符串；
* start：表示检索开始的起始位置，如果不指定，默认从头开始检索；
* end：表示检索的结束位置，如果不指定，默认一直检索到结尾。

```python
>>> str.index('z')
Traceback (most recent call last):
  File "<pyshell#49>", line 1, in <module>
    str.index('z')
ValueError: substring not found
>>> str.rindex('.')
11
```
# 字符串对齐方法(ljust()、rjust()和center())
## ljust()方法
ljust() 方法的功能是向指定字符串的右侧填充指定字符，从而达到左对齐文本的目的。ljust() 方法的基本格式如下：
>S.ljust(width[, fillchar])

其中各个参数的含义如下：
* S：表示要进行填充的字符串；
* width：表示包括 S 本身长度在内，字符串要占的总长度；
* fillchar：作为可选参数，用来指定填充字符串时所用的字符，默认情况使用空格。
```python
>>> S = 'http://c.biancheng.net/python/'
>>> addr = 'http://c.biancheng.net'
>>> print(S.ljust(35))
http://c.biancheng.net/python/      
>>> print(addr.ljust(35))
http://c.biancheng.net 
# 该输出结果中除了明显可见的网址字符串外，其后还有空格字符存在，每行一共 35 个字符长度。
```
## rjust()方法
rjust() 和 ljust() 方法类似，唯一的不同在于，rjust() 方法是向字符串的左侧填充指定字符，从而达到右对齐文本的目的。rjust() 方法的基本格式如下：
>S.rjust(width[, fillchar])
```python
>>> print(S.rjust(35))
     http://c.biancheng.net/python/
>>> print(addr.rjust(35))
             http://c.biancheng.net
```
## center()方法
center() 字符串方法与 ljust() 和 rjust() 的用法类似，但它让文本居中，而不是左对齐或右对齐。center() 方法的基本格式如下：
>S.center(width[, fillchar])

其中各个参数的含义和 ljust()、rjust()方法相同。
# startswith()和endswith()方法
## startswith()方法
startswith() 方法用于检索字符串是否以指定字符串开头，如果是返回 True；反之返回 False。此方法的语法格式如下：
>str.startswith(sub[,start[,end]])

此格式中各个参数的具体含义如下：
* str：表示原字符串；
* sub：要检索的子串；
* start：指定检索开始的起始位置索引，如果不指定，则默认从头开始检索；
* end：指定检索的结束位置索引，如果不指定，则默认一直检索在结束。
```python
>>> str = "c.biancheng.net"
>>> str.startswith("http")
False
```
## endswith()方法
endswith() 方法用于检索字符串是否以指定字符串结尾，如果是则返回 True；反之则返回 False。该方法的语法格式如下：
>str.endswith(sub[,start[,end]])

此格式中各参数的含义如下：
* str：表示原字符串；
* sub：表示要检索的字符串；
* start：指定检索开始时的起始位置索引（字符串第一个字符对应的索引值为 0），如果不指定，默认从头开始检索。
* end：指定检索的结束位置索引，如果不指定，默认一直检索到结束。

# 字符串大小写转换（3种）函数及用法
## title()方法
title() 方法用于将字符串中每个单词的首字母转为大写，其他字母全部转为小写，转换完成后，此方法会返回转换得到的字符串。如果字符串中没有需要被转换的字符，此方法会将字符串原封不动地返回。title()方法的语法格式如下：
>str.title()

其中，str 表示要进行转换的字符串。
```python
>>> str = "c.biancheng.net"
>>> str.title()
'C.Biancheng.Net'
```
## lower()方法
lower() 方法用于将字符串中的所有大写字母转换为小写字母，转换完成后，该方法会返回新得到的字符串。如果字符串中原本就都是小写字母，则该方法会返回原字符串。lower() 方法的语法格式如下：
>str.lower()
## upper()方法
upper() 的功能和 lower()方法恰好相反，它用于将字符串中的所有小写字母转换为大写字母，和以上两种方法的返回方式相同，即如果转换成功，则返回新字符串；反之，则返回原字符串。upper()方法的语法格式如下：
>str.upper()

# 去除字符串中空格（删除指定字符）的3种方法
特殊字符，指的是制表符（\t）、回车符（\r）、换行符（\n）等
字符串变量提供了 3 种方法来删除字符串中多余的空格和特殊字符，它们分别是：
* strip()：删除字符串前后（左右两侧）的空格或特殊字符。
* lstrip()：删除字符串前面（左边）的空格或特殊字符。
* rstrip()：删除字符串后面（右边）的空格或特殊字符。

## strip()方法
strip() 方法用于删除字符串左右两个的空格和特殊字符，该方法的语法格式为：
>str.strip([chars])

其中，str 表示原字符串，[chars] 用来指定要删除的字符，可以同时指定多个，如果不手动指定，则默认会删除空格以及制表符、回车符、换行符等特殊字符。

## lstrip()方法
lstrip() 方法用于去掉字符串左右的空格和特殊字符。该方法的语法格式如下：
>str.lstrip([chars])

其中，str和chars参数的含义，分别同 strip()语法格式中的str和chars完全相同。
```python
>>> str = "  c.biancheng.net \t\n\r"
>>> str.lstrip()
'c.biancheng.net \t\n\r'
```
## rstrip()方法
rstrip() 方法用于删除字符串右侧的空格和特殊字符，其语法格式为：
>str.rstrip([chars])

str和chars参数的含义和前面2种方法语法格式中的参数完全相同。
```python
>>> str = "  c.biancheng.net \t\n\r"
>>> str.rstrip()
'  c.biancheng.net'
```
# format()格式化输出方法详解
format()方法的语法格式如下：
>str.format(args)

此方法中，str用于指定字符串的显示样式；args用于指定要进行格式转换的项，如果有多项，之间有逗号进行分割。使用{}和：来指定占位符，其完整的语法格式为：
>{ [index][ : [ [fill] align] [sign] [#] [width] [.precision] [type] ] }

格式中用[]括起来的参数都是可选参数，即可以使用，也可以不使用。各个参数的含义如下：
* index：指定：后边设置的格式要作用到 args 中第几个数据，数据的索引值从 0 开始。如果省略此选项，则会根据 args 中数据的先后顺序自动分配。
* fill：指定空白处填充的字符。注意，当填充字符为逗号(,)且作用于整数或浮点数时，该整数（或浮点数）会以逗号分隔的形式输出，例如（1000000会输出 1,000,000）。
* align：指定数据的对齐方式，具体的对齐方式如下表所示。

| align | 含义                                                                   |
| ----- | ---------------------------------------------------------------------- |
| <     | 数据左对齐                                                             |
| >     | 数据右对齐                                                             |
| =     | 数据右对齐，同时将符号放置在填充内容的最左侧，该选项只对数字类型有效。 |
| ^     | 数据居中，此选项需和 width 参数一起使用。                              |

* sign：指定有无符号数，此参数的值以及对应的含义如下表：

| sign参数 | 含义                                                                                                    |
| -------- | ------------------------------------------------------------------------------------------------------- |
| +        | 正数前加正号，负数前加负号。                                                                            |
| -        | 正数前不加正号，负数前加负号。                                                                          |
| 空格     | 正数前加空格，负数前加负号。                                                                            |
| \#       | 对于二进制数、八进制数和十六进制数，使用此参数，各进制数前会分别显示 0b、0o、0x前缀；反之则不显示前缀。 |

* width：指定输出数据时所占的宽度。
* .precision：指定保留的小数位数。
* type：指定输出数据的具体类型，如下表：
* 
| type类型值 | 含义                                                  |
| ---------- | ----------------------------------------------------- |
| s          | 对字符串类型格式化。                                  |
| d          | 十进制整数。                                          |
| c          | 将十进制整数自动转换成对应的 Unicode 字符。           |
| e 或者 E   | 转换成科学计数法后，再格式化输出。                    |
| g 或 G     | 自动在 e 和 f（或 E 和 F）中切换。                    |
| b          | 将十进制数自动转换成二进制表示，再格式化输出。        |
| o          | 将十进制数自动转换成八进制表示，再格式化输出。        |
| x 或者 X   | 将十进制数自动转换成十六进制表示，再格式化输出。      |
| f 或者 F   | 转换为浮点数（默认小数点后保留 6 位），再格式化输出。 |
| %          | 显示百分比（默认显示小数点后 6 位）。                 |
```python
>>> print("货币形式：{:,d}".format(1000000))
货币形式：1,000,000
>>> #科学计数法表示
... print("科学计数法：{:E}".format(1200.12))
科学计数法：1.200120E+03
>>> #以十六进制表示
... print("100的十六进制：{:#x}".format(100))
100的十六进制：0x64
>>> #输出百分比形式
... print("0.01的百分比表示：{:.0%}".format(0.01))
0.01的百分比表示：1%
```
# encode()和decode()方法：字符串编码转换
Python 中，有2种常用的字符串类型，分别为str和 bytes 类型，其中 str 用来表示 Unicode 字符，bytes 用来表示二进制数据。str 类型和 bytes 类型之间就需要使用 encode() 和 decode() 方法进行转换。
>Python 3.x 默认采用 UTF-8 编码格式，有效地解决了中文乱码的问题。

## encode()方法
encode() 方法为字符串类型（str）提供的方法，用于将 str 类型转换成 bytes 类型，这个过程也称为“编码”。encode()方法的语法格式如下：
>str.encode([encoding="utf-8"][,errors="strict"])

<font color=red>注意，格式中用 [] 括起来的参数为可选参数，也就是说，在使用此方法时，可以使用 [] 中的参数，也可以不使用。</font>

errors = "strict"指定错误处理方式，其可选择值可以是：
* strict：遇到非法字符就抛出异常。
* ignore：忽略非法字符。
* replace：用“？”替换非法字符。
* xmlcharrefreplace：使用 xml的字符引用。该参数的默认值为 strict。
## decode()方法
和 encode() 方法正好相反，decode() 方法用于将 bytes 类型的二进制数据转换为 str 类型，这个过程也称为“解码”。decode()方法的语法格式如下：
>bytes.decode([encoding="utf-8"][,errors="strict"])
# dir()和help()帮助函数
dir() 函数用来列出某个类或者某个模块中的全部内容，包括变量、方法、函数和类等，它的用法为：
>dir(obj)

obj表示要查看的对象。obj 可以不写，此时 dir() 会列出当前范围内的变量、方法和定义的类型。

Python help() 函数用来查看某个函数或者模块的帮助文档，它的用法为：
>help(obj)

obj表示要查看的对象。obj可以不写，此时help()会进入帮助子程序。
```python 
>>> help(str.lower)
Help on method_descriptor:

lower(self, /)
    Return a copy of the string converted to lowercase.
```