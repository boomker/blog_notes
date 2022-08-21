# Python进阶强化训练之装饰器使用技巧进阶

## 如何使用函数装饰器？

实际案例

某些时候我们想为多个函数，统一添加某种功能，比如记时统计、记录日志、缓存运算结果等等。

我们不想在每个函数内一一添加完全相同的代码，有什么好的解决方案呢？

### 解决方案

定义装饰奇函数，用它来生成一个在原函数基础添加了新功能的函数，替代原函数

如有如下两道题：

**题目一**

斐波那契数列又称黄金分割数列，指的是这样一个数列：1,1,2,3,5,8,13,21,....,这个数列从第三项开始，每一项都等于前两项之和，求数列第n项。

**题目二**

一个共有10个台阶的楼梯，从下面走到上面，一次只能迈1-3个台阶，并且不能后退，走完整个楼梯共有多少种方法？

脚本如下：
```python
# 函数装饰器
def memp(func):
    cache = {}

    def wrap(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]

    return wrap

# 第一题
@memp
def fibonacci(n):
    if n <= 1:
        return 1
    return fibonacci(n - 1) + fibonacci(n - 2)


print(fibonacci(50))


# 第二题
@memp
def climb(n, steps):
    count = 0
    if n == 0:
        count = 1
    elif n > 0:
        for step in steps:
            count += climb(n - step, steps)
    return count


print(climb(10, (1, 2, 3)))
```
输出结果：
```python
C:\Python\Python35\python.exe E:/python-intensive-training/s11.py
20365011074
274

Process finished with exit code 0
```

## 如何为被装饰的函数保存元数据？

实际案例

在函数对象张保存着一些函数的元数据，例如：

|方法|描述|
|:--|:--|
|`f.__name__`|函数的名字|
|`f.__doc__`|函数文档字符串|
|`f.__module__`|函数所属模块名|
|`f.__dict__`|属性字典|
|`f.__defaults__`|默认参数元素|

我们在使用装饰器后，再使用上面的这些属性访问时，看到的是内部包裹函数的元数据，原来函数的元数据变丢失掉了，应该如何解决？

### 解决方案

使用标准库`functools`中的装饰器`wraps`装饰内部包裹函数，可以指定将原来函数的某些属性更新到包裹函数上面

```python
from functools import wraps

def mydecoratot(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        """wrapper function"""
        print("In wrapper")
        func(*args, **kwargs)
    return wrapper

@mydecoratot
def example():
    """example function"""
    print('In example')

print(example.__name__)
print(example.__doc__)
```
输出结果：
```bash
C:\Python\Python35\python.exe E:/python-intensive-training/s12.py
example
example function

Process finished with exit code 0
```

## 如何定义带参数的装饰器？

实际案例

实现一个装饰器，它用来检查被装饰函数的参数类型，装饰器可以通过指定函数参数的类型，调用时如果检测出类型不匹配则抛出异常，比如调用时可以写成如下：

```python
@typeassert(str, int, int)
def f(a, b, c):
   ......
```
或者
```python
@typeassert(y=list)
def g(x, y):
   ......
```

### 解决方案

提取函数签名：inspect.signature()

带参数的装饰器，也就是根据参数定制化一个装饰器，可以看成生产装饰器的工厂，美的调用`typeassert`，返回一个特定的装饰器，然后用他去装饰其他函数。

```python
from inspect import signature


def typeassery(*ty_args, **ty_kwargs):
    def decorator(func):
        # 获取到函数参数和类型之前的关系
        sig = signature(func)
        btypes = sig.bind_partial(*ty_args, **ty_kwargs).arguments

        def wrapper(*args, **kwargs):
            for name, obj in sig.bind(*args, **kwargs).arguments.items():
                if name in btypes:
                    if not isinstance(obj, btypes[name]):
                        raise TypeError('"%s" must be "%s" ' % (name, btypes[name]))
            return func(*args, **kwargs)

        return wrapper

    return decorator


@typeassery(int, str, list)
def f(a, b, c):
    print(a, b, c)

# 正确的
f(1, 'abc', [1, 2, 3])
# 错误的
f(1, 2, [1, 2, 3])
```
执行结果
```bash
C:\Python\Python35\python.exe E:/python-intensive-training/s13.py
1 abc [1, 2, 3]
Traceback (most recent call last):
  File "E:/python-intensive-training/s13.py", line 28, in <module>
    f(1, 2, [1, 2, 3])
  File "E:/python-intensive-training/s13.py", line 14, in wrapper
    raise TypeError('"%s" must be "%s" ' % (name, btypes[name]))
TypeError: "b" must be "<class 'str'>" 

Process finished with exit code 1
```

## 如何实现属性可修改的函数装饰器？

实际案例

为分析程序内那些函数执行时间开销较大，我们定义一个带timeout参数的函数装饰器，装饰功能如下：

1. 统计被装饰函数单词调用运行时间
2. 时间大于参数timeout的，将此次函数调用记录到log日志中
3. 运行时可修改timeout的值

### 解决方案

为包裹函数增加一个函数，用来修改闭包中使用的自由变量

在python3中使用nonlocal访问嵌套作用于中的变量引用

代码如下：

```python
from functools import wraps
import time
import logging
from random import randint


def warn(timeout):
    # timeout = [timeout]  # py2

    def decorator(func):
        def wrapper(*args, **kwargs):
            start = time.time()
            res = func(*args, **kwargs)
            used = time.time() - start
            if used > timeout:
                # if used > timeout:  # py2
                msg = '"%s": "%s" > "%s"' % (func.__name__, used, timeout)
                # msg = '"%s": "%s" > "%s"' % (func.__name__, used, timeout[0])  # py2
                logging.warn(msg)
            return res

        def setTimeout(k):
            nonlocal timeout
            timeout = k
            # timeout[0] = k  # py2

        wrapper.setTimeout = setTimeout
        return wrapper

    return decorator


@warn(1.5)
def test():
    print('In Tst')
    while randint(0, 1):
        time.sleep(0.5)


for _ in range(10):
    test()

test.setTimeout(1)

for _ in range(10):
    test()
```
输出结果:
```bash
C:\Python\Python35\python.exe E:/python-intensive-training/s14.py
In Tst
In Tst
WARNING:root:"test": "2.503000259399414" > "1.5"
In Tst
In Tst
In Tst
In Tst
In Tst
In Tst
In Tst
In Tst
In Tst
WARNING:root:"test": "1.0008063316345215" > "1"
In Tst
In Tst
In Tst
WARNING:root:"test": "1.0009682178497314" > "1"
In Tst
In Tst
WARNING:root:"test": "1.5025172233581543" > "1"
In Tst
In Tst
In Tst
In Tst

Process finished with exit code 0
```

## 如何在类中定义装饰器？

实际案例

实现一个能将函数调用信息记录到日志的装饰器：

1. 把每次函数的调用时间，执行时间，调用次数写入日志
2. 可以对被装饰函数分组，调用信息记录到不同日志
3. 动态修改参数，比如日志格式
4. 动态打开关闭日志输出功能

### 解决方案

为了让装饰器在使用上更加灵活，可以把类的实例方法作为装饰器，此时包裹函数中就可以持有实例对象，便于修改属性和扩展功能

代码如下：

```python
import logging
from time import localtime, time, strftime, sleep
from random import choice


class CallingInfo:
    def __init__(self, name):
        log = logging.getLogger(name)
        log.setLevel(logging.INFO)
        fh = logging.FileHandler(name + '.log')  # 日志保存的文件
        log.addHandler(fh)
        log.info('Start'.center(50, '-'))
        self.log = log
        self.formattter = '%(func)s -> [%(time)s - %(used)s - %(ncalls)s]'

    def info(self, func):
        def wrapper(*args, **kwargs):
            wrapper.ncalls += 1
            lt = localtime()
            start = time()
            res = func(*args, **kwargs)
            used = time() - start
            info = {}
            info['func'] = func.__name__
            info['time'] = strftime('%x %x', lt)
            info['used'] = used
            info['ncalls'] = wrapper.ncalls
            msg = self.formattter % info
            self.log.info(msg)
            return res

        wrapper.ncalls = 0
        return wrapper

    def SetFormatter(self, formatter):
        self.formattter = formatter

    def turnOm(self):
        self.log.setLevel(logging.INFO)

    def turnOff(self):
        self.log.setLevel(logging.WARN)


cinfo1 = CallingInfo('mylog1')
cinfo2 = CallingInfo('mylog2')

# 设置日志指定格式
# cinfo1.SetFormatter('%(func)s -> [%(time)s - %(ncalls)s]')
# 关闭日志
# cinfo2.turnOff()


@cinfo1.info
def f():
    print('in F')


@cinfo1.info
def g():
    print('in G')


@cinfo2.info
def h():
    print('in H')


for _ in range(50):
    choice([f, g, h])()
    sleep(choice([0.5, 1, 1.5]))
```