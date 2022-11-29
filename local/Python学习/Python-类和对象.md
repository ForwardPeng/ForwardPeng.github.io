---
title: Python-类和对象
date: 2020-04-28 12:15:50
categories: 
- 计算机基础
- Python
tags:
- Python
toc: true
---
# Python描述符详解
述符就是一个类，只不过它定义了另一个类中属性的访问方式。换句话说，一个类可以将属性管理全权委托给描述符类。
>描述符是Python中复杂属性访问的基础，它在内部被用于实现property、方法、类方法、静态方法和super类型。

描述符协议：
* __set__(self, obj, type=None)：在设置属性时将调用这一方法（本节后续用 setter 表示）；
* __get__(self, obj, value)：在读取属性时将调用这一方法（本节后续用 getter 表示）；
* __delete__(self, obj)：对属性调用 del 时将调用这一方法。

在每次查找属性时，描述符协议中的方法都由类对象的特殊方法 __getattribute__()调用（注意不要和__getattr__() 弄混）。也就是说，每次使用类对象.属性（或者 getattr(类对象，属性值)）的调用方式时，都会隐式地调用 __getattribute__()，它会按照下列顺序查找该属性：
* 验证该属性是否为类实例对象的数据描述符；
* 如果不是，就查看该属性是否能在类实例对象的 __dict__ 中找到；
* 最后，查看该属性是否为类实例对象的非数据描述符。
```python
class revealAccess:
    def __init__(self, initval = None, name = 'var'):
        self.val = initval
        self.name = name
    def __get__(self, obj, objtype):
        print("Retrieving",self.name)
        return self.val
    def __set__(self, obj, val):
        print("updating",self.name)
        self.val = val
class myClass:
    x = revealAccess(10,'var "x"')
    y = 5
m = myClass()
print(m.x)
m.x = 20
print(m.x)
print(m.y)

Retrieving var "x"
10
updating var "x"
Retrieving var "x"
20
5
```
如果一个类的某个属性有数据描述符，那么每次查找这个属性时，都会调用描述符的__get__()方法，并返回它的值；同样，每次在对该属性赋值时，也会调用__set__()方法。
>除了使用描述符类自定义类属性被调用时做的操作外，还可以使用 property()函数或者@property装饰器
## property()函数：定义属性
Python 中提供了 property() 函数，可以实现在不破坏类封装原则的前提下，让开发者依旧使用“类对象.属性”的方式操作类中的属性。property()函数的基本使用格式如下：
>属性名=property(fget=None, fset=None, fdel=None, doc=None)

其中，fget 参数用于指定获取该属性值的类方法，fset 参数用于指定设置该属性值的方法，fdel 参数用于指定删除该属性值的方法，最后的 doc 是一个文档字符串，用于说明此函数的作用。
```python
class CLanguage:
    #构造函数
    def __init__(self,n):
        self.__name = n
    #设置 name 属性值的函数
    def setname(self,n):
        self.__name = n
    #访问nema属性值的函数
    def getname(self):
        return self.__name
    #删除name属性值的函数
    def delname(self):
        self.__name="xxx"
    #为name 属性配置 property() 函数
    name = property(getname, setname, delname, '指明出处')
#调取说明文档的 2 种方式
#print(CLanguage.name.__doc__)
help(CLanguage.name)
clang = CLanguage("C语言中文网")
#调用 getname() 方法
print(clang.name)
#调用 setname() 方法
clang.name="Python教程"
print(clang.name)
#调用 delname() 方法
del clang.name
print(clang.name)
```
<font color=red>由于getname()方法中需要返回 name 属性，如果使用 self.name的话，其本身又被调用 getname()，这将会先入无限死循环。为了避免这种情况的出现，程序中的name属性必须设置为私有属性，即使用__name（前面有2个下划线）。</font>

```python
name = property(getname)    # name 属性可读，不可写，也不能删除
name = property(getname, setname,delname)    #name属性可读、可写、也可删除，就是没有说明文档
```
# @property装饰器
保护类的封装特性，又要让开发者可以使用“对象.属性”的方式操作操作类属性，除了使用 property() 函数，Python 还提供了 @property 装饰器。通过 @property 装饰器，可以直接通过方法名来访问方法，不需要在方法名后添加一对“（）”小括号。语法格式：
>@property
def 方法名(self)
    代码块
```python
class Rect:
    def __init__(self,area):
        self.__area = area
    @property
    def area(self):
        return self.__area
rect = Rect(30)
#直接通过方法名来访问 area 方法
print("矩形的面积是：",rect.area)
```
使用＠property 修饰了area()方法，这样就使得该方法变成了 area 属性的 getter 方法。需要注意的是，如果类中只包含该方法，那么area属性将是一个只读属性。添加setter方法，需要用到setter装饰器，语法格式：
>@方法名.setter
def 方法名(self, value):
    代码块

删除deleter装饰器指定属性，语法格式为：
>@方法名.deleter
def 方法名(self):
    代码块
```python
@area.setter
def area(self, value):
    self.__area = value
@area.deleter
def area(self):
    self.__area = 0
```
# 封装
Python并没有提供public、private这些修饰符。为了实现类的封装，Python采取了下面的方法：
* 默认情况下，Python 类中的变量和方法都是公有（public）的，它们的名称前都没有下划线（_）；
* 如果类中的变量和函数，其名称以双下划线“__”开头，则该变量（函数）为私有变量（私有函数），其属性等同于 private。
* 定义以单下划线“_”开头的类属性或者类方法（例如 _name、_display(self)），这种类属性和类方法通常被视为私有属性和私有方法，虽然它们也能通过类对象正常访问，但这是一种约定俗称的用法
>Python 类中还有以双下划线开头和结尾的类方法（例如类的构造函数__init__(self)），这些都是 Python 内部定义的，用于 Python 内部调用。我们自己定义类属性或者类方法时，不要使用这种格式。

```python
class CLanguage :
    def setname(self, name):
        if len(name) < 3:
            raise ValueError('名称长度必须大于3！')
        self.__name = name

    def getname(self):
        return self.__name
    #为 name 配置 setter 和 getter 方法
    name = property(getname, setname)
    def setadd(self, add):
        if add.startswith("http://"):
            self.__add = add
        else:
            raise ValueError('地址必须以 http:// 开头') 

    def getadd(self):
        return self.__add
   
    #为 add 配置 setter 和 getter 方法
    add = property(getadd, setadd)

    #定义个私有方法
    def __display(self):
        print(self.__name,self.__add)

clang = CLanguage()
clang.name = "个人博客"
clang.add = "http://forwardpeng.club"
print(clang.name)
print(clang.add)
```
CLanguage 将 name 和 add 属性都隐藏了起来，但同时也提供了可操作它们的“窗口”，也就是各自的 setter 和 getter 方法，这些方法都是公有（public）的。

不仅如此，以add属性的 setadd() 方法为例，通过在该方法内部添加控制逻辑，即通过调用 startswith()方法，控制用户输入的地址必须以“http://”开头，否则程序将会执行 raise 语句抛出 ValueError 异常。
>raise 这里可简单理解成，如果用户输入不规范，程序将会报错。

<font color=red>以双下划线开头命名的类属性或类方法，Python 在底层实现时，将它们的名称都偷偷改成了 "_类名__属性（方法）名" 的格式。私有的类属性（例如 __name 和 __add），其底层的名称也改成了“_类名__属性名”的这种格式。可以通过修改 clang 对象的私有属性</font>

# 继承机制及其使用
Python 类的封装、继承、多态 3 大特性，前面章节已经详细介绍了 Python 类的封装，本节继续讲解 Python 类的继承机制。

继承机制经常用于创建和现有类功能类似的新类，又或是新类只需要在现有类基础上添加一些成员（属性和方法），但又不想直接将现有类代码复制给新类。也就是说，通过使用继承这种机制，可以轻松实现类的重复使用。
```python
class Shape:
    def draw(self,content):
        print("画",content)
class Form(Shape):
    def area(self):
        #....
        print("此图形的面积为...")
```
class From(Shape) 就表示 From 继承 Shape。Python中，实现继承的类称为子类，被继承的类称为父类（也可称为基类、超类）。因此在上面这个样例中，From 是子类，Shape 是父类。

子类继承父类时，只需在定义子类时，将父类（可以是多个）放在子类之后的圆括号里即可。语法格式如下：
>class 类名(父类1, 父类2, ...)：
    #类定义部分

<font color=red>如果该类没有显式指定继承自哪个类，则默认继承 object 类（object 类是 Python 中所有类的父类，即要么是直接父类，要么是间接父类）。另外，Python 的继承是多继承机制（和 C++ 一样），即一个子类可以同时拥有多个直接父类。</font>

继承是相对子类来说的，即子类继承自父类；而派生是相对于父类来说的，即父类派生出子类。子类拥有父类所有的属性和方法，即便该属性或方法是私有（private）的

## 多继承
使用多继承经常需要面临的问题是，多个父类中包含同名的类方法。对于这种情况，Python 的处置措施是：根据子类继承多个父类时这些父类的前后次序决定，即排在前面父类中的类方法会覆盖排在后面父类中的同名类方法。
```python
class People:
    def __init__(self):
        self.name = People
    def say(self):
        print("People类",self.name)

class Animal:
    def __init__(self):
        self.name = Animal
    def say(self):
        print("Animal类",self.name)
#People中的 name 属性和 say() 会遮蔽 Animal 类中的
class Person(People, Animal):
    pass

zhangsan = Person()
zhangsan.name = "张三"
zhangsan.say()
OutPut：People类 张三
```
# 父类方法重写
类继承了父类，那么子类就拥有了父类所有的类属性和类方法。通常情况下，子类会在此基础上，扩展一些新的类属性和类方法。
>重写，有时又称覆盖，是一个意思，指的是对类中已有方法的内部实现进行修改。
```python
class Bird:
    #鸟有翅膀
    def isWing(self):
        print("鸟有翅膀")
    #鸟会飞
    def fly(self):
        print("鸟会飞")
class Ostrich(Bird):
    # 重写Bird类的fly()方法
    def fly(self):
        print("鸵鸟不会飞")

# 创建Ostrich对象
ostrich = Ostrich()
#调用 Ostrich 类中重写的 fly() 类方法
ostrich.fly()
结果：鸵鸟不会飞
```
## 如何调用被重写的方法
通过类名调用实例方法的这种方式，又被称为未绑定方法。
```python
# 创建Ostrich对象
ostrich = Ostrich()
#调用 Bird 类中的 fly() 方法
Bird.fly(ostrich)
鸟会飞
```
# 使用Python继承机制(子类化内置类型)
内置类型子类化，其实就是自定义一个新类，使其继承有类似行为的内置类，通过重定义这个新类实现指定的功能。
```python
class newDictError(ValueError):
  """如果向newDict 添加重复值，则引发此异常"""
class newDict(dict):
  """不接受重复值的字典"""
  def __setitem__(self,key,value):
    if value in self.values():
      if ((key in self and self[key]!=value) or (key not in self)):
        raise newDictError("这个值已经存在，并对应不同的键")
    super().__setitem__(key,value)
demoDict = newDict()
demoDict['key']='value'
demoDict['other_key']='value2'
print(demoDict)
demoDict['other_key']='value'
print(demoDict)
```
newDict是Python中 dict 类型的子类，所以其大部分行为都和dict内置类相同，唯一不同之处在于，newDict不允许字典中多个键对应相同的值。如果用户试图添加具有相同值的新元素，则会引发 newDictError 异常，并给出提示信息。
# super()函数：调用父类构造方法
Python 中子类会继承父类所有的类属性和类方法。严格来说，类的构造方法其实就是实例方法，因此毫无疑问，父类的构造方法，子类同样会继承。在子类中的构造方法中，调用父类构造方法的方式有 2 种，分别是：
* 类可以看做一个独立空间，在类的外部调用其中的实例方法，可以向调用普通函数那样，只不过需要额外备注类名（此方式又称为未绑定方法）；
* 使用 super() 函数。但如果涉及多继承，该函数只能调用第一个直接父类的构造方法。
>也就是说，涉及到多继承时，在子类构造函数中，调用第一个父类构造方法的方式有以上 2 种，而调用其它父类构造方法的方式只能使用未绑定方法。

语法格式如下：
>super().__init__(self,...)
```python
class People:
    def __init__(self,name):
        self.name = name
    def say(self):
        print("我是人，名字为：",self.name)
class Animal:
    def __init__(self,food):
        self.food = food
    def display(self):
        print("我是动物,我吃",self.food)
class Person(People, Animal):
    #自定义构造方法
    def __init__(self,name,food):
        #调用 People 类的构造方法
        super().__init__(name)
        #super(Person,self).__init__(name) #执行效果和上一行相同
        #People.__init__(self,name)#使用未绑定方法调用 People 类构造方法
        #调用其它父类的构造方法，需手动给 self 传值
        Animal.__init__(self,food)    
per = Person("zhangsan","熟食")
per.say()
per.display()
# 运行结果：
    我是人，名字为： zhangsan
    我是动物,我吃 熟食
```
Person类自定义的构造方法中，调用People类构造方法，可以使用super() 函数，也可以使用未绑定方法。但是调用Animal类的构造方法，只能使用未绑定方法。
# super()使用注意事项（包含新式类和旧式类的区别）
Python 2.x 版本中，为了向后兼容保留了旧式类。该版本中的新式类必须显式继承 object 或者其他新式类：
```python
class newStyleClass(object):
  pass
class newStyleClass(newStyleClass):
  pass
```
Python3.x中，显式声明某个类继承自object似乎是冗余的。但如果考虑跨版本兼容，那么就必须将 object 作为所有基类的祖先，因为如果不这么做的话，这些类将被解释为旧式类，最终会导致难以诊断的问题。
# super()使用注意事项
由于基类不会在__init__() 中被隐式地调用，需要程序员显式调用它们。这种情况下，当程序中包含多重继承的类层次结构时，使用super是非常危险的，往往会在类的初始化过程中出现问题。
## 混用super与显式类调用
C类使用了 __init__() 方法调用它的基类，会造成B类被调用了2次：
```python
class A:
    def __init__(self):
        print("A",end=" ")
        super().__init__()
class B:
    def __init__(self):
        print("B",end=" ")
        super().__init__()
class C(A,B):
    def __init__(self):
        print("C",end=" ")
        A.__init__(self)
        B.__init__(self)
print("MRO:",[x.__name__ for x in C.__mro__])
C()
运行结果为：
MRO: ['C', 'A', 'B', 'object']
C A B B
```
 C的实例调用A.__init__(self)，使得super(A,self).__init__() 调用了B.__init__()方法。换句话说，super应该被用到整个类的层次结构中。

 ## 不同种类的参数
 ```python
 class commonBase:
    def __init__(self,*args,**kwargs):
        print("commonBase")
        super().__init__()
class base1(commonBase):
    def __init__(self,*args,**kwargs):
        print("base1")
        super().__init__(*args,**kwargs)
class base2(commonBase):
    def __init__(self,*args,**kwargs):
        print("base2")
        super().__init__(*args,**kwargs)
class myClass(base1,base2):
    def __init__(self,arg):
        print("my base")
        super().__init__(arg)
myClass(10)
```
使用*args和**kwargs包装的参数和关键字参数，但是由于任何参数都可以传入，所有构造函数都可以接受任何类型的参数，这会导致代码变得脆弱。另一种解决方法是在 MyClass 中显式地使用特定类的 __init__() 调用，但这无疑会导致第一种错误。

## 总结
* 尽可能避免使用多继承，可以使用一些设计模式来替代它；
* super的使用必须一致，即在类的层次结构中，要么全部使用super，要么全不用。混用super和传统调用是一种混乱的写法；
* 如果代码需要兼容 Python 2.x，在 Python 3.x中应该显式地继承自 object。在 Python 2.x 中，没有指定任何祖先地类都被认定为旧式类。
* 调用父类时应提前查看类的层次结构，也就是使用类的__mro__属性或者mro()方法查看有关类的MRO。

# __slots__:限制类实例动态添加属性和方法
Python 提供了 __slots__ 属性，通过它可以避免用户频繁的给实例对象动态地添加属性或方法。再次声明，__slots__ 只能限制为实例对象动态添加属性和方法，而无法限制动态地为类添加属性和方法。

__slots__属性值其实就是一个元组，只有其中指定的元素，才可以作为动态添加的属性或者方法的名称。
```python
class CLanguage:
    __slots__ = ('name','add','info')
```
>对于动态添加的方法，__slots__限制的是其方法名，并不限制参数的个数。只对当前所在的类起限制作用，如果为子类也设置有 __slots__ 属性，那么子类实例对象允许动态添加的属性和方法，是子类中 __slots__ 属性和父类 __slots__ 属性的和。
# type()函数：动态创建类
type() 函数属于 Python 内置函数，通常用来查看某个变量的具体类型。其实，type() 函数还有一个更高级的用法，即创建一个自定义类型（也就是创建一个类）。type() 函数的语法格式有 2 种，分别如下：
* type(obj) 
* type(name, bases, dict)

以上这 2 种语法格式，各参数的含义及功能分别是：
* 第一种语法格式用来查看某个变量（类对象）的具体类型，obj 表示某个变量或者类对象。
* 第二种语法格式用来创建类，其中 name 表示类的名称；bases 表示一个元组，其中存储的是该类的父类；dict 表示一个字典，用于表示类内定义的属性或者方法。

```python
def say(self):
    print("我要学 Python！")
#使用 type() 函数创建类
CLanguage = type("CLanguage",(object,),dict(say = say, name = "个人博客"))
#创建一个 CLanguage 实例对象
clangs = CLanguage()
#调用 say() 方法和 name 属性
clangs.say()
print(clangs.name)
```
>Python 元组语法规定，当 (object,) 元组中只有一个元素时，最后的逗号（,）不能省略。
# MetaClass元类
使用元类的主要目的就是为了实现在创建类时，能够动态地改变类中定义的属性或者方法。把一个类设计成MetaClass 元类，其必须符合以下条件：
* 必须显式继承自type类；
* 类中需要定义并实现__new__()方法，该方法一定要返回该类的一个实例对象，因为在使用元类创建类时，该__new__()方法会自动被执行，用来修改新建的类。
```python
#定义一个元类
class FirstMetaClass(type):
    # cls代表动态修改的类
    # name代表动态修改的类名
    # bases代表被动态修改的类的所有父类
    # attr代表被动态修改的类的所有属性、方法组成的字典
    def __new__(cls, name, bases, attrs):
        # 动态为该类添加一个name属性
        attrs['name'] = "个人博客"
        attrs['say'] = lambda self: print("调用say()实例方法")
        return super().__new__(cls,name,bases,attrs)
#定义类时，指定元类
class CLanguage(object,metaclass=FirstMetaClass):
    pass
```
# Python多态及用法
Python是弱类型语言，其最明显的特征是在使用变量时，无需为其指定具体的数据类型。这会导致一种情况，即同一变量可能会被先后赋值不同的类对象，类的多态特性，还要满足以下 2 个前提条件：
* 继承：多态一定是发生在子类和父类之间；
* 重写：子类重写了父类的方法。
```python
class WhoSay:
    def say(self,who):
        who.say()
class CLanguage:
    def say(self):
        print("调用的是 Clanguage 类的say方法")
class CPython(CLanguage):
    def say(self):
        print("调用的是 CPython 类的say方法")
class CLinux(CLanguage):
    def say(self):
        print("调用的是 CLinux 类的say方法")
a = WhoSay()
#调用 CLanguage 类的 say() 方法
a.say(CLanguage())
#调用 CPython 类的 say() 方法
a.say(CPython())
#调用 CLinux 类的 say() 方法
a.say(CLinux())
```
通过给WhoSay类中的say()函数添加一个who参数，其内部利用传入的who调用 say() 方法。这意味着，当调用 WhoSay 类中的 say() 方法时，我们传给 who 参数的是哪个类的实例对象，它就会调用那个类中的 say()方法。
# 枚举类定义及使用
实例：
```python
from enum import Enum
class Color(Enum):
    # 为序列值指定value值
    red = 1
    green = 2
    blue = 3
```
Color 枚举类中，red、green、blue 都是该类的成员（可以理解为是类变量）。注意，枚举类的每个成员都由 2 部分组成，分别为 name 和 value，其中 name 属性值为该枚举值的变量名（如 red），value 代表该枚举值的序号（序号通常从 1 开始）。
```python
# 访问枚举类成员
print(Color.red)
print(Color['red'])
print(Color(1))
#调取枚举成员中的 value 和 name
print(Color.red.value)
print(Color.red.name)
```
枚举类成员之间可以用 == 或者 is 进行比较是否相等，但各个成员的值，不能在类的外部做任何修改。Python枚举类中各个成员必须保证 name 互不相同，但value可以相同。可以借助@unique装饰器，这样当枚举类中出现相同值的成员时，程序会报 ValueError错误。实例如下：
```python
from enum import Enum
class Color(Enum):
    # 为序列值指定value值
    red = 1
    green = 1
    blue = 3
print(Color['green'])
Output Color：red
from enum import Enum,unique
#添加 unique 装饰器
@unique
class Color(Enum):
    # 为序列值指定value值
    red = 1
    green = 1
    blue = 3
print(Color['green'])
ValueError错误
```
除了通过继承 Enum 类的方法创建枚举类，还可以使用Enum()函数创建枚举类。可接受2个参数，第一个用于指定枚举类的类名，第二个参数用于指定枚举类中的多个成员。