---
title: Python-类特殊成员
date: 2020-04-28 23:27:44
categories: 
- 计算机基础
- Python
tags:
- Python
toc: true
---
# __new__()方法
__new__() 是一种负责创建类实例的静态方法，它无需使用 staticmethod 装饰器修饰，且该方法会优先__init__() 初始化方法被调用。一般情况下，覆写__new__()的实现将会使用合适的参数调用其超类的 super().__new__()，并在返回之前修改实例。
```python
class demoClass:
    instances_created = 0
    def __new__(cls,*args,**kwargs):
        print("__new__():",cls,args,kwargs)
        instance = super().__new__(cls)
        instance.number = cls.instances_created
        cls.instances_created += 1
        return instance
    def __init__(self,attribute):
        print("__init__():",self,attribute)
        self.attribute = attribute
test1 = demoClass("abc")
test2 = demoClass("xyz")
print(test1.number,test1.instances_created)
print(test2.number,test2.instances_created)
# 输出结果
#__new__(): <class '__main__.demoClass'> ('abc',) {}
#__init__(): <__main__.demoClass object at 0x0000026FC0DF8080> abc
#__new__(): <class '__main__.demoClass'> ('xyz',) {}
#__init__(): <__main__.demoClass object at 0x0000026FC0DED358> xyz
#0 2
#1 2
```
# __repr__()方法：显示属性
__repr__() 会返回和调用者有关的 “类名+object at+内存地址”信息。当然，我们还可以通过在类中重写这个方法，从而实现当输出实例化对象时，输出我们想要的信息。实例：
```python
class CLanguage:
    def __init__(self):
        self.name = "C语言中文网"
        self.add = "http://c.biancheng.net"
    def __repr__(self):
        return "CLanguage[name="+ self.name +",add=" + self.add +"]"
clangs = CLanguage()
print(clangs)
# CLanguage[name=C语言中文网,add=http://c.biancheng.net]
```
__repr__() 方法是类的实例化对象用来做“自我介绍”的方法，默认情况下，它会返回当前对象的“类名+object at+内存地址”，而如果对该方法进行重写，可以为其制作自定义的自我描述信息。
# __dir__()用法
提到了dir() 函数，通过此函数可以某个对象拥有的所有的属性名和方法名，该函数会返回一个包含有所有属性名和方法名的有序列表。
# __dict__属性：查看对象内部所有属性名和属性值组成的字典
便用户查看类中包含哪些属性，Python 类提供了__dict__属性。需要注意的一点是，该属性可以用类名或者类的实例对象来调用，用类名直接调用 __dict__，会输出该由类中所有类属性组成的字典；而使用类的实例对象调用 __dict__，会输出由类中所有实例属性组成的字典。对于具有继承关系的父类和子类来说，父类有自己的__dict__，同样子类也有自己的__dict__，它不会包含父类的__dict__

# setattr()、getattr()、hasattr()函数用法
## hasattr()函数
hasattr() 函数用来判断某个类实例对象是否包含指定名称的属性或方法。该函数的语法格式如下：
>hasattr(obj, name)

无论是属性名还是方法名，都在 hasattr() 函数的匹配范围内。因此，我们只能通过该函数判断实例对象是否包含该名称的属性或方法，但不能精确判断，该名称代表的是属性还是方法。

## getattr()函数
getattr() 函数获取某个类实例对象中指定属性的值，该函数只会从类对象包含的所有属性中进行查找。
getattr() 函数的语法格式如下：
>getattr(obj, name[, default])

其中，obj 表示指定的类实例对象，name 表示指定的属性名，而 default 是可选参数，用于设定该函数的默认返回值，即当函数查找失败时，如果不指定 default 参数，则程序将直接报 AttributeError 错误，反之该函数将返回 default 指定的值。

## setattr()函数
setattr() 函数的功能相对比较复杂，它最基础的功能是修改类实例对象中的属性值。其次，它还可以实现为实例对象动态添加属性或者方法。setattr() 函数的语法格式如下：
>setattr(obj, name, value)
```python
def say(self):
    print("我正在学Python")
class CLanguage:
    def __init__ (self):
        self.name = "C语言中文网"
        self.add = "http://c.biancheng.net"
clangs = CLanguage()
print(clangs.name)
print(clangs.add)
setattr(clangs,"name",say)
clangs.name(clangs)
#程序运行结果为：
#C语言中文网
#http://c.biancheng.net
#我正在学Python
```
# issubclass和isinstance函数：检查类型
Python 提供了如下两个函数来检查类型：
* issubclass(cls, class_or_tuple)：检查 cls 是否为后一个类或元组包含的多个类中任意类的子类。
* isinstance(obj, class_or_tuple)：检查 obj 是否为后一个类或元组包含的多个类中任意类的对象。

区别只是 issubclass() 的第一个参数是类名，而 isinstance() 的第一个参数是变量，这也与两个函数的意义对应：issubclass 用于判断是否为子类，而 isinstance() 用于判断是否为该类或子类的实例。

Python为所有类都提供了一个 __bases__ 属性，通过该属性可以查看该类的所有直接父类，该属性返回所有直接父类组成的元组。

# __call__()方法
功能类似于在类中重载 () 运算符，使得类实例对象可以像调用普通函数那样，以“对象名()”的形式使用。
>Python 中，凡是可以将 () 直接应用到自身并执行，都称为可调用对象。可调用对象包括自定义的函数、Python 内置函数以及本节所讲的类实例对象。

hasattr()的功能是查找类的实例对象中是否包含指定名称的属性或者方法，但该函数有一个缺陷，即它无法判断该指定的名称，到底是类属性还是类方法。
```python
class CLanguage:
    def __init__ (self):
        self.name = "C语言中文网"
        self.add = "http://c.biancheng.net"
    def say(self):
        print("我正在学Python")
clangs = CLanguage()
if hasattr(clangs,"name"):
    print(hasattr(clangs.name,"__call__"))
print("**********")
if hasattr(clangs,"say"):
    print(hasattr(clangs.say,"__call__"))
执行结果：
    False  
    *********
    True
# 由于name是类属性，它没有以__call__为名的__call__()方法；而 say是类方法，它是可调用对象，因此它有__call__()方法。
```
# 运算符重载
Python 类支持对哪些方法进行重载呢？，列出了 Python 中常用的可重载的运算符，以及各自的含义。
| 重载运算符                                     | 含义                                                                                                                                                                          |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| __new__                                        | 创建类，在 __init__ 之前创建对象                                                                                                                                              |
| __init__                                       | 类的构造函数，其功能是创建类对象时做初始化工作。                                                                                                                              |
| __del__                                        | 析构函数，其功能是销毁对象时进行回收资源的操作                                                                                                                                |
| __add__                                        | 加法运算符 +，当类对象 X 做例如 X+Y 或者 X+=Y 等操作，内部会调用此方法。但如果类中对 __iadd__ 方法进行了重载，则类对象 X 在做 X+=Y 类似操作时，会优先选择调用 __iadd__ 方法。 |
| __radd__                                       | 当类对象 X 做类似 Y+X 的运算时，会调用此方法。                                                                                                                                |
| __iadd__                                       | 重载 += 运算符，也就是说，当类对象 X 做类似 X+=Y 的操作时，会调用此方法。                                                                                                     |
| __or__                                         | “或”运算符                                                                                                                                                                    | ，如果没有重载 __ior__，则在类似 X | Y、X     | =Y 这样的语句中，“或”符号生效        |
| __repr__，__str__                              | 格式转换方法，分别对应函数 repr(X)、str(X)                                                                                                                                    |
| __call__                                       | 函数调用，类似于 X(*args, **kwargs) 语句                                                                                                                                      |
| __getattr__                                    | 点号运算，用来获取类属性                                                                                                                                                      |
| __setattr__                                    | 属性赋值语句，类似于 X.any=value                                                                                                                                              |
| __delattr__                                    | 删除属性，类似于 del X.any                                                                                                                                                    |
| __getattribute__                               | 获取属性，类似于 X.any                                                                                                                                                        |
| __getitem__                                    | 索引运算，类似于 X[key]，X[i:j]                                                                                                                                               |
| __setitem__                                    | 索引赋值语句，类似于 X[key], X[i:j]=sequence                                                                                                                                  |
| __delitem__                                    | 索引和分片删除                                                                                                                                                                |
| __get__, __set__, __delete__                   | 描述符属性，类似于 X.attr，X.attr=value，del X.attr                                                                                                                           |
| __len__                                        | 计算长度，类似于 len(X)                                                                                                                                                       |
| __lt__，__gt__，__le__，__ge__，__eq__，__ne__ | 比较，分别对应于 <、>、<=、>=、=、!= 运算符。                                                                                                                                 |
| __iter__，__next__                             | 迭代环境下，生成迭代器与取下一条，类似于 I=iter(X) 和 next()                                                                                                                  |
| __contains__                                   | 成员关系测试，类似于 item in X                                                                                                                                                |
| __index__                                      | 整数值，类似于 hex(X)，bin(X)，oct(X)                                                                                                                                         |
| __enter__，__exit__                            | 在对类对象执行类似 with obj as var 的操作之前，会先调用 __enter__ 方法，其结果会传给 var；在最终结束该操作之前，会调用                                                        |                                    | __exit__ | 方法（常用于做一些清理、扫尾的工作） |

# Python迭代器
迭代器指的就是支持迭代的容器，更确切的说，是支持迭代的容器类对象，这里的容器可以是列表、元组等这些 Python 提供的基础容器，也可以是自定义的容器类对象，只要该容器支持迭代即可。自定义实现一个迭代器，则类中必须实现如下2个方法：
* __next__(self)：返回容器的下一个元素。
* __iter__(self)：该方法返回一个迭代器（iterator）。

Python 内置的 iter() 函数也会返回一个迭代器，该函数的语法格式如下：
>iter(obj[, sentinel])

其中，obj 必须是一个可迭代的容器对象，而 sentinel 作为可选参数，如果使用此参数，要求 obj 必须是一个可调用对象。
>可调用对象，指的是该类的实例对象可以像函数那样，直接以“对象名()”的形式被使用。通过在类中添加 __call__() 方法，就可以将该类的实例对象编程可调用对象。

 1个参数的 iter()函数，通过传入一个可迭代的容器对象，我们可以获得一个迭代器，通过调用该迭代器中的__next__()方法即可实现迭代，使用next()内置函数来迭代，即next(myIter)，和__next__()方法是完全一样的。

 iter()函数第2个参数的作用，如果使用该参数，则要求第一个obj参数必须传入可调用对象（可以不支持迭代），这样当使用返回的迭代器调用__next__()方法时，它会通过执行obj()调用 __call__()方法，如果该方法的返回值和第 2 个参数值相同，则输出 StopInteration 异常；反之，则输出 __call__() 方法的返回值。
```python
# 迭代器实现字符串逆序
class Reverse:
    def __init__(self, string):
        self.__string = string
        self.__index = len(string)
    def __iter__(self):
        return self
    def __next__(self):
        if self.__index == 0:
            raise(StopIteration)
        self.__index -= 1
        return self.__string[self.__index]
revstr = Reverse('Python')
for c in revstr:
    print(c,end=" ")
```
# 生成器
生成器的创建方式也比迭代器简单很多，大体分为以下 2 步：
* 定义一个以 yield 关键字标识返回值的函数；
* 调用刚刚创建的函数，即可创建一个生成器。

和return相比，yield 除了可以返回相应的值，还有一个更重要的功能，即每当程序执行完该语句时，程序就会暂停执行。不仅如此，即便调用生成器函数，Python 解释器也不会执行函数中的代码，它只会返回一个生成器（对象）。
>相比迭代器，生成器最明显的优势就是节省内存空间，即它不会一次性生成所有的数据，而是什么时候需要，什么时候生成。
```python
def intNum():
    print("开始执行")
    for i in range(5):
        yield i
        print("继续执行")
num = intNum()
```
即便调用生成器函数，Python 解释器也不会执行函数中的代码，它只会返回一个生成器（对象）。想要生成器函数执行，或者想使执行完 yield 语句立即暂停的程序得以继续执行，可以通过：
* 通过生成器调用next()内置函数或者__next__()方法
* 通过for循环遍历生成器

## 生成器send()方法
通过 send() 方法，还可以向生成器中传值。

值得一提的是，send()方法可带一个参数，也可以不带任何参数（用 None 表示）。其中，当使用不带参数的send()方法时，它和next()函数的功能完全相同。例如：
```python
ddef foo():
    bar_a = yield "hello"
    bar_b = yield bar_a
    yield bar_b
f = foo()
print(f.send(None))
print(f.send("C语言中文网"))
print(f.send("http://c.biancheng.net"))
#hello
#C语言中文网
#http://c.biancheng.net
```
## close()方法
生成器函数中遇到yield语句暂停运行时，此时如果调用 close()方法，会阻止生成器函数继续执行，该函数会在程序停止运行的位置抛出 GeneratorExit 异常。
```python
def foo():
    try:
        yield 1
    except GeneratorExit:
        print('捕获到 GeneratorExit')
        yield 2 #抛出 RuntimeError 异常
f = foo()
print(next(f))
f.close()
'''
1
捕获到 GeneratorExit Traceback (most recent call last):
  File "D:\python3.6\1.py", line 10, in <module>
    f.close()
RuntimeError: generator ignored GeneratorExit
'''
```
## 生成器throw()方法
生成器 throw() 方法的功能是，在生成器函数执行暂停处，抛出一个指定的异常，之后程序会继续执行生成器函数中后续的代码，直到遇到下一个yield语句。需要注意的是，如果到剩余代码执行完毕没有遇到下一个yield语句，则程序会抛出 StopIteration异常。
```python
def foo():
    try:
        yield 1
    except ValueError:
        print('捕获到 ValueError')
f = foo()
print(next(f))
f.throw(ValueError)
'''
运行结果：1
捕获到 ValueError
Traceback (most recent call last):
  File "D:\python3.6\1.py", line 9, in <module>
    f.throw(ValueError)
StopIteration
'''
```
一开始生成器函数在yield 1处暂停执行，当执行throw()方法时，它会先抛出 ValueError异常，然后继续执行后续代码找到下一个 yield语句，该程序中由于后续不再有yield语句，因此程序执行到最后，会抛出一个 StopIteration异常。

# 函数装饰器及用法
 Python 内置的 3 种函数装饰器，分别是 ＠staticmethod、＠classmethod 和 @property，其中 staticmethod()、classmethod()和 property()都是Python的内置函数。
 使用函数装饰器A()去装饰另一个函数B()，其底层执行了如下2步操作：
* 将B作为参数传给A()函数；
* 将A()函数执行完成的返回值反馈回B。
```python
def funA(fn):
    print("C语言中文网")
    fn() # 执行传入的fn参数
    print("http://c.biancheng.net")
    return "装饰器函数的返回值"
@funA
def funB():
    print("学习 Python")
'''
C语言中文网
学习 Python
http://c.biancheng.net
在此基础上，如果在程序末尾添加如下语句：
print(funB)
其输出结果为：
装饰器函数的返回值
'''
```
如果装饰器函数的返回值为普通变量，那么被修饰的函数名就变成了变量名；同样，如果装饰器返回的是一个函数的名称，怎么被修饰的函数名依然表示一个函数。
>函数装饰器，就是通过装饰器函数，在不修改原函数的前提下，来对函数的功能进行合理的扩充。
## 带参数的函数装饰器
函数装饰器中嵌套一个函数，该函数带有的参数个数和被装饰器修饰的函数相同。例如：
```python
def funA(fn):
    # 定义一个嵌套函数
    def say(arc):
        print("Python教程:",arc)
    return say
@funA
def funB(arc):
    print("funB():", a)
funB("http://c.biancheng.net/python")
'''
程序执行结果为：
Python教程: http://c.biancheng.net/python
'''
```
## 函数装饰器嵌套
上面程序的执行顺序是里到外，所以它等效于下面这行代码：
>fun = funA( funB ( funC (fun) ) )
# 装饰器的应用场景
## 身份认证
```python
import functools
def authenticate(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        request = args[0]
        # 如果用户处于登录状态
        if check_user_logged_in(request):
            # 执行函数 post_comment()
            return func(*args, **kwargs)  
        else:
            raise Exception('Authentication failed')
    return wrapper
   
@authenticate
def post_comment(request, ...)
    ...
```
定义了装饰器 authenticate，函数 post_comment() 则表示发表用户对某篇文章的评论，每次调用这个函数前，都会先检查用户是否处于登录状态，如果是登录状态，则允许这项操作；如果没有登录，则不允许。
## 日志记录
日志记录同样是很常见的一个案例。在实际工作中，如果你怀疑某些函数的耗时过长，导致整个系统的延迟增加，想在线上测试某些函数的执行时间，那么，装饰器就是一种很常用的手段。
```python
import time
import functools

def log_execution_time(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        res = func(*args, **kwargs)
        print('{} took {} ms'.format(func.__name__, (end - start) * 1000))
        return res
    return wrapper
   
@log_execution_time
def calculate_similarity(items):
    ...
```
装饰器 log_execution_time 记录某个函数的运行时间，并返回其执行结果。如果你想计算任何函数的执行时间，在这个函数上方加上@log_execution_time即可。
## 装饰器用于输入合理性检查
在大型公司的机器学习框架中，调用机器集群进行模型训练前，往往会用装饰器对其输入（往往是很长的 json 文件）进行合理性检查。这样就可以大大避免输入不正确对机器造成的巨大开销。
```python
import functools

def validation_check(input):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        ...
@validation_check
def neural_network_training(param1, param2,...)
```
很多情况下都会出现输入不合理的现象。因为我们调用的训练模型往往很复杂，输入的文件有成千上万行，很多时候确实也很难发现。

## 缓存装饰器
ython 中的表示形式是 @lru_cache。@lru_cache 会缓存进程中的函数参数和结果，当缓存满了以后，会删除最近最久未使用的数据。正确使用缓存装饰器，往往能极大地提高程序运行效率。
```python
@lru_cache
def check(param1, param2, ...)  # 检查用户设备类型、版本号
```