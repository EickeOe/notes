# 基础

### dict

python内置了字典：dict的支持，dict全程dictionart，在其他语言种也成为map，使用键值对存储，具有极快的查找速度。

```python
dict = {'name':'liu', age: 23}
print dict['name']
```

#### 设置value

```python
dict['name'] = 'junhui'
```

#### 获取vaue

```python
# 1,通过属性获取
dict['name']
# 如果key不存在，dict就会报错
# 避免错误需要通过in判断key是否存在
'name' in dict
dict['name']

# 2,通过get获取
dict.get('name')
# 如果key不存在，可以返回None,或者返回指定的value
dict.get('name', -1)
```

#### 删除key-value

```python
# 用pop(key)方法
dict.pop('name')
```

#### dict的特点

1. 查找和插入速度极快
2. 需要占用大量内存，内存浪费多

### Set

同java和js种的set

#### 语法

```python
s = set([1,2,3])
# 增加
s.add(4)
# 删除
s.remove(4)
```

### 数据转换

```python
int('123')
# 123

int(12.34)
# 12

float('12.34')
# 12.34

str(1.23)
# '1.23'

unicode(100)
# u'100'

bool(1)
# True

bool('')
# False

# 函数名赋值,同js
a = abs
```

### 函数

在python中，定义一个函数要使用`def`语句，一次写出函数名、括号、括号中的参数和冒号`:`，然后，在缩进块中编写函数体，函数的返回值用`return`语句返回。

```python
def my_bas(x):
    if x >= 0:
    return x
  else:
    return -x
```

#### 空函数

如果需要定义一个空函数，可以用`pass`语句：

```python
def nop():
    pass
```

`pass`作为占位符，可以放在`if`中，`while`中作为主体

#### 参数检查

调用函数时，如果参数个数不对，python会抛出`TypeError`异常

#### 返回多个值

python的返回多个值，其实返回的仍然是单一值，返回的是`tuple`类型的值

```python
def move(x, y):
  return x, y
x, y = move(1, 2)
print x, y
# 1, 2
# or
r = move(1, 2)
print r
# (1, 2)
```

#### 参数默认值

```python
def power(x, y=2):
  return x, y
print power(1, 2)
# (1, 2)
```

#### 可变参数

```python
def calc(*numbers):
  sum = 0
  for number in numbers:
    sum = sum + number
  return sum

# 如果传入的是数据，变成js中的解构写法
list = [1,2,3,4]
calc(*list)
```

### 切片

常用来取list中的部分元素。等于js中的split

```python
list = [1,2,3,4,5,6,7,8]
list[0:3]
# [1,2,3]

# 如果第一个索引是0，可以省略
list[:3]
#[1,2,3]

# 支持倒数切片
list[-2:-1]
# [7,8]
list[-3:]
#[6,7,8]

# 可以用来复制数组
list[:]
#[1,2,3,4,5,6,7,8]

# tuple也可以用切片操作，只是切片返回的值也为tuple
tu = (1,2,3,4,5)
tu[:3]
#(1,2,3)

# 字符串也可以用切片操作
'qwerty'[:3]
#'qwe'
```

#### 生成器

概念同js中的生成器(Generator)，同java中的迭代器(Iterator)

```python
# 创建一个生成器
list = [x * x for x in range(10)]
g = (x * x for x in range(10))

#使用生成器
g.next()

# 遍历生成器
for n in g:
  print n
```

#### 匿名函数

```python
map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9])
# lambda x: x * x等于
def f(x):
  return x * x
```

#### 装饰器

同js中的装饰器，同java的注解

### 模块

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

' a test module '

__author__ = 'Michael Liao'

import sys

def test():
    args = sys.argv
    if len(args)==1:
        print 'Hello, world!'
    elif len(args)==2:
        print 'Hello, %s!' % args[1]
    else:
        print 'Too many arguments!'

if __name__=='__main__':
    test()
```

第1行和第2行是标准注释，第1行注释可以让这个`hello.py`文件直接在Unix/Linux/Mac上运行，第2行注释表示.py文件本身使用标准UTF-8编码；

第4行是一个字符串，表示模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释；

第6行使用`__author__`变量把作者写进去，这样当你公开源代码后别人就可以瞻仰你的大名；

以上就是Python模块的标准文件模板，当然也可以全部删掉不写，但是，按标准办事肯定没错。

后面开始就是真正的代码部分。

```python
import sys
```

导入`sys`模块后，我们就有了变量`sys`指向该模块，利用`sys`这个变量，就可以访问`sys`模块的所有功能。

`sys`模块有一个`argv`变量，用list存储了命令行的所有参数。`argv`至少有一个元素，因为第一个参数永远是该.py文件的名称，例如：

运行`python hello.py`获得的`sys.argv`就是`['hello.py']`；

运行`python hello.py Michael`获得的`sys.argv`就是`['hello.py', 'Michael]`。

最后，注意到这两行代码：

```
if __name__=='__main__':
    test()
```

当我们在命令行运行`hello`模块文件时，Python解释器把一个特殊变量`__name__`置为`__main__`，而如果在其他地方导入该`hello`模块时，`if`判断将失败，因此，这种`if`测试可以让一个模块通过命令行运行时执行一些额外的代码，最常见的就是运行测试。

#### 作用域

模块内部使用的私有变量，通过`_`前缀来实现

类似`__xxx__`这样的变量是特殊变量，可以被直接引用，但是有特殊用途，比如上面的`__author__`，`__name__`就是特殊变量，`hello`模块定义的文档注释也可以用特殊变量`__doc__`访问，我们自己的变量一般不要用这种变量名；

类似`_xxx`和`__xxx`这样的函数或变量就是非公开的（private），不应该被直接引用，比如`_abc`，`__abc`等；

#### 安装第三方模块

在python中，安装第三方模块，是通过setuotools这个工具完成的。官方推荐`pip`

```
pip install xxx
```

#### 使用_\_future\\_\_

python版本迭代会有不兼容的api，所以python提供了`__future__`模块，把下一个新版本的特性导入到当前版本

```python
from __future__ import unicode_literals
```

### 面向对象

```python
class Per(object):
  def __init__(self, name):
    self.name = name

  def print_name(self):
    print self.name
```

`__init__`方法可以理解为js和java中的构造函数。

方法中的第一个参数永远是`self`，表示创建的示例本身，等同于js中的`this`指针。同时调用方法时，`self`不需要传递，python解释器自己会把示例传进去。

```python
per = Per('liu')
per.print_name()
# 'liu'
```

#### 继承和多态

```python
# 继承
class Animal(object):
  def run(self):
    print 'run'
class Dog(Animal):
  pass
class Cat(Animal):
  pass
```

#### 获取对象信息

**type()**

```python
# type()可以理解为js中的typeof

type(123)
# <type 'int'>

type(None)
# <type 'NoneType'>

a = Animal()
type(a)
# <class '__main__.Animal'>
```

**isinstance()**

`isinstance`可以通过查找继承关系来确定类型

```python
# 继承关系object -> Animal -> Dog
a = Animal()
d = Dog()
isinstance(d, Dog)
# True

isinstance(d, Animal)
# True
```

**dir()**

`dir`可以用来获取一个对象所有的属性和方法，它返回一个包含字符串的list

```python
dir('ABC')
#['__add__', '__class__', '__contains__', '__delattr__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__getslice__', '__gt__', '__hash__', '__init__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_formatter_field_name_split', '_formatter_parser', 'capitalize', 'center', 'count', 'decode', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'index', 'isalnum', 'isalpha', 'isdigit', 'islower', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
```

类似`__xxx__`的属性和方法在Python中都是有特殊用途的，比如`__len__`方法返回长度。在Python中，如果你调用`len()`函数试图获取一个对象的长度，实际上，在`len()`函数内部，它自动去调用该对象的`__len__()`方法，所以，下面的代码是等价的：

```python
len('ABC')
# 3
'ABC'.__len__()
# 3
```

我们自己写的类，如果也想用`len(myObj)`的话，就自己写一个`__len__()`方法：

```python
class MyObject(object):
    def __len__(self):
        return 100
  pass

obj = MyObject()
len(obj)
# 100
```

**get\&set\&hasattr**

`getattr`&`setattr`是用来获取和设置对象的属性的api。`hasattr`等同于`hasOwnProperty`。

```python
class MyObject(object):
    def __init(self, x):
    self.x = x
    pass
  pass
obj = MyObject(1)
hasattr(obj, 'x')
# True

getattr(obj, 'y')
# <bound method MyObject.y of <__main__.MyObject object at 0x108ca35d0>>

setattr(obj, 'y', 12)
```

#### _\_\_slots\__\_

动态给对象增加方法

```python
obj = MyObject(1)
# 单个实例增加方法或属性

#1.定义一个方法
def print_x(self):
  print self.x
# 引用MethodType
from types import MethodType
obj.print_x = MethodType(print_x, obj, MyObject)
obj.print_x()
# 1

# 给所有实例绑定方法
def set_y(self, y):
  self.y = y
MyObject.set_y = MethodType(set_y, None, MyObject)
```

使用\__slots\\_\_来限制`class`的属性，比如只允许对`MyObject`实例添加`y`和`z`属性。为了达到限制的目的，python允许再定义`class`的时候，定义一个特殊的`__slots__`变量，来限制改`class`能添加的属性。

```python
class MyObject(object):
  __slots__ = ('y', 'z')# 用tuple定义允许绑定的属性名称
```

> 注意：`__slots__`定义的属性仅对当前类起作用，对继承的子类是不起作用的。

#### 使用@property

@property用途等同于js对象中的getter\&setter

```python
class MyObject(object):
  @property
  def x(self):
    return self._x
  @x.setter
  def x(self, value):
    self._x = value
```

定义只读属性，只定义getter方法，不定义setter方法就是一个只读属性：

```python
class MyObject(object):
  @property
  def r(self):
    return self._r
```

#### 多重继承

```python
class Animal(object):
    pass

# 大类:
class Mammal(Animal):
    pass

class Bird(Animal):
    pass

class Runnable(object):
    def run(self):
        print('Running...')

class Flyable(object):
    def fly(self):
        print('Flying...')

# 各种动物:
class Dog(Mammal, Runnable):
    pass
```

**Mixin**

在设计类的继承关系时，通常，主线都是单一继承下来的，例如，`Ostrich`继承自`Bird`。但是，如果需要“混入”额外的功能，通过多重继承就可以实现，比如，让`Ostrich`除了继承自`Bird`外，再同时继承`Runnable`。这种设计通常称之为Mixin。

#### 自定义类

特殊函数，类似于java中对象的`toString()`、`hashCode()`、`compareTo()`等，python中也可以对class的类似函数进行重写。

**\_str\_**

```python
class MyObject(object):
  def __init__(self, x);
    self.x = x
  def __str__(self):
    return self.x
print MyObject(2)
# 2
```

`__str__`是用来是返回对象实例的字符串，如果直接在控制台输出实例对象，则需要重写`__repr__`方法，`__repr__`是为调试服务的。

**\_iter\_**

class的迭代器方法，该方法返回一个迭代对象(iterator)，如果重写此方法，则表示此class可以被迭代。

```python
class Fib(object):
    def __init__(self):
        self.a, self.b = 0, 1 # 初始化两个计数器a，b

    def __iter__(self):
        return self # 实例本身就是迭代对象，故返回自己

    def next(self):
        self.a, self.b = self.b, self.a + self.b # 计算下一个值
        if self.a > 100000: # 退出循环的条件
            raise StopIteration();
        return self.a # 返回下一个值
```

**\_getitem\_**

重写该方法，可以用来获取类似于数组下标的元素

```python
class Fib(object):
    def __getitem__(self, n):
        a, b = 1, 1
        for x in range(n):
            a, b = b, a + b
        return a
f = Fib()
f[0]
# 1
f[1]
# 1
```

list的切片，用`__getitem__`依然可以做到，重写`__getitem__`，判断传入的是`int`或者`slice`。

```python
class Fib(object):
    def __getitem__(self, n):
        if isinstance(n, int):
            a, b = 1, 1
            for x in range(n):
                a, b = b, a + b
            return a
        if isinstance(n, slice):
            start = n.start
            stop = n.stop
            a, b = 1, 1
            L = []
            for x in range(stop):
                if x >= start:
                    L.append(a)
                a, b = b, a + b
            return L
f = Fib()
f[0:5]
# [1, 1, 2, 3, 5]
```

**\_setitem\_**

把对象视作list或dict来对集合赋值。

**\_delitem\_**

用于删除某个元素。

**\_getattr\_**

在python中，可以使用`__getattr__`方法，来动态获取属性。

```python
class MyObject(object):
  def __init__(self, x):
    self.x = x
  def __getattr__(self, attr):
    if attr== 'y':
      return 1
obj = MyObject(2)
obj.y
# 1
```

> 注意：只有在没找到属性得情况下，才调用`__getattr__`，已有得属性，不会在`__getattr__`中查找

**\_call\_**

实现`__call__`方法，可以直接对实例进行调用。

```python
class MyObject(object):
  def __call__(self):
    print 'call'
obj = MyObject()
obj()
# call
```

#### 使用元类

**type()**

使用`type()`可以动态创建class，无需使用`class`关键字创建class

```python
def fn(self, name='world'):
  print('Hello, %s.' % name)
Hello = type('Hello', (object,), dict(hello=fn))
h = Hello()
h.hello()
# Hello, world.
```

`type()`需要传递三个参数：

1. class得名称。
2. 继承得父类集合，python支持多继承，需要使用tuple来传递。
3. class得方法名称与函数绑定

**mateclass**

mateclass用来创建类，然后使用类来创建实例对象，可以理解为类的蓝本。

mateclass允许创建类或者修改类，类就是mateclass的实例对象。

```python
# metaclass是创建类，所以必须从`type`类型派生：
class ListMetaclass(type):
  def __new__(cls, name, bases, attrs):
    attrs['add'] = lambda self, value: self.append(value)
    return type.__new__(cls, name, bases, attrs)
class MyList(list):
  __metaclass__ = ListMetaclass # 指示使用ListMetaclass来定制类
```

当创建`MyList`实例时，要通过`ListMetaclass.__new__()`来创建，在此，可以修改类的定义

`__new__`方法接收到的参数依次是：

1. 当前准备创建的类的对象；
2. 类的名字；
3. 类继承的父类集合；
4. 类的方法集合。

### 错误、调试、测试

#### error处理

```python
try:
  print 'try...'
  r = 10 / 0
  print 'result:', r
except ZeroDivisionError, e:
  print 'except:', e
finally:
  print 'finally...'
print 'END'
```

**debug**

python中可以使用`assert`来断言调试

```python
# err.py
def foo(s):
  n = int(s)
  assert n != 0, 'n is zero!'
  return 10 / n

def main():
  foo('0')
```

`assert`的意思是，表达式`n != 0`应该是`True`，否则，后面的代码就会出错。

如果断言失败，`assert`语句本身就会抛出`AssertionError`：

```
$ python err.py
Traceback (most recent call last):
  ...
AssertionError: n is zero!
```

启动python解释器时可以使用`-O`参数来关闭`assert`

```
$ python -O err.py
Traceback (most recent call last):
  ...
ZeroDivisionError: integer division or modulo by zero
```

关闭后，你可以把所有的`assert`语句当成`pass`来看。

**logging**

python的日志记录

**pdb**

python的调试器.

**单元测试**

```python
pass
```

### IO编程

**文件读写**

**读**

```python
# r标识符表示读
f = open('./test.txt', 'r')
f.read()
# 'Hello, world!'
f.close()
```

可以用with关键字对try except finally语句进行优化

```python
with open('file_name','r') as f:
  r=f.read()
```

**写**

```python
f = open('./test.txt', 'w')
```

### 进程和线程

python的os模块封装了常见的系统调用,包含`fork`可以在python程序中创建子进程.

```python
import os

print `Process (%s) start...` % os.getpid()
pid = os.fork()
if pid==0:
  print 'I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid())
else:
  print 'I (%s) just created a child process (%s).' % (os.getpid(), pid)
# Process (876) start...
# I (876) just created a child process (877).
# I am child process (877) and my parent is 876.
```

由于windows没有fork调用,所以上面的代码无法在windows中运行

**multiptocessing**

`multiprocessing`模块是跨平台版本的多进程模块.

`multiprocessing`模块提供了一个`Process`类来代表一个进程对象.

```python
from multiprocessing import Process
import os

# 子进程要执行的代码
def run_proc(name):
  print 'Run child process %s (%s)...' % (name, os.getpid())

  if __name__ == '__mainA__':
    print 'Parent process %s.' % os.getpid()
    p = Process(target = run_proc, args=('test',))
    print 'Process will start.'
    p.start()
    p.join()
    print 'Process end.'

# Parent process 928.
# Process will start.
# Run child process test (929)...
# Process end.
```

创建子进程时,只需要传入一个执行函数和函数的参数,创建一个`Process实例,用`start()\`方法启动.

`join()`方法可以等待子进程结束后再继续往下运行,通常用于进程间的同步.

#### Pool

如果要穷的那个大量的子进程,可以用进程池的方式批量创建子进程.

```python
from multiprocessing import Pool
import os, time, random

def long_time_task(name):
  print 'Run task%s (%s)...' %(name,os.getpid())
  start = time.time()
  time.sleep(random.random()*3)
  end = time.time()
  print 'Task %s runs %0.2f seconds.' % (name,(end - start))

if __name__ == '__main__':
    print 'Parent process %s.' % os.getpid()
  p = Pool()
  for i in range(5):
    p.apply_async(long_time_task, args=(i,))
  print 'Waiting for all subprocess done...'
  p.close()
  p.join()
  print 'All subprocess done.'
```

```
Parent process 669.
Waiting for all subprocesses done...
Run task 0 (671)...
Run task 1 (672)...
Run task 2 (673)...
Run task 3 (674)...
Task 2 runs 0.14 seconds.
Run task 4 (673)...
Task 1 runs 0.27 seconds.
Task 3 runs 0.86 seconds.
Task 0 runs 1.41 seconds.
Task 4 runs 1.91 seconds.
All subprocesses done.
```

对`Pool`对象调用`join()`方法会等待所有子进程执行外币,调用`join()`之前必须先调用`close()`调用`close()`之后就不能继续添加新的`Process`了.

**进程间通信**

`Process`之间肯定是需要通信的,操作系统提供了很多机制来实现进程间的通信.Python的`mulitiprocess`模块包装了底层的机制,提供了`Queue`,`Pipes`等多种方式来交换数据. Queue:

```python
from multiprocess import Process, Queue
import os, time, random

# 写数据进程执行的代码
def write(q):
```

**多线程**

python标准库提供了两个模块:`thread`&`threading`,`thread`是低级模块,`threading`是高级模块,对`thread`进行了封装.大多数情况下,仅使用`threading`高级模块.

```python
import time, threading

# 新县城执行的代码
def loop():
  print 'thread %s is running...' % threading.current_thread().name
  n = 0
  while n < 5:
    n = n + 1
    print 'thread %s >>> %s' % (threading.current_thread().name, n)
    time.sleep(1)
  print 'thread %s ended.' % threading.current_thread().name
print 'thread %s is running...' % threading.current_thread().name
t = threading.Thread(target = loop, name='LoopThread')
t.start()
t.join()
print 'thread %s ended.' % threading.current_thread().name
```

**Lock**

线程锁,等同于java中的lock

```python
import threading
lock = threading.Lock()
# 获取锁:
lock.acquire()
# 解锁
lock.release()
```

Python解释器由于设计时有GIL全局锁，导致了多线程无法利用多核

**ThreadLocal**

线程内局部变量

```python
import threading

# 创建全局ThreadLocal对象:
local_school = threading.local()

def process_student():
  print 'Hello, %s (in %s)' % (local_school.student, threading.current_thread().name)

def process_thread(name):
  # 绑定ThreadLocal的student:
  local_school.student = name
  process_student()

t1 = threading.Thread(target= process_thread, args=('Alice',), name='Thread-A')
t2 = threading.Thread(target= process_thread, args=('Bob',), name='Thread-B')
t1.start()
t2.start()
t1.join()
t2.join()
# Hello, Alice (in Thread-A)
# Hello, Bob (in Thread-B)
```

**分布式进程**

```python
# taskmanager.py
import random, time, Queue
from multiprocessing.managers import BaseManager

# 发送任务的队列:
task_queue = Queue.Queue()
# 接收结果的队列:
result_queue = Queue.Queue()

# 从BaseManager继承的QueueManager:
class QueueManager(BaseManager):
    pass

# 把两个Queue都注册到网络上, callable参数关联了Queue对象:
QueueManager.register('get_task_queue', callable=lambda: task_queue)
QueueManager.register('get_result_queue', callable=lambda: result_queue)
# 绑定端口5000, 设置验证码'abc':
manager = QueueManager(address=('', 5000), authkey='abc')
# 启动Queue:
manager.start()
# 获得通过网络访问的Queue对象:
task = manager.get_task_queue()
result = manager.get_result_queue()
# 放几个任务进去:
for i in range(10):
    n = random.randint(0, 10000)
    print('Put task %d...' % n)
    task.put(n)
# 从result队列读取结果:
print('Try get results...')
for i in range(10):
    r = result.get(timeout=10)
    print('Result: %s' % r)
# 关闭:
manager.shutdown()
```

```python
# taskworker.py

import time, sys, Queue
from multiprocessing.managers import BaseManager

# 创建类似的QueueManager:
class QueueManager(BaseManager):
    pass

# 由于这个QueueManager只从网络上获取Queue，所以注册时只提供名字:
QueueManager.register('get_task_queue')
QueueManager.register('get_result_queue')

# 连接到服务器，也就是运行taskmanager.py的机器:
server_addr = '127.0.0.1'
print('Connect to server %s...' % server_addr)
# 端口和验证码注意保持与taskmanager.py设置的完全一致:
m = QueueManager(address=(server_addr, 5000), authkey='abc')
# 从网络连接:
m.connect()
# 获取Queue的对象:
task = m.get_task_queue()
result = m.get_result_queue()
# 从task队列取任务,并把结果写入result队列:
for i in range(10):
    try:
        n = task.get(timeout=1)
        print('run task %d * %d...' % (n, n))
        r = '%d * %d = %d' % (n, n, n*n)
        time.sleep(1)
        result.put(r)
    except Queue.Empty:
        print('task queue is empty.')
# 处理结束:
print('worker exit.')
```
