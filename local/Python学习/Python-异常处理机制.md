---
title: Python-异常处理机制
date: 2020-04-29 12:18:06
categories: 
- 计算机基础
- Python
tags:
- Python
toc: true
---
# 常见的异常类型
| 异常类型          | 含义                                                                     | 实例                                                                                                                                                                                                                                        |
| ----------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AssertionError    | 当 assert 关键字后的条件为假时，程序运行会停止并抛出 AssertionError 异常 | >>> demo_list = ['C语言中文网']>>> assert len(demo_list) > 0>>> demo_list.pop()'C语言中文网'>>> assert len(demo_list) > 0 Traceback (most recent call last):File "<pyshell#6>", line 1, in <module>assert len(demo_list) > 0 AssertionError |
| AttributeError    | 当试图访问的对象属性不存在时抛出的异常                                   | >>> demo_list = ['C语言中文网']>>> demo_list.len Traceback (most recent call last): File "<pyshell#10>", line 1, in <module> demo_list.len AttributeError: 'list' object has no attribute 'len'                                             |
| IndexError        | 索引超出序列范围会引发此异常                                             | >>> demo_list = ['C语言中文网'] >>> demo_list[3] Traceback (most recent call last): File "<pyshell#8>", line 1, in <module> demo_list[3] IndexError: list index out of range                                                                |
| KeyError          | 字典中查找一个不存在的关键字时引发此异常                                 | >>> demo_dict={'C语言中文网':"c.biancheng.net"} >>> demo_dict["C语言"] Traceback (most recent call last):File "<pyshell#12>", line 1, in <module> demo_dict["C语言"] KeyError: 'C语言'                                                      |
| NameError         | 尝试访问一个未声明的变量时，引发此异常                                   | >>> C语言中文网 Traceback (most recent call last): File "<pyshell#15>", line 1, in <module> C语言中文网NameError: name 'C语言中文网' is not defined                                                                                         |
| TypeError         | 不同类型数据之间的无效操作                                               | >>> 1+'C语言中文网'                                                 Traceback (most recent call last): File "<pyshell#17>", line 1, in <module> 1+'C语言中文网' TypeError: unsupported operand type(s) for +: 'int' and 'str'               |
| ZeroDivisionError | 除法运算中除数为 0 引发此异常                                            | >>> a = 1/0 Traceback (most recent call last): File "<pyshell#2>", line 1, in <module> a = 1/0 ZeroDivisionError: division by zero                                                                                                          |
|                   |

>使用 Python 异常处理机制，可以让程序中的异常处理代码和正常业务代码分离，使得程序代码更加优雅，并可以提高程序的健壮性。
# try except异常处理
Python 中，用try except语句块捕获并处理异常，其基本语法结构如下所示：
>try:
    可能产生异常的代码块
except [ (Error1, Error2, ... ) [as e] ]:
    处理异常的代码块1
except [ (Error3, Error4, ... ) [as e] ]:
    处理异常的代码块2
except  [Exception]:
    处理其它异常

该格式中，[]括起来的部分可以使用，也可以省略。其中各部分的含义如下：
* (Error1, Error2,...) 、(Error3, Error4,...)：其中，Error1、Error2、Error3 和 Error4都是具体的异常类型。显然，一个except块可以同时处理多种异常。
* [as e]：作为可选参数，表示给异常类型起一个别名 e，这样做的好处是方便在except块中调用异常类型（后续会用到）。
* [Exception]：作为可选参数，可以代指程序可能发生的所有异常情况，其通常用在最后一个except块。
>当程序发生不同的意外情况时，会对应特定的异常类型，Python 解释器会根据该异常类型选择对应的 except 块来处理该异常。
## 获取特定异常
每种异常类型都提供了如下几个属性和方法，通过调用它们，就可以获取当前处理异常类型的相关信息：
* args：返回异常的错误编号和描述字符串；
* str(e)：返回异常信息，但不包括异常信息的类型；
* repr(e)：返回较全的异常信息，包括异常信息的类型。
```python
try:
    1/0
except Exception as e:
    # 访问异常的错误编号和详细信息
    print(e.args)
    print(str(e))
    print(repr(e))
Output：
('division by zero',)
division by zero
ZeroDivisionError('division by zero',)
```

# 异常处理的底层实现
对于可以接收任何异常的 except 来说，其后可以跟Exception，也可以不跟任何参数，但表示的含义都是一样的。 Python 的常见异常类之间的继承关系如图 1：
 <div align=center><img src="/image/Exception.png" width="400"/></div>
 <center>图1 Python 的常见异常类之间的继承关系</center>

>如果用户要实现自定义异常，不应该继承 BaseException ，而应该继承 Exception 类

当一个try块配有多个except块时，这些except块应遵循这样一个排序规则，即可处理全部异常的except块（参数为Exception，也可以什么都不写）要放到所有except块的后面，且所有父类异常的except块要放到子类异常的except块的后面。

# try except else详解
在原本的try except结构的基础上，Python 异常处理机制还提供了一个 else块，也就是原有try except 语句的基础上再添加一个else块，即try except else结构。

使用else包裹的代码，只有当try块没有捕获到任何异常时，才会得到执行；反之，如果try块捕获到异常，即便调用对应的except处理完异常，else块中的代码也不会得到执行。
# try except finally：资源回收
在整个异常处理机制中，finally语句的功能是：无论try块是否发生异常，最终都要进入finally语句，并执行其中的代码块。基于finally语句的这种特性，在某些情况下，当try块中的程序打开了一些物理资源（文件、数据库连接等）时，由于这些资源必须手动回收，而回收工作通常就放在finally块中。
>Python垃圾回收机制，只能帮我们回收变量、类对象占用的内存，而无法自动完成类似关闭文件、数据库连接等这些的工作。

# 异常处理机制结构
```python
# 语法结构
try:
    #业务实现代码
except Exception1 as e:
    #异常处理块1
    ...
except Exception2 as e:
    #异常处理块2
    ...
#可以有多个 except
...
else:
    #正常处理块
finally :
    #资源回收块
    ...
```

整个异常处理结构中，只有 try 块是必需的，也就是说：
* 如果没有 try 块，则不能有后面的 except 块、else 块和 * finally 块。但是也不能只使用 try 块，要么使用 try except 结构，要么使用 try finally 结构；
except 块、else 块、finally 块都是可选的，当然也可以同时出现；
* 可以有多个 except 块，但捕获父类异常的 except 块应该位于捕获子类异常的 except 块的后面；
* 多个except块必须位于try块之后，finally块必须位于所有的 except 块之后。
* 要使用else块，其前面必须包含 try和except。
异常处理语句块的执行流程如下图2：
 <div align=center><img src="/image/liucheng.png" width="400"/></div>
 <center>图2 异常处理语句块的执行流程</center>

 # raise用法
raise 语句的基本语法格式为：
。raise [exceptionName [(reason)]]

其中，用 [] 括起来的为可选参数，其作用是指定抛出的异常名称，以及异常信息的相关描述。如果可选参数全部省略，则 raise 会把当前错误原样抛出；如果仅省略 (reason)，则在抛出异常时，将不附带任何的异常描述信息。也就是说，raise 语句有如下三种常用的用法：
* raise：单独一个 raise。该语句引发当前上下文中捕获的异常（比如在 except 块中），或默认引发 RuntimeError 异常。
* raise 异常类名称：raise 后带一个异常类名称，表示引发执行类型的异常。
* raise 异常类名称(描述信息)：在引发指定类型的异常的同时，附带异常的描述信息。

 如果没有 finally 块，程序才会立即执行 return 或 raise 语句；反之，如果找到 finally 块，系统立即开始执行 finally 块，只有当 finally 块执行完成后，系统才会再次跳回来执行 try 块、except 块里的 return 或 raise 语句。尽量避免在 finally 块里使用 return 或 raise 等导致方法中止的语句，否则可能出现一些很奇怪的情况。<font color=red>但是，如果在 finally 块里也使用了 return 或 raise 等导致方法中止的语句，finally 块己经中止了方法，系统将不会跳回去执行 try 块、except 块里的任何代码。</font>

 # sys.exc_info()方法：获取异常信息
捕获异常时，有 2 种方式可获得更多的异常信息，分别是：
* 使用 sys 模块中的 exc_info 方法；
* 使用 traceback 模块中的相关函数。

模块 sys 中，有两个方法可以返回异常的全部信息，分别是 exc_info() 和 last_traceback()，这两个函数有相同的功能和用法，
exc_info() 方法会将当前的异常信息以元组的形式返回，该元组中包含 3 个元素，分别为 type、value 和 traceback，它们的含义分别是：
* type：异常类型的名称，它是 BaseException 的子类（有关 Python 异常类，可阅读《Python常见异常类型》一节）
* value：捕获到的异常实例。
* traceback：是一个 traceback 对象。

使用 traceback 的 print_exc() 方法输出异常传播信息，print_exc([limit[, file]]) 相当于如下形式：
>print_exception(sys.exc_etype, sys.exc_value, sys.exc_tb[, limit[, file]])

```python
# 捕捉异常，并将异常传播信息输出控制台
    traceback.print_exc()
    # 捕捉异常，并将异常传播信息输出指定文件中
    traceback.print_exc(file=open('log.txt', 'a'))
```
# 自定义异常类
```python
class InputError(Exception):
    '''当输出有误时，抛出此异常'''
    #自定义异常类型的初始化
    def __init__(self, value):
        self.value = value
    # 返回异常类对象的说明信息
    def __str__(self):
        return ("{} is invalid input".format(repr(self.value)))
   
try:
    raise InputError(1) # 抛出 MyInputError 这个异常
except InputError as err:
    print('error: {}'.format(err))
'''
运行结果：
    error: 1 is invalid input
'''
```
注意，只要自定义的类继承自 Exception，则该类就是一个异常类，至于此类中包含的内容，并没有做任何规定。

# 异常使用规则
成功的异常处理应该实现如下 4 个目标：
* 使程序代码混乱最小化。
* 捕获并保留诊断信息。
* 通知合适的人员。
* 采用合适的方式结束异常活动。


## 不要过度使用异常
过度使用异常主要表现在两个方面：
* 把异常和普通错误混淆在一起，不再编写任何错误处理代码，而是以简单地引发异常来代苦所有的错误处理。
* 使用异常处理来代替流程控制。


>异常处理机制的初衷是将不可预期异常的处理代码和正常的业务逻辑处理代码分离，因此绝不要使用异常处理来代替正常的业务逻辑判断。
## 不要使用过于庞大的 try 块
因为 try 块里的代码过于庞大，业务过于复杂，就会造成 try 块中出现异常的可能性大大增加，从而导致分析异常原因的难度也大大增加。而且当时块过于庞大时，就难免在 try 块后紧跟大量的 except 块才可以针对不同的异常提供不同的处理逻辑。在同一个 try 块后紧跟大量的 except 块则需要分析它们之间的逻辑关系，反而增加了编程复杂度。

正确的做法是，把大块的 try 块分割成多个可能出现异常的程序段落，并把它们放在单独的 try 块中，从而分别捕获并处理异常。
## 不要忽略捕获的异常
既然己捕获到异常，那么 except 块理应做些有用的事情，及处理并修复异常。except 块整个为空，或者仅仅打印简单的异常信息都是不妥的！常建议对异常采取适当措施，比如：
* 处理异常。对异常进行合适的修复，然后绕过异常发生的地方继续运行；或者用别的数据进行计算，以代替期望的方法返回值；或者提示用户重新操作……总之，程序应该尽量修复异常，使程序能恢复运行。
* 重新引发新异常。把在当前运行环境下能做的事情尽量做完，然后进行异常转译，把异常包装成当前层的异常，重新传给上层调用者。
* 在合适的层处理异常。如果当前层不清楚如何处理异常，就不要在当前层使用 except 语句来捕获该异常，让上层调用者来负责处理该异常。

# logging模块用法
logging模块可以很容易地创建自定义的消息记录，这些日志消息将描述程序执行何时到达日志函数调用，并列出指定的任何变量当时的值。
```python
# 程序开头加上
import logging
logging.basicConfig(level=logging.DEBUG, format=' %(asctime)s - %(levelname)s - %(message)s')
```
工作原理：当Python记录一个事件的日志时，它会创建一个LogRecord对象，保存关于该事件的信息。
```python
import logging
logging.basicConfig(level=logging.DEBUG,
                    format=' %(asctime)s - %(levelname)s - %(message)s')
logging.debug('Start of program')


def factorial(n):
    logging.debug('Start of factorial(%s%%)' % (n))
    total = 1
    for i in range(n + 1):
        total *= i
        logging.debug('i is ' + str(i) + ', total is ' + str(total))
    logging.debug('End of factorial(%s%%)' % (n))
    return total


print(factorial(5))
logging.debug('End of program')
'''
运行结果：
2020-04-29 15:48:52,751 - DEBUG - Start of program
 2020-04-29 15:48:52,751 - DEBUG - Start of factorial(5%)
 2020-04-29 15:48:52,751 - DEBUG - i is 0, total is 0
 2020-04-29 15:48:52,751 - DEBUG - i is 1, total is 0
 2020-04-29 15:48:52,751 - DEBUG - i is 2, total is 0
 2020-04-29 15:48:52,751 - DEBUG - i is 3, total is 0
 2020-04-29 15:48:52,751 - DEBUG - i is 4, total is 0
 2020-04-29 15:48:52,751 - DEBUG - i is 5, total is 0
 2020-04-29 15:48:52,751 - DEBUG - End of factorial(5%)
 2020-04-29 15:48:52,751 - DEBUG - End of program
0
'''
```
## logging日志级别
| 级别     | 对应的函数         | 描述                                                             |
| -------- | ------------------ | ---------------------------------------------------------------- |
| DEBUG    | logging.debug()    | 最低级别，用于小细节，通常只有在诊断问题时，才会关心这些消息。   |
| INFO     | logging.info()     | 用于记录程序中一般事件的信息，或确认一切工作正常。               |
| WARNING  | logging.warning()  | 用于表示可能的问题，它不会阻止程序的工作，但将来可能会。         |
| ERROR    | logging.error()    | 用于记录错误，它导致程序做某事失败。                             |
| CRITICAL | logging.critical() | 最高级别，用于表示致命的错误，它导致或将要导致程序完全停止工作。 |
## logging禁用日志
在调试完程序后，可能并不希望所有这些日志消息出现在屏幕上，这时就可以使用 logging.disable() 函数禁用这些日志消息，从而不必进入到程序中，手工删除所有的日志调用。

logging.disable() 函数的用法是，向其传入一个日志级别，它会禁止该级别以及更低级别的所有日志消息。因此，如果想要禁用所有日志，只要在程序中添加 logging.disable(logging.CRITICAL) 即可
## 日志消息输出到文件中
将日志消息输出到文件中的实现方法很简单，只需要设置 logging.basicConfig() 函数中的 filename 关键字参数即可，例如：
```python
>>> import logging
>>> logging.basicConfig(filename='demo.txt', level=logging.DEBUG, format='%(asctime)s - %(levelname)s - %(message)s')
```
此程序中，将日志消息存储到了 demo.txt 文件中，该文件就位于运行的程序文件所在的目录。