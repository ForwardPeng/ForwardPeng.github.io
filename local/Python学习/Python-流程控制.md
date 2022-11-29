---
title: Python-流程控制
date: 2020-04-27 11:55:54
categories: 
- 计算机基础
- Python
tags:
- Python
toc: true
---
# assert断言
assert 语句的语法结构为：
>assert 表达式

assert 语句的执行流程可以用 if 判断语句表示，如下所示：
>if 表达式==True:
    程序继续执行
else:
    程序报 AssertionError 错误
```python
#price 为原价，discount 为折扣力度
def apply_discount(price, discount):
    updated_price = price * (1 - discount)
    assert 0 <= updated_price <= price, '折扣价应在 0 和原价之间'
    return updated_price
# 添加了一个 assert 语句，用来检查折后价格，这里要求新折扣价格必须大于等于 0、小于等于原来的价格，否则就抛出异常。
```
assert 的加入可以有效预防程序漏洞，提高程序的健壮性。循环嵌套结构的代码，Python解释器执行的流程为：
* 当外层循环条件为 True 时，则执行外层循环结构中的循环体；
* 外层循环体中包含了普通程序和内循环，当内层循环的循环条件为 True 时会执行此循环中的循环体，直到内层循环条件为 False，跳出内循环；
* 如果此时外层循环的条件仍为 True，则返回第 2 步，继续执行外层循环体，直到外层循环的循环条件为False；
* 当内层循环的循环条件为False，且外层循环的循环条件也为False，则整个嵌套循环才算执行完毕。

# zip函数及其用法
zip() 函数是 Python 内置函数之一，它可以将多个序列（列表、元组、字典、集合、字符串以及 range() 区间构成的列表）“压缩”成一个 zip 对象。所谓“压缩”，其实就是将这些序列中对应位置的元素重新组合，生成一个个新的元组。
>语法格式：zip(iterable, ...)

其中iterable,... 表示多个列表、元组、字典、集合、字符串，甚至还可以为range()区间。
```python
>>> my_list = [11,12,13]
>>> my_tuple = (21,22,23)
>>> print([x for x in zip(my_list,my_tuple)])
[(11, 21), (12, 22), (13, 23)]
```
# reversed函数及用法
对于给定的序列（包括列表、元组、字符串以及 range(n) 区间），该函数可以返回一个逆序序列的迭代器（用于遍历该逆序序列）reserved()函数的语法格式如下：
>reversed(seq)

其中，seq可以是列表，元素，字符串以及range()生成的区间列表。
```python
>>> print([x for x in reversed((1,2,3,4,5))])
[5, 4, 3, 2, 1]
```
# sorted函数及用法
sorted() 作为 Python 内置函数之一，其功能是对序列（列表、元组、字典、集合、还包括字符串）进行排序。sorted()函数的基本语法格式如下：
>list = sorted(iterable, key=None, reverse=False)  

其中，iterable表示指定的序列，key 参数可以自定义排序规则；reverse参数指定以升序（False，默认）还是降序（True）进行排序。sorted()函数会返回一个排好序的列表。

<font color=red>注意，key 参数和 reverse 参数是可选参数，即可以使用，也可以忽略。</font>
```python
>>> a = {4:1,\
...      5:2,\
...      3:3,\
...      2:6,\
...      1:8}
>>> print(sorted(a.items()))
[(1, 8), (2, 6), (3, 3), (4, 1), (5, 2)]
>>> print(sorted(a.values()))
[1, 2, 3, 6, 8]
# 函数默认对序列中元素进行升序排序，通过手动将其reverse参数值改为True，可实现降序排序。
```
sorted()函数时，还可传入一个 key参数，它可以接受一个函数，该函数的功能是指定sorted()函数按照什么标准进行排序，实例如下：
```python
>>> chars = ['sss', 'ss', 'sssss', 's']
>>> print(sorted(chars))
['s', 'ss', 'sss', 'sssss']
>>> print(sorted(chars, key=lambda x:len(x)))
['s', 'ss', 'sss', 'sssss']
```

# 函数可变参数*args及**kwargs
Python函数可变参数*args及**kwargs，先给出标准答案：
* *args是arguments单词缩写，表示任意多个无名参数，是一个tuple，如 (1,2,3,'a','b','c')

* \**kwargs是keyword arguments单词缩写,表示关键字参数，是一个dict，如{'a':1,'b':2,'c':3}，*args参数必须在\**kwargs前
```python
def foo(*args,**kwargs):
    print 'args=',args
    print 'kwargs=',kwargs
    print '*'*20
 
if __name__=='__main__':
    #只传参数*args=(1,2,3)
    foo(1,2,3)

    #只传参数**kwargs=dict(a=1,b=2,c=3)
    foo(a=1,b=2,c=3)
    
    #传入参数*args=(1,2,3)
    #传入参数**kwargs=dict(a=1,b=2,c=3)
    foo(1,2,3,a=1,b=2,c=3)
    
    #传入参数*args=(1,'b','c')
    #传入参数**kwargs=dict(a=1,b='b',c='c')
    foo(1,'b','c',a=1,b='b',c='c')
输出：    
args= (1, 2, 3)
kwargs= {}
********************
args= ()
kwargs= {'a': 1, 'c': 3, 'b': 2}
********************
args= (1, 2, 3)
kwargs= {'a': 1, 'c': 3, 'b': 2}
********************
args= (1, 'b', 'c')
kwargs= {'a': 1, 'c': 'c', 'b': 'b'}
********************
```
## 逆向参数收集
在列表、元组前添加 *，在字典前添加 **。
```python
>>> def test(a, b):
...     print(a)
...     print(b)
... 
>>> vals_1 = [10,20]
>>> test(*vals_1)
10
20
>>> vals_2 = {'a':10, 'b':20}
>>> test(*vals_2)
a
b
>>> test(**vals_2)
10
20
```
# None(空值)及其用法
有一个特殊的常量None（N 必须大写）。和False不同，它不表示0，也不表示空字符串，而表示没有值，也就是空值。

这里的空值并不代表空对象，即None和[]、“” 不同：
```python
>>> None is []
False
>>> None is ""
False
>>> type(None)
<class 'NoneType'>
```
<font color=red>None 是 NoneType 数据类型的唯一值（其他编程语言可能称这个值为 null、nil 或 undefined），也就是说，我们不能再创建其它 NoneType 类型的变量，但是可以将 None 赋值给任何变量。如果希望变量中存储的东西不与任何其它值混淆，就可以使用 None。</font>

# partial偏函数及其用法
简单的理解偏函数，它是对原始函数的二次封装，是将现有函数的部分参数预先绑定为指定值，从而得到一个新的函数，该函数就称为偏函数。相比原函数，偏函数具有较少的可变参数，从而降低了函数调用的难度。

定义偏函数，需使用 partial 关键字（位于 functools 模块中），其语法格式如下：
* 偏函数名 = partial(func, *args, **kwargs)

其中，func 指的是要封装的原函数，*args 和 **kwargs 分别用于接收无关键字实参和关键字实参。
```python
>>> def mod( n, m ):
...   return n % m
... 
>>> mod_by_100 = partial( mod, 100 )
>>> print(mod( 100, 7 ))
2
>>> print(mod_by_100( 7 ))
2
```
# 变量作用域(全局变量和局部变量)
作用域（Scope），就是变量的有效范围，就是变量可以在哪个范围以内使用。有些变量可以在整段代码的任意位置使用，有些变量只能在函数内部使用，有些变量只能在 for 循环内部使用。变量的作用域由变量的定义位置决定，在不同位置定义的变量，它的作用域是不一样的。本节我们只讲解两种变量，局部变量和全局变量。
## 局部变量
在函数内部定义的变量，它的作用域也仅限于函数内部，出了函数就不能使用了，我们将这样的变量称为局部变量（Local Variable）。

要知道，当函数被执行时，Python 会为其分配一块临时的存储空间，所有在函数内部定义的变量，都会存储在这块空间中。而在函数执行完毕后，这块临时存储空间随即会被释放并回收，该空间中存储的变量自然也就无法再被使用。
```python
>>> def demo():
...     add = "http://c.biancheng.net/python/"
...     print("函数内部 add =",add)
... 
>>> demo()
函数内部 add = http://c.biancheng.net/python/
>>> print("函数外部 add =",add)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'add' is not defined
```
## 全局变量
在函数内部定义变量，Python 还允许在所有函数的外部定义变量，这样的变量称为全局变量（Global Variable）。和局部变量不同，全局变量的默认作用域是整个程序，即全局变量既可以在各个函数的外部使用，也可以在各函数内部使用。

定义全局变量的方式有以下2种：
* 在函数体外定义的变量，一定是全局变量，如：
```python
>>> add = "http://c.biancheng.net/shell/"
>>> def text():
...     print("函数体内访问：",add)
... 
>>> text()
函数体内访问： http://c.biancheng.net/shell/
>>> print('函数体外访问：',add)
函数体外访问： http://c.biancheng.net/shell/
```
* 在函数体内定义全局变量。即使用 global关键字对变量进行修饰后，该变量就会变为全局变量
```python
>>> def text():
...     global add
...     add= "http://c.biancheng.net/java/"
...     print("函数体内访问：",add)
... 
>>> text()
函数体内访问： http://c.biancheng.net/java/
>>> print(add)
http://c.biancheng.net/java/
```
<font color=red>注意，在使用 global 关键字修饰变量名时，不能直接给变量赋初值，否则会引发语法错误。</font>
## 获取指定作用域范围中的变量

**1.globals()函数** 

globals()函数为Python的内置函数，它可以返回一个包含全局范围内所有变量的字典，该字典中的每个键值对，键为变量名，值为该变量的值。globals()函数返回的字典中，会默认包含有很多变量。
```python
>>> globals()['Pyname'] = "Python入门教程"
>>> print(Pyname)
Python入门教程
```
**2.locals()函数**

locals()函数也是Python内置函数之一，通过调用该函数，我们可以得到一个包含当前作用域内所有变量的字典。这里所谓的“当前作用域”指的是，在函数内部调用 locals() 函数，会获得包含所有局部变量的字典；而在全局范文内调用 locals() 函数，其功能和 globals() 函数相同。
```python
>>> Pyname = "Python教程"
>>> Pyadd = "http://c.biancheng.net/python/"
>>> def text():
...     #局部变量
...     Shename = "shell教程"
...     Sheadd= "http://c.biancheng.net/shell/"
...     print("函数内部的 locals:")
...     print(locals())
... 
>>> text()
函数内部的 locals:
{'Sheadd': 'http://c.biancheng.net/shell/', 'Shename': 'shell教程'}
>>> print("函数外部的 locals:")
函数外部的 locals:
>>> print(locals())
```
**3.vars(object)**

vars() 函数也是 Python 内置函数，其功能是返回一个指定 object 对象范围内所有变量组成的字典。如果不传入object 参数，vars() 和 locals() 的作用完全相同。

# 同名的全局变量
解决方式：

方式1：在函数中要定义局部变量时不要与全局变量同名， 即在numCheck( )中定义的局部变量换个名。

方式2：进入函数时先定义与全局变量同名的局部变量，就不会报错了，但是这样就没有达到引用全局变量a之后再定义与全局变量同名的局部变量a 的目的，所以引入方式3。

方式3：这里涉及到全局变量和局部变量的区分，如果想使用全局变量a之后再使用同名的局部变量a，就应该是把方法和变量定义在类里。通过类的成员变量去引用全局变量。
# 局部函数及用法（包含nonlocal关键字）
```python
#全局函数
def outdef ():
    name = "所在函数中定义的 name 变量"
    #局部函数
    def indef():
        nonlocal name
        print(name)
        #修改name变量的值
        name = "局部函数中定义的 name 变量"
    indef()
#调用全局函数
outdef()
```
由于这里的name变量也是局部变量，因此前面章节讲解的globals() 函数或者 globals关键字，并不适用于解决此问题。这里可以使用Python提供的 nonlocal关键字

# 函数的高级用法
Python 函数还支持赋值、作为其他函数的参数以及作为其他函数的返回值。
```python
def my_def():
    print("正在执行 my_def 函数")
other = my_def
other()
正在执行 my_def 函数
# Python 还支持函数的返回值也为函数
def my_def ():
    #局部函数
    def indef():
        print("调用局部函数")
    #调用局部函数
    return indef
other_def = my_def()
#调用局部的 indef() 函数
other_def()
调用局部函数
```
# Python的闭包
闭包，又称闭包函数或者闭合函数，其实和前面讲的嵌套函数类似，不同之处在于，闭包中外部函数返回的不是一个具体的值，而是一个函数。一般情况下，返回的函数会赋值给一个变量，这个变量可以在后面被继续执行调用。
```python
#闭包函数，其中 exponent 称为自由变量
def nth_power(exponent):
    def exponent_of(base):
        return base ** exponent
    return exponent_of # 返回值是 exponent_of 函数
square = nth_power(2) # 计算一个数的平方
cube = nth_power(3) # 计算一个数的立方
print(square(2))  # 计算 2 的平方
print(cube(2)) # 计算 2 的立方
```
<font color=red>函数开头需要做一些额外工作，当需要多次调用该函数时，如果将那些额外工作的代码放在外部函数，就可以减少多次调用导致的不必要开销，提高程序的运行效率。</font>

## 闭包的__closure__属性
记录着自由变量的地址。当闭包被调用时，系统就会根据该地址找到对应的自由变量，完成整体的函数调用。类型是一个元组，这表明闭包可以支持多个自由变量的形式。
# lambda表达式(匿名函数)及其用法
lambda 表达式，又称匿名函数，常用来表示内部仅包含 1 行表达式的函数。如果一个函数的函数体仅有 1 行表达式，则该函数就可以用 lambda 表达式来代替。lambda 表达式的语法格式如下：
>name = lambda [list] : 表达式

其中，定义 lambda 表达式，必须使用 lambda 关键字；[list] 作为可选参数，等同于定义函数是指定的参数列表；value 为该表达式的名称。
```python
>>> add = lambda x,y: x + y
>>> print(add(3,4))
7
```
lamba 表达式具有以下  2 个优势：
* 对于单行函数，使用 lambda 表达式可以省去定义函数的过程，让代码更加简洁；
* 对于不需要多次复用的函数，使用 lambda 表达式可以在用完之后立即释放，提高程序执行的性能。
# eval()和exec()函数
eval() 和 exec() 函数的功能是相似的，都可以执行一个字符串形式的 Python 代码（代码以字符串的形式提供），相当于一个 Python 的解释器。二者不同之处在于，eval() 执行完要返回结果，而 exec() 执行完不返回结果。
eval() 函数的语法格式为：
>eval(source, globals=None, locals=None, /)

而 exec() 函数的语法格式如下：
>exec(source, globals=None, locals=None, /)

二者的语法格式除了函数名，其他都相同，其中各个参数的具体含义如下：
* expression：这个参数是一个字符串，代表要执行的语句 。该语句受后面两个字典类型参数 globals 和 locals 的限制，只有在 globals 字典和 locals 字典作用域内的函数和变量才能被执行。
* globals：这个参数管控的是一个全局的命名空间，即 expression 可以使用全局命名空间中的函数。如果只是提供了 globals 参数，而没有提供自定义的 __builtins__，则系统会将当前环境中的 * __builtins__ 复制到自己提供的 globals 中，然后才会进行计算；如果连 globals 这个参数都没有被提供，则使用 Python 的全局命名空间。
* locals：这个参数管控的是一个局部的命名空间，和 globals 类似，当它和 globals 中有重复或冲突时，以 locals 的为准。如果 locals 没有被提供，则默认为 globals。

<font color=red>注意，__builtins__ 是 Python 的内建模块，平时使用的 int、str、abs 都在这个模块中。通过 
print(dic["__builtins__"]) 语句可以查看 __builtins__ 所对应的 value。</font>
```python
>>> a=10
>>> b=20
>>> c=30
>>> g={'a':6, 'b':8} #定义一个字典
>>> t={'b':100, 'c':10} #定义一个字典
>>> print(eval('a+b+c', g, t)) #定义一个字典 116
116
```

## exec()和eval()区别 
```python
a = 1
exec("a = 2") #相当于直接执行 a=2
print(a)
2
a = exec("2+3") #相当于直接执行 2+3，但是并没有返回值，a 应为 None
print(a)
None
a = eval('2+3') #执行 2+3，并把结果返回给 a
print(a)
5
```
exec() 中最适合放置运行后没有结果的语句，而 eval() 中适合放置有结果返回的语句。
## eval() 和 exec() 函数的应用场景
客户端向服务端发送一段字符串代码，服务端无需关心具体的内容，直接跳过 eval() 或 exec() 来执行，这样的设计会使服务端与客户端的耦合度更低，系统更易扩展。
<font color=red>注意的是，在使用 eval() 或是 exec() 来处理请求代码时，函数 eval() 和 exec() 常常会被黑客利用，成为可以执行系统级命令的入口点，进而来攻击网站。解决方法是：通过设置其命名空间里的可执行函数，来限制 eval() 和 exec() 的执行范围。</font>

第一个参数是字符串，而字符串的内容一定要是可执行的代码。在编写代码时，一般会使 repr() 数来生成动态的字符串，再传入到 eval() 或 exec() 函数内，实现动态执行代码的功能。

# 函数式编程（map()、filter()和reduce()）详解
函数式编程的优点，主要在于其纯函数和不可变的特性使程序更加健壮，易于调试和测试；缺点主要在于限制多，难写。
注意，纯粹的函数式编程语言（比如 Scala），其编写的函数中是没有变量的，因此可以保证，只要输入是确定的，输出就是确定的；而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，同样的输入，可能得到不同的输出。

Python 允许使用变量，所以它并不是一门纯函数式编程语言。Python 仅对函数式编程提供了部分支持，主要包括 map()、filter() 和 reduce() 这 3 个函数，它们通常都结合 lambda 匿名函数一起使用。接下来就对这 3 个函数的用法做逐一介绍。
## map()函数
map()函数的基本语法格式如下：
>map(function, iterable)

其中，function参数表示要传入一个函数，其可以是内置函数、自定义函数或者 lambda 匿名函数；iterable 表示一个或多个可迭代对象，可以是列表、字符串等。map()函数的功能是对可迭代对象中的每个元素，都调用指定的函数，并返回一个map对象。
>注意，该函数返回的是一个map对象，不能直接输出，可以通过for循环或者 list() 函数来显示。
```python
>>> listDemo1 = [1, 2, 3, 4, 5]
>>> listDemo2 = [3, 4, 5, 6, 7]
>>> new_list = map(lambda x,y: x + y, listDemo1,listDemo2)
>>> print(list(new_list))
[4, 6, 8, 10, 12]
```
<font color=red>注意，由于 map() 函数是直接由用 C 语言写的，运行时不需要通过 Python 解释器间接调用，并且内部做了诸多优化，所以相比其他方法，此方法的运行效率最高。</font>

## filter()函数
filter()函数的基本语法格式如下：
>filter(function, iterable)

此格式中，funcition参数表示要传入一个函数，iterable表示一个可迭代对象。filter()函数的功能是对 iterable中的每个元素，都使用 function函数判断，并返回 True 或者 False，最后将返回 True 的元素组成一个新的可遍历的集合。
```python
>>> new_list = map(lambda x,y: x-y>0,[3,5,6],[1,5,8] )
>>> print(list(new_list))
[True, False, False]
```
## reduce()函数
reduce()函数通常用来对一个集合做一些累积操作，其基本语法格式为：
>reduce(function, iterable)

其中，function规定必须是一个包含2个参数的函数；iterable 表示可迭代对象。注意，由于 reduce()函数在 Python 3.x中已经被移除，放入了 functools模块，因此在使用该函数之前，需先导入functools模块。
```python
>>> listDemo = [1, 2, 3, 4, 5]
>>> product = functools.reduce(lambda x, y: x * y, listDemo)
>>> print(product) # 叠乘
120
```
# 函数注解（Function Annotations）
函数注解语法 可以让你在定义函数的时候对参数和返回值添加注解：
```python
def foobar(a: int, b: "it's b", c: str = 5) -> tuple:
    return a, b, c
# foobar.__annotations__获取参数注解
```
* a: int 这种是注解参数
* c: str = 5 是注解有默认值的参数
* -> tuple 是注解返回值。

基于注解可以实现参数类型检查的装饰器
```python
# coding: utf8
import collections
import functools
import inspect

def check(func):
    msg = ('Expected type {expected!r} for argument {argument}, '
           'but got type {got!r} with value {value!r}')
    # 获取函数定义的参数
    sig = inspect.signature(func)
    parameters = sig.parameters          # 参数有序字典
    arg_keys = tuple(parameters.keys())   # 参数名称

    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        CheckItem = collections.namedtuple('CheckItem', ('anno', 'arg_name', 'value'))
        check_list = []

        # collect args   *args 传入的参数以及对应的函数参数注解
        for i, value in enumerate(args):
            arg_name = arg_keys[i]
            anno = parameters[arg_name].annotation
            check_list.append(CheckItem(anno, arg_name, value))

        # collect kwargs  **kwargs 传入的参数以及对应的函数参数注解
        for arg_name, value in kwargs.items():
           anno = parameters[arg_name].annotation
           check_list.append(CheckItem(anno, arg_name, value))

        # check type
        for item in check_list:
            if not isinstance(item.value, item.anno):
                error = msg.format(expected=item.anno, argument=item.arg_name,
                                   got=type(item.value), value=item.value)
                raise TypeError(error)

        return func(*args, **kwargs)

    return wrapper
@check
def foobar(a: int, b:str, c: float=3.2) -> tuple:
    return a, b, c
>>> foobar(1, 'b')
(1, 'b', 3.2)
>>> foobar('a', 'b')
...
TypeError: Expected type <class 'int'> for argument a, but got type <class 'str'> with value 'a
```



