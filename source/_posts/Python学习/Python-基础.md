---
title: Python-基础
date: 2020-04-24 16:50:45
categories: 
- 计算机基础
- Python
tags:
- Python
toc: true
---
# Python注释
Python 支持两种类型的注释，分别是单行注释和多行注释。
## Python单行注释
Python使用井号#作为单行注释的符号，语法格式为：
>#注释内容

从井号#开始，直到这行结束为止的所有内容都是注释。Python 解释器遇到#时，会忽略它后面的整行内容。
## Python多行注释
多行注释指的是一次性注释程序中多行的内容（包含一行）。Python 使用三个连续的单引号'''或者三个连续的双引号"""注释多行内容，具体格式如下：
>'''
使用 3 个单引号分别作为注释的开头和结尾
可以一次性注释多行内容
这里面的内容全部是注释内容
'''
# Python缩进规则(包含快捷键)
在 Python 中，对于类定义、函数定义、流程控制语句、异常处理语句等，行尾的冒号和下一行的缩进，表示下一个代码块的开始，而缩进的结束则表示此代码块的结束。

注意，Python 中实现对代码的缩进，可以使用空格或者 Tab 键实现。但无论是手动敲空格，还是使用 Tab 键，通常情况下都是采用 4 个空格长度作为一个缩进量（默认情况下，一个 Tab 键就表示 4 个空格）。
# Python编码规范（PEP 8）
Python 采用 PEP 8 作为编码规范，其中 PEP 是 Python Enhancement Proposal（Python 增强建议书）的缩写，8 代表的是 Python 代码的样式指南。
* 每个 import 语句只导入一个模块，尽量避免一次导入多个模块
* 不要在行尾添加分号，也不要用分号将两条命令放在同一行
* 建议每行不超过80个字符，如果超过，建议使用小括号将多行内容隐式的连接起来，而不推荐使用反斜杠\进行连接。
* 使用必要的空行可以增加代码的可读性，通常在顶级定义（如函数或类的定义）之间空两行，而方法定义之间空一行，另外在用于分隔某些功能的位置也可以空一行。
* 通常情况下，在运算符两侧、函数参数之间以及逗号两侧，都建议使用空格进行分隔。
# Python标识符命名规范
Python 中标识符的命名不是随意的，而是要遵守一定的命令规则，比如说：
* 标识符是由字符（A~Z 和 a~z）、下划线和数字组成，但第一个字符不能是数字。
* 标识符不能和 Python 中的保留字相同。有关保留字，后续章节会详细介绍。
* Python中的标识符中，不能包含空格、@、% 以及 $ 等特殊字符。
* 在 Python 中，标识符中的字母是严格区分大小写的，也就是说，两个同样的单词，如果大小格式不一样，多代表的意义也是完全不同的。
* Python 语言中，以下划线开头的标识符有特殊含义，例如：以单下划线开头的标识符（如 _width），表示不能直接访问的类属性，其无法通过 from...import* 的方式导入；以双下划线开头的标识符（如__add）表示类的私有成员；以双下划线作为开头和结尾的标识符（如 __init__），是专用标识符。
* 标识符的命名，除了要遵守以上这几条规则外，不同场景中的标识符，其名称也有一定的规范可循，例如：当标识符用作模块名时，应尽量短小，并且全部使用小写字母，可以使用下划线分割多个字母，例如 game_mian、game_register等。当标识符用作包的名称时，应尽量短小，也全部使用小写字母，不推荐使用下划线，例如 com.mr、com.mr.book 等。当标识符用作类名时，应采用单词首字母大写的形式。例如，定义一个图书类，可以命名为 Book。模块内部的类名，可以采用 "下划线+首字母大写" 的形式，如 _Book;函数名、类中的属性名和方法名，应全部使用小写字母，多个单词之间可以用下划线分割；常量命名应全部使用大写字母，单词之间可以用下划线分割；

# Python关键字(保留字)
保留字是 Python 语言中一些已经被赋予特定意义的单词，这就要求开发者在开发程序时，不能用这些保留字作为标识符给变量、函数、类、模板以及其他对象命名。
```python
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```
# Python内置函数
## abs(x)
返回一个数的绝对值。实参可以是整数或浮点数。如果实参是一个复数，返回它的模。
## all(iterable)
如果 iterable 的所有元素为真（或迭代器为空），返回 True 。等价于:
```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
```
## any(iterable)
如果 iterable 的任一元素为真则返回 True。 如果迭代器为空，返回 False。 等价于:
```python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
```
## ascii(object)
像函数repr()，返回一个对象可打印的字符串，但是repr()返回的字符串中非ASCII编码的字符，会使用 \x、\u 和 \U 来转义。生成的字符串和Python2的repr()返回的结果相似。
## bin(x)
将一个整数转变为一个前缀为“0b”的二进制字符串。结果是一个合法的 Python 表达式。如果x不是 Python的int对象，那它需要定义 __index__()方法返回一个整数。
```python
>>> bin(3)
'0b11'
>>> bin(-10)
'-0b1010'
```
## class bool([x])
返回一个布尔值，True 或者 False。 x使用标准的 真值测试过程 来转换。如果x是假的或者被省略，返回 False；其他情况返回True。bool类是int的子类（参见 数字类型 --- int, float, complex）。其他类不能继承自它。它只有False和True两个实例。

## breakpoint(*args, **kws)
此函数会在调用时将你陷入调试器中。具体来说，它调用sys.breakpointhook() ，直接传递args和kws 。默认情况下， sys.breakpointhook()调用 pdb.set_trace()且没有参数。在这种情况下，它纯粹是一个便利函数，因此您不必显式导入 pdb 且键入尽可能少的代码即可进入调试器。但是，sys.breakpointhook() 可以设置为其他一些函数并被breakpoint()自动调用，以允许进入你想用的调试器。

## class bytearray([source[, encoding[, errors]]])
返回一个新的bytes数组。 bytearray类是一个可变序列，包含范围为0 <= x < 256的整数。它有可变序列大部分常见的方法，见 可变序列类型的描述；同时有 bytes类型的大部分方法，参见 bytes和bytearray操作。

## enumerate(iterable, start=0)
返回一个枚举对象。iterable 必须是一个序列，或 iterator，或其他支持迭代的对象。 enumerate() 返回的迭代器的 __next__() 方法返回一个元组，里面包含一个计数值（从 start 开始，默认为 0）和通过迭代 iterable 获得的值。
##　divmod(a, b)
它将两个（非复数）数字作为实参，并在执行整数除法时返回一对商和余数。对于混合操作数类型，适用双目算术运算符的规则。对于整数，结果和 (a // b, a % b) 一致。对于浮点数，结果是(q, a % b) ，q通常是 math.floor(a / b) 但可能会比 1 小。在任何情况下， q * b + a % b和a基本相等；如果a % b非零，它的符号和 b 一样，并且 0 <= abs(a % b) < abs(b)。
## eval(expression[, globals[, locals]])
实参是一个字符串，以及可选的 globals 和 locals。globals 实参必须是一个字典。locals 可以是任何映射对象。

expression参数会作为一个 Python表达式（从技术上说是一个条件列表）被解析并求值，使用 globals 和 locals 字典作为全局和局部命名空间。如果 globals 字典存在且不包含以 __builtins__为键的值，则会在解析expression之前插入以此为键的对内置模块builtins的字典的引用。这意味着expression通常具有对标准builtins模块的完全访问权限且受限的环境会被传播。如果省略locals字典则其默认值为 globals字典。如果两个字典同时省略，表达式会在eval()被调用的环境中执行。返回值为表达式求值的结果。语法错误将作为异常被报告。
## getattr(object, name[, default])
返回对象命名属性的值。name必须是字符串。如果该字符串是对象的属性之一，则返回该属性的值。例如， getattr(x, 'foobar') 等同于 x.foobar。如果指定的属性不存在，且提供了default值，则返回它，否则触发 AttributeError。
## globals()
返回表示当前全局符号表的字典。这总是当前模块的字典（在函数或方法中，不是调用它的模块，而是定义它的模块）。
## hasattr(object, name)
该实参是一个对象和一个字符串。如果字符串是对象的属性之一的名称，则返回True，否则返回False。（此功能是通过调用getattr(object, name) 看是否有 AttributeError异常来实现的）。
## hash(object)
返回该对象的哈希值（如果它有的话）。哈希值是整数。它们在字典查找元素时用来快速比较字典的键。相同大小的数字变量有相同的哈希值（即使它们类型不同，如 1 和 1.0）。
## repr(object)
返回包含一个对象的可打印表示形式的字符串。 对于许多类型来说，该函数会尝试返回的字符串将会与该对象被传递给 eval() 时所生成的对象具有相同的值，在其他情况下表示形式会是一个括在尖括号中的字符串，其中包含对象类型的名称与通常包括对象名称和地址的附加信息。 类可以通过定义 __repr__() 方法来控制此函数为它的实例所返回的内容。
