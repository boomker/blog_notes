# Python进阶强化训练之类与对象深度技术进阶

## 如何派生内置不可变类型并修改实例化行为

**实际案例**

我们想自定义一种新类型的元祖，对于传入的可迭代对象，我们只保留其中init类型且值大于0的元素，例如`IntTuple([1,-1,'abc',6,['x','y'],3])=>(1,6,3)`

要求`IntTuple`是内置的`tuple`的子类，如何实现？

**解决方案**

定义类`IntTuple`继承内置`tuple`，并实现`__new__`，修改实例化行为。

**解决代码**

```python
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

# Created by 安生 on 2017/2/19

class IntTuple(tuple):
    def __new__(cls, iterable):
        g = (x for x in iterable if isinstance(x, int) and x > 0)
        return super(IntTuple, cls).__new__(cls, g)

    def __init__(self, iterable):
        super(IntTuple, self).__init__()


t = IntTuple([1, -1, 'abc', 6, ['x', 'y'], 3])
print(t)
```
输出
```bash
/Library/Frameworks/Python.framework/Versions/3.5/bin/python3.5 "/Users/ansheng/MyPythonCode/Code/7-1 如何派生内置不可变类型并修改实例化行为.py"
(1, 6, 3)

Process finished with exit code 0
```

## 如何为创建大量实例节省内存

**实际案例**

某网络游戏中，定义了玩家类Player(id,name,status,...)，每有一个在线玩家，在服务器程序内侧有一个Player的实例，当在线人数很多时，将产生大量实例(数百万级)

如何降低这些大量实例的内存开销？

**解决方案**

定义类的`__slots__`属性，它是用来声明实例属性名字的列表。

**解决代码**

```python
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

# Created by 安生 on 2017/2/19

class Plater:
    """ 关闭了动态绑定类属性的__dict__，以便节省内存，__dict__默认占用1024个字节 """
    __slots__ = ['uid', 'name', 'status', 'level']  # 声明实例拥有的属性，阻止动态属性绑定

    def __init__(self, uid, name, status=0, level=1):
        self.uid = uid
        self.name = name
        self.status = status
        self.level = level
```

## 如何让对象支持上下文管理

**实际案例**

我们实现了一个Telnet客户端的类`TelnetClient`，调用实例的`start()`方法启动客户端与服务器交互，交互完毕后需调用`cleanup()`方法，关闭已链接的socket，以及将操作历史记录写入文件并关闭.

能否让`TelnetClient`的实例实现上下文管理协议，从而替代手工调用`cleanup()`方法

**解决方案**

实现上下文管理协议，需定义实例的`__enter__`，`__exit__`方法，他们分别在`with`开始和结束时被调用。

**解决代码**

```python
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

# Created by 安生 on 2017/2/19

from telnetlib import Telnet
from sys import stdin, stdout
from collections import deque


class TelnetClient:
    def __init__(self, addr, port=23):
        self.addr = addr
        self.port = port
        self.tn = None

    def start(self):
        # user
        t = self.tn.read_until('login: ')
        stdout.write(t)
        user = stdin.readline()
        self.tn.write(user)

        # password
        t = self.tn.read_until('Password: ')
        if t.startswith(user[:-1]): t = t[len(user) + 1:]
        stdout.write(t)
        self.tn.write(stdin.readline())

        t = self.tn.read_until('$ ')
        stdout.write(t)
        while True:
            uinput = stdin.readline()
            if not uinput:
                break
            self.history.append(uinput)
            self.tn.write(uinput)
            t = self.tn.read_until('$ ')
            stdout.write(t[len(uinput) + 1:])

    def __enter__(self):
        self.tn = Telnet(self.addr, self.port)  # 创建链接
        self.history = deque()  # 创建历史记录队列
        return self  # 返回当前的实例

    def __exit__(self, exc_type, exc_val, exc_tb):
        """
        :param exc_type: 异常类型
        :param exc_val: 异常值
        :param exc_tb: 跟踪的栈
        :return:
        """
        self.tn.close()  # 关闭链接
        self.tn = None
        with open(self.addr + '_history.txt', 'w') as f:
            """ 把历史记录写入文件中 """
            f.writelines(self.history)
            # return True  # 如果返回True，即使出错也会往下继续执行


with TelnetClient('127.0.0.1') as client:
    client.start()
```

## 如何创建可管理的对象属性

**实际案例**

在面向对象变成中，我们把方法(函数)看做对象的接口，直接访问对象的属性可能是不安全的，或设计上不够灵活，但是使用调用方法在形式上不如访问属性简洁.

```python
circle.getRadius()
circle.setRadius(5.0)  # 繁
```

```python
circle.radius
circle.radius = 5.0  # 简
```

能否在形式上是属性访问，但实际上调用方法？

**解决方案**

使用`property`函数为类创建可管理属性,`fget/fset`对应相应属性访问

**解决代码**

```python
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

# Created by 安生 on 2017/2/19

from math import pi


class Circle:
    def __init__(self, radius):
        self.radius = radius

    def getRadius(self):
        return self.radius

    def setRadius(self, value):
        if not isinstance(value, (int, float)):
            raise ValueError('wrong type. ')
        self.radius = float(value)

    def getArea(self):
        return self.radius ** 2 * pi

    R = property(getRadius, setRadius)


c = Circle(3.2)
print(c.R)
c.R = 5.9
print(c.R)
```
输出
```python
/Library/Frameworks/Python.framework/Versions/3.5/bin/python3.5 "/Users/ansheng/MyPythonCode/Code/7-4 如何创建可管理的对象属性.py"
3.2
5.9

Process finished with exit code 0
```

## 如何让类支持比较操作

**实际案例**

有时我们希望自定义类，实例间可以使用`<`、`<=`、`>`、`>=`、`==`、`!=`符号进行比较，我们自定义比较的行为，例如，有一个矩形的类，我们希望比较两个矩形的实例时，比较的是他们的面积.

**解决方案**

比较符号运算符重载，需要实现以下方法:`__lt__`，`__le__`，`__gt__`，`__ge__`，`__eq__`，`__ne__`，或者使用标准库下的`functools`下的类装饰器`total_ordering`可以简化此过程.

**解决代码**

```python
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

# Created by 安生 on 2017/2/19

from functools import total_ordering
from abc import ABCMeta, abstractmethod


@total_ordering
class Shape(object):
    __metaclass__ = ABCMeta

    @abstractmethod
    def area(self):
        pass

    def __lt__(self, other):
        if not isinstance(other, Shape):
            raise TypeError('other not Shape')
        return self.area() < other.area()

    def __eq__(self, other):
        if not isinstance(other, Shape):
            raise TypeError('other not Shape')
        return self.area() == other.area()


class Rectangle(Shape):
    def __init__(self, w, h):
        self.w = w
        self.h = h

    def area(self):
        return self.w * self.h


class Circle(Shape):
    def __init__(self, r):
        self.r = r

    def area(self):
        return self.r ** 2 * 3.14


r1 = Rectangle(5, 3)
r2 = Rectangle(5, 4)
c1 = Circle(3)

print(c1 <= r1)
print(r1 > c1)
print(r1 < r2)
```
输出
```python
/Library/Frameworks/Python.framework/Versions/3.5/bin/python3.5 "/Users/ansheng/MyPythonCode/Code/7-5 如何让类支持比较操作.py"
False
False
True

Process finished with exit code 0
```

## 如何使用描述符对实例属性做类型检查

**实际案例**

在某些项目中，我们实现了一些类，并希望能像静态类型语言那样对它们的实例属性做类型检查：

```python
p = Person()
p.name = 'Bob'   # 必须是str
p.age = 18       # 必须是int
p.height = 1.83  # 必须是float
```

要求：

1. 可以对实例变量名制定类型
2. 赋予不正确类型时抛出异常

**解决方案**

使用描述符来实现需要类型检查的属性，分别实现`__get__`，`__set__`，`__delete__`方法，在`__set__`内使用`isinstance`函数做类型检查。

**解决代码**

```python
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

# Created by 安生 on 2017/2/19

class Attr:
    def __init__(self, name, type_):
        self.name = name  # 实例名字
        self.type_ = type_  # 类型

    def __get__(self, instance, owner):
        return instance.__dict__[self.name]

    def __set__(self, instance, value):
        if not isinstance(value, self.type_):
            raise TypeError('expected an %s' % self.type_)
        instance.__dict__[self.name] = value

    def __delete__(self, instance):
        del instance.__dict__[self.name]


class Person:
    name = Attr('name', str)
    age = Attr('age', int)
    height = Attr('height', float)


p = Person()
p.name = 'Bob'
print(p.name)

p.age = '17'
```
输出
```python
/Library/Frameworks/Python.framework/Versions/3.5/bin/python3.5 "/Users/ansheng/MyPythonCode/Code/7-6 如何使用描述符对实例属性做类型检查.py"
Bob
Traceback (most recent call last):
  File "/Users/ansheng/MyPythonCode/Code/7-6 如何使用描述符对实例属性做类型检查.py", line 33, in <module>
    p.age = '17'
  File "/Users/ansheng/MyPythonCode/Code/7-6 如何使用描述符对实例属性做类型检查.py", line 16, in __set__
    raise TypeError('expected an %s' % self.type_)
TypeError: expected an <class 'int'>

Process finished with exit code 1
```

## 如何在环状数据结构中管理内存

**实际案例**

在Python中，垃圾回收器通过引用计数来回收垃圾对象，但某些环状数据结构，存在对象间的循环引用，比如书的父节点引用子节点，子节点也同时引用父节点，此时同事del掉父子节点，两个对象不能立即回收，如何解决此类的内存管理问题？

**解决方案**

使用标准库`weakref`，它可以创建一种访问对象但不增加引用计数的对象。

**解决代码**

```python
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

# Created by 安生 on 2017/2/19

import weakref


class Data:
    def __init__(self, value, owner):
        self.owner = weakref.ref(owner)
        self.value = value

    def __str__(self):
        return "%s's data, value is %s" % (self.owner(), self.value)

    def __del__(self):
        print('in Data.__del__')


class Node:
    def __init__(self, value):
        self.data = Data(value, self)

    def __del__(self):
        print('in Node.__del__')


node = Node(100)
del node
input('wait...')
```
输出
```bash
/Library/Frameworks/Python.framework/Versions/3.5/bin/python3.5 "/Users/ansheng/MyPythonCode/Code/7-7 如何在环状数据结构中管理内存.py"
in Node.__del__
in Data.__del__
wait...
```

## 如何通过实例方法名字的字符串调用方法

**实际案例**

某项目中，我们的代码使用了三个不同库中的图形类:

1. Circle
2. Triangle
3. Rectangle

他们都有一个获取图形面积的接口，但接口名字不同，我们可以实现一个统一的获取面积的函数，使用没种方法名进行尝试，调用相应类的接口

**解决方案**

1. 使用内置函数getattr，通过名字在实例上获取方法对象，然后调用；
2. 使用标准库operator下的methodcaller函数调用；

**解决代码**

```python
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

# Created by 安生 on 2017/2/19

class Circle:
    def __init__(self, r):
        self.r = r

    def area(self):
        return self.r ** 2 * 3.14


class Rectangle:
    def __init__(self, w, h):
        self.w = w
        self.h = h

    def get_area(self):
        return self.w * self.h


class Triangle:
    def __init__(self, a, b, c):
        self.a = a
        self.b = b
        self.c = c

    def getArea(self):
        a, c, b = self.a, self.b, self.c
        p = (a + b + c) / 2
        area = (p * (p - a) * (p - b) * (p - c)) ** 0.5
        return area


def getArea(shape):
    for name in ('area', 'get_area', 'getArea'):
        f = getattr(shape, name, None)
        if f:
            return f()


shape1 = Circle(2)
shape2 = Triangle(3, 4, 5)
shape3 = Rectangle(6, 4)

shapes = [shape1, shape2, shape3]
print(list(map(getArea, shapes)))
```
输出
```python
/Library/Frameworks/Python.framework/Versions/3.5/bin/python3.5 "/Users/ansheng/MyPythonCode/Code/7-8 如何通过实例方法名字的字符串调用方法.py"
[12.56, 6.0, 24]

Process finished with exit code 0
```
方法2
```python
>>> s = 'abc123abc456'
>>> s.find('abc',4)
6
>>> from operator import methodcaller
>>> methodcaller('find','abc',4)(s)
6
```