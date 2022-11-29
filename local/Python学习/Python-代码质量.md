---
title: Python-代码质量
date: 2020-04-27 17:52:09
categories: 
- 计算机基础
- Python
tags:
- Python
toc: true
---
# 参考链接
* [怎样才能写出 Pythonic 的代码？](https://www.zhihu.com/question/21408921/answer/129036707)
* [Python优美代码的一些方法](https://www.zhihu.com/question/37751951/answer/73425339)
# 内置函数
## enumerate类
```python
from __future__ import print_function

L = [i*i for i in range(5)]
# 普通写法
index = 0
for data in L:
    index += 1
    print(index, ':', data)
# enumerate类的使用
for index, data in enumerate(L, 1):
    print(index, ':', data)
```
在保证代码可读性的前提下，代码越少越好。显然，使用enumerate效果就好很多。
## reversed
Python中的列表支持切片操作，可以像L[::-1]这样取reverse列表。
## any
遍历一个二维的元组，判断是否存在Non_unique为0,Null列不为YES的记录
```python
# 普通
def has_primary_key():
    for row in rows:
        if row[1] == 0 and row[9] != 'YES':
            return True
        return False
# 使用any
def has_prmary_key1():
    return any(row[1] == 0 and row[9] != 'YES' for row in rows)
```
# Python中的小细节
## raise SystemExit
在程序检测某种错误时，打印错误信息，并退出程序
```python
raise SystemExit('It failed!')
```
## 文件的x模式
需求：写一个文件，如果该文件已经存在，则不写，否则以w模式打开文件并写入：
```python
import os
if not os.paht.exists('filename'):
    f.write('Hello\n')
else:
    print('File already exists!')
# 使用x模式
with open('filename', 'xt') as f:
    f.write('Hello\n')
```
## ConfigParser
提供生成连接字符串的功能，用于读取配置参数。如下：
```txt
$cat db.conf
[DEFAULT]
conn_str=%(dbn)s://(%user)s:%(pw)s@%(host)s:%(port)s/%(db)s
dbn=mysql
user=root
pw=root
host=localhost
port=3306
db=test
```
# 合理的使用数据结构
## 字典的get传递默认值
```python
port = kwargs.get('port')
if port is None:
    port = 3306
```
字典的get方法支持提供默认参数，在字典没有值的情况下，将返回用户提供的默认参数，高质量的写法：
```python
port = kwargs.get('port', 3306)
```
在调用pop()函数是，需要返回最后一个元素：
```python
L = [1,2,3,4]
last = L[-1]
L.pop()
# 优化
last = L.pop()
```
## dfaultdict & Counter
需求1：假设字典的value是list，先判断key是否已经存在，如果不存在，新建一个list并赋值给key，如果已经存在，则调用list的append()方法，将值添加进去。
```python
# 普通
d = {}
for key, value in pairs:
    if key not in d:
        d[key] = []
    d[key].append(value)
# 使用defaultdict
d = defaultdict(list)
for key, value in pairs:
    d[key].append(value)
```
需求2：统计一个文件中，每个单词出现的次数，使用字典的写法如下：
```python
# 普通写法
d = {}
with open('/etc/passwd/) as f:
    for line in f:
        for word in line.strip().split(':'):
            if word not in d:
                d[word] = 1
            else:
            d[word] += 1
# 使用collections中的Counter
word_counts = Counter()
with open('/etc/passwd') as f:
    for line in f:
        word_counts.update(line.strip().split(':'))
```
需求3：打印出现次数最多的三个单词，使用字典如下：
```python
result = sorted(zip(d.values(),d.keys()), reverse=True)[:3]
# Couter直接提供
for key, val in (word_counts.most_common(3)):
    print(key,':', val)
```
## nametuple
监控系统，可以从/proc/diskstats中获取磁盘的详细信息
```shell
$ cat /proc/diskstats 
   7       0 loop0 59 0 2116 3486 0 0 0 0 0 120 3420 0 0 0 0
   7       1 loop1 132 0 2288 3816 0 0 0 0 0 164 3752 0 0 0 0
```
如果使用下标访问，计算较复杂，可以使用Python中的命名元组，即collections中的namedtuple，如下：
```python
DiskDevice = collections.namedtuple('DiskDevice', 'major_number minor_number device_name read_count read_merged_count'...)
# 有了命名空间，通过命名元组，能够通过属性访问各个字段，获取磁盘监控的代码如下：
def get_disk_info(disk_name):
    with open("/proc/diskstats") as f:
        for line in f:
            if line.split()[2] == disk_name:
                return DiskDevice(*(line.split()))
```
# 使用高级并发工具
实例：生产者消费者模型，生产者向队列中放东西，消费者从队列中取东西。创建一个锁来保证线程间操作的互斥性，当队列满时，生产者进入等待状态，当队列空的时候，消费者进入等待状态。如下：
```python
from threading import Thread, Condition
import time
import random

queue = []
MAX_NUM = 10
Condition = Condition()


class ProducerThread(Thread):
    def run(self):
        nums = range(5)
        global queue
        while True:
            Condition.acquire()
            if len(queue) == MAX_NUM:
                print("Queue full, producer is waiting")
                Condition.wait()
                print("Space in queue, Consumer notified the producer")
            num = random.choice(nums)
            queue.append(num)
            print("Produced", num)
            Condition.notify()
            Condition.release()
            time.sleep(random.random())


class ConsumerThread(Thread):
    def run(self):
        global queue
        while True:
            Condition.acquire()
            if not queue:
                print("Nothing in queue, consumer is waiting")
                Condition.wait()
                print(
                    "producer added something queue and notified the consumer")
            num = queue.pop()
            print("Consumed", num)
            Condition.notify()
            Condition.release()
            time.sleep(random.random())


ProducerThread().start()
ConsumerThread().start()
```
对于同步问题，可以直接使用Queue，Queue提供线程安全的队列，适用解决生产者和消费者问题，支持阻塞读、阻塞写，写法如下：
```python
from threading import Thread
import time
import random
from queue import Queue

queue = Queue(10)


class ProducThread(Thread):
    def run(self):
        nums = range(5)
        global queue
        while True:
            num = random.choice(nums)
            queue.put(num)
            print("Produced", num)
            time.sleep(random.random())


class ConsumerThread(Thread):
    def run(self):
        global queue
        while True:
            num = queue.get()
            queue.task_done()
            print("Consumed", num)
            time.sleep(random.random())


ProducThread().start()
ConsumerThread().start()
```
使用Queue后，代码量减少。同时，在并发编程，不需要手动启动一个线程或进程，可以使用并发工具，内置的map是单线程运行的，如果涉及到网络请求或大量CPU计算，速度相对会慢很多，需要使用并发的map，如下：
```python
import requests
from multiprocessing import Pool

def get_website_data(url):
    r = requests.get(url)
    return r.url

def main():
    urls = ['http://www.google.com'.
    'https://www.baidu.com',
    'http://www.163.com']
    pool = Pool(2)
    print(pool.map(get_website_data, urls))

main()
```
为了保证线程兼容，模型提供了dummy，用以提供线程池的实现，如下：
```python
from multiprocessing.dummy import Pool
# 可以快速的在线程池和进程池之间切换
```
# 使用装饰器
实例：有两个模块，A模块需要给B模块发消息，B模块检查A模块发送过来的参数，没有问题则进行处理，对于检查参数的操作，使用装饰器的代码如下：
```python
import inspect
import functools

def check_args(parameters):
    """
    check paramenters of action
    """
    def decorated(f):
        """
        decorator
        """
        @functools.wraps
        def wrapper(*args, **kwargs):
            func_args = inspect.getcallargs(f, *args, **kwargs)
            msg = func_args.get('msg')

            for item in parameters:
                if msg.body_dict.get(item) is None:
                    return False, "check faild, %s is not found" % item
            return f(*args, **kwargs)
        return wrapper
    return decorated

#使用装饰器
class AsyncMsgHandle(MsgHandler):
    @check.check_args(['Containerldentifier', 'MonitorSecretKey', "InstanceID", "UUID"])
    def init_container(self, msg):
        pass
```
# Python中的设计模式
## 单例模式
```python
class Borg:
    _shared_state = {}
    def __init__(self):
        self.__dict__ = self._shared_state
```
只要所有的实例共享状态、行为一直，就达到单例的目的，通过Borg可以创建任意数量的实例。在Python中，模块初始化一次，import机制是线程安全的，因此模块本身就是单例的实现。

## 工厂模式
```cpp
// CPP的工厂模式实现
class Shape
    class Circle: public Shape
    class Square: public Shape

    Shape *Shape::factory(const string &type)
    {
        if(type == "Circle")
            return new Circle;
        if(type == "Square")
            return new Square;
    }
```
上述单例的Python实现如下：
```python
class Shape:
    pass
class Circle(Shape):
    pass
class Square(Shape):
    pass

for name in ["Circle", "Square"]:
    cls = globals()[name]
    obj = cls()
```
理解：Python中的类是可调用的对象，在import后，存在于当前命名空间中。可以先通过名字获取类，再用类构造出对象，Python比C++少一个需要维护的函数。

# 优美代码注意事项
* 写代码跟写作文一样，条理要清晰
* 准确无歧义，完整且清晰
* 排版清楚，添加必要的空行
* 添加必要的注释、注意标点符号
* 保证可读性且代码尽可能短小

