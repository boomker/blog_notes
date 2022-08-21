# Python基础编程第二版（修订版）

## 第一章 快速改造：基础知识

Python2与Python3的一些小插曲

```python
# 在Python2内两个整数相除，得到结果的小数部分会被截断
>>> 1/2
0
>>> 12/7
1
# 如果要避免这种情况，当然可以使用小数（浮点数）的方式进行除法咯
>>> float(12)/float(7)
1.7142857142857142
# 或者你可以倒入division模块进行相除
>>> from __future__ import division
>>> 12/7
1.7142857142857142
# 如果使用的是//号，即使是浮点数也会被整除
>>> float(12) // float(7)
1.0
# 你还可以使用%号取余
>>> 12 % 7
5
```

---

```python
# 然而在Python3中就不会出现这种情况
>>> 1/2
0.5
>>> 12/7
1.7142857142857142
```

在Python3中`print`变成了函数，所以调用的时候需要执行`print("string")`而不像Python2中`print "string"`

四舍五入

```python
>>> round(10.5)
11.0
>>> round(10.4)
10.0
```

计算一个数的平方

```python
>>> from math import sqrt
>>> sqrt(9)
3.0
>>> sqrt(10)
3.1622776601683795
```

__future__

通过它可以导入那些未来会成为标准Python组成的新特性。

使用反斜线`\`对单引号或双引号进行转义

```python
>>> 'Let's go!'
  File "<stdin>", line 1
    'Let's go!'
         ^
SyntaxError: invalid syntax
>>> 'Let\'s go!'
"Let's go!"
```

str和repr

`str`、`repr`和反引号是将Python值转换为字符串的三种方式。

```bash
>>> print repr("Hello, World!")
'Hello, World!'
>>> print str("Hello, World!")
Hello, World!
>>> age = 21
>>> print "My age is:" + `age`
My age is:21
>>> print "My age is:" + repr(age)
My age is:21
>>> print "My age is:" + str(age)
My age is:21
>>> print "My age is:" + age
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot concatenate 'str' and 'int' objects
```

`input`和`raw_input`

```python
# input会假设用户输入的是合法的Python表达式，如果输入的是一个字符串或者数字则没有问题
>>> input("Hello, ")
Hello, ansheng
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<string>", line 1, in <module>
NameError: name 'ansheng' is not defined
>>> input("Hello, ")
Hello, "ansheng"
'ansheng'
>>> input("Hello, ")
Hello, 1
1
```

```python
# raw_input就不会这样，他会把所有的输入当作原始数据
>>> name = "ansheng"
>>> raw_input("hello, ")
hello, name
'name'
>>> input("hello, ")
hello, name
'ansheng'
```

原始字符串

```python
>>> path = 'c:\nwindows'
>>> print path
c:
windows
# 使用反斜线对其本身进行转义
>>> path = 'c:\\nwindows'
>>> print path
c:\nwindows
# 使用r让字符串变成原始字符串，即r后面是什么就会输出什么
>>> path = r'c:\nwindows'
>>> print path
c:\nwindows
# 并且不能在原始字符串结尾输入反斜线
>>> path = r'c:\nwindows\'
  File "<stdin>", line 1
    path = r'c:\nwindows\'
                         ^
SyntaxError: EOL while scanning string literal
```

表达式

表达式是计算机程序组成的部分，它用于表示值。

第一章所涉及到的函数

|函数|描述|
|:--|:--|
|`abs(number)`|返回数字的绝对值|
|`cmath.sqrt(number)`|返回平方根，也可以应用于浮点数|
|`float(object)`|将字符串和数字转换为浮点数|
|`help()`|提供交互式帮助|
|`input(prompt)`|获取用户输入，输入的值是一个表达式|
|`int(object)`|将字符串和浮点数转换为整数|
|`long(object)`|将字符串和浮点数转换为长整数|
|`math.ceil(number)`|返回数的上入整数，返回值的类型为浮点数|
|`math.floor(number)`|返回数的下设整数，返回值的类型为浮点数|
|`math.sqrt(number)`|返回平方根，不适用于负数|
|`pow(x,y[,z])`|返回x的y次幂（所得结果对z取模）|
|`raw_input(prompt)`|获取用户输入，结果被看作是原始字符串|
|`repo(object)`|返回值的字符串表示形式|
|`round(number[, ndigits])`|根据给定的精度对数字进行四舍五入|
|`str(object)`|将值转换为字符串|

## 第二章 列表和元组

列表和元组的主要区别就在于列表中的元素是可以修改的，而元组中的元素则不可以被修改。

```python
# 创建列表
>>> L = list([1,2,3])
# 列表中的第一个元素的值
>>> L[0]
1
# 修改列表中第一个元素的址
>>> L[0] = 2
>>> L[0]
2
```

```python
# 创建一个元组
>>> T = tuple([1,2,3])
>>> T
(1, 2, 3)
>>> T[0]
1
>>> T[0] = 3
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```

获取每四个元素的第一个值

```python
>>> numbers[::4]
[1, 5, 9]
```

如果步长为负数，切片从右到左提取元素

```python
>>> numbers[8:3:-1]
[9, 8, 7, 6, 5]
>>> numbers[10:0]
[]
>>> numbers[10:0:-2]
[10, 8, 6, 4, 2]
>>> numbers[0:10:-2]
[]
>>> numbers[5::-1]
[6, 5, 4, 3, 2, 1]
>>> numbers[5::]
[6, 7, 8, 9, 10]
>>> numbers[5::-1]
[6, 5, 4, 3, 2, 1]
>>> numbers[5::-2]
[6, 4, 2]
```

序列乘法

```python
>>> 'python' * 5
'pythonpythonpythonpythonpython'
>>> [5] * 10
[5, 5, 5, 5, 5, 5, 5, 5, 5, 5]
```

通过字符串创建列表

```python
>>> list('Hello, World!')
['H', 'e', 'l', 'l', 'o', ',', ' ', 'W', 'o', 'r', 'l', 'd', '!']
# 通过join将字符组成的列表转换为字符串
>>> somelist = list('Hello, World!')
>>> ''.join(somelist)
'Hello, World!'
```

分片赋值

```python
>>> lange = list('Perl')
>>> lange
['P', 'e', 'r', 'l']
>>> lange[1:] = list('ython')
>>> lange
['P', 'y', 't', 'h', 'o', 'n']
>>> ''.join(lange)
'Python'
# 插入元素
>>> numbers = [1,5]
>>> numbers[1:1] = list('234')
>>> numbers
[1, '2', '3', '4', 5]
# 删除元素
>>> numbers[1:4] = []
>>> numbers
[1, 5]
```

`+`与`extend`的区别

`extend`方法修改了被扩展的序列，而`+`则会返回一个全新的列表

```python
>>> a = [1,2,3]
>>> b = [4,5,6]
>>> a + b
[1, 2, 3, 4, 5, 6]
>>> a
[1, 2, 3]
>>> a.extend(b)
>>> a
[1, 2, 3, 4, 5, 6]
```

列表赋值

```python
>>> x = [5,6,3,5,7,2]
>>> y = x[:]
>>> x.sort()
>>> x
[2, 3, 5, 5, 6, 7]
>>> y
[5, 6, 3, 5, 7, 2]
```
如果直接把`x`赋值给`y`，那么如果`x`的值改动了，`y`的值也会改动，因为`y`的内容指向的内存地址是`x`：

```python
>>> x = [5,6,3,5,7,2]
>>> y = x
>>> id(x)
4314650528
>>> id(y)
4314650528
>>> x.sort()
>>> id(x)
4314650528
>>> id(y)
4314650528
>>> x
[2, 3, 5, 5, 6, 7]
>>> y
[2, 3, 5, 5, 6, 7]
```

高级排序

```python
>>> x = ['asdsa','sd','gdet']
>>> x.sort()
>>> x
['asdsa', 'gdet', 'sd']
>>> x = ['asdsa','sd','gdet']
>>> x.sort(key=len)
>>> x
['sd', 'gdet', 'asdsa']
```

```python
# 倒序
>>> x = [5,3,9,1]
>>> x.sort(reverse=True)
>>> x
[9, 5, 3, 1]
```

第二章所涉及到的函数

|函数|描述|
|:--|:--|
|`cmp(x,y)`|比较两个值|
|`len(seq)`|返回序列的长度|
|`list(seq)`|把序列转换为列表|
|`max(args)`|返回序列或者参数集合中的最大值|
|`min(args)`|返回序列或者参数集合中的最小值|
|`reversed(seq)`|对序列进行反向迭代|
|`sorted(seq)`|返回已排序的包含seq所有元素的列表|
|`tuple(seq)`|把序列转换为元组|

## 第三章 使用字符串

所有标准的序列操作（索引、分片、乘法、判断成员资格、求长度、取最小值和最大值）对字符串同样适用。

模版字符串

`substitute`这个模版方法会用传递进来的关键字参数`foo`替换掉字符串中的`$foo`。

```python
>>> from string import Template
>>> s = Template("$x, glorious $x!")
>>> s.substitute(x='slurm')
'slurm, glorious slurm!'
```

如果替换字段是单词的一部分，那么参数名就必须用括号扩起来，从而准确指名结尾：

```python
>>> s = Template("It's ${x}tastic!")
>>> s.substitute(x='slurm')
"It's slurmtastic!"
```

可以使用`$$`插入美元符

```python
>>> s = Template("Make $$ selling $x!")
>>> s.substitute(x='slurm')
'Make $ selling slurm!'
```

使用字典的方式进行字符串格式化

```python
>>> s = Template("A $thing must never $action.")
>>> d = {}
>>> d['thing'] = "gentleman"
>>> d['action'] = "show his socks"
>>> s.substitute(d)
'A gentleman must never show his socks.'
```

pi==3.1415926

```python
>>> from math import pi
>>> pi
3.141592653589793
```

字符串其他方法

```python
>>> import string
# 包含数字0-9的字符串
>>> string.digits
'0123456789'
# 包含所有字母（大写或小写）的字符串
>>> string.letters
'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'
# 包含所有小写字母的字符串
>>> string.lowercase
'abcdefghijklmnopqrstuvwxyz'
# 包含所有可打印字符的字符串
>>> string.printable
'0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~ \t\n\r\x0b\x0c'
# 包含所哟标点符号的字符串
>>> string.punctuation
'!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'
# 包含所有大写字母的字符串
>>> string.uppercase
'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
```

第三章所涉及到的新函数

|函数|描述|
|:--|:--|
|`string.capwords(s[, sep])`|使用`split`函数分割字符串`s（以sep为分隔符）`，使用`capitalize`函数将分割得到的各单词首字母大写，并且使用`join`函数以`sep`为分隔符将各单词连接起来|
|`string.maketrans(from, to)`|创建用于转换的转换表|

## 第四章 字典：当索引不好用时

以元组的方式创建字典

```python
>>> item = [('name','ansheng'),('age','20')]
>>> item
[('name', 'ansheng'), ('age', '20')]
>>> dict(item)
{'age': '20', 'name': 'ansheng'}
```

健可以是任意不可变类型

```python
>>> x = []
# 列表x并没有42这个索引
>>> x[42] = 'Foobar'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list assignment index out of range
```

```python
>>> x = {}
>>> x[42] = 'Foobar'
>>> x
{42: 'Foobar'}
```

字典的格式化字符串

```python
>>> phonebook = {"Beth":'9102','Alice':'2341','Cecil':'3258'}
>>> "Cecil's phone number is %(Cecil)s." % phonebook
"Cecil's phone number is 3258."
```
```python
>>> tempalte = '''<html>
... <head><title>%(title)s</title><head>
... <body>
... <h1>%(title)s</h1>
... <p>%(text)s</p>
... <body>'''
>>> data = {'title':'My Home Page','text':'Welcome to my home page!'}
>>> print tempalte % data
<html>
<head><title>My Home Page</title><head>
<body>
<h1>My Home Page</h1>
<p>Welcome to my home page!</p>
<body>
```

深浅拷贝

在副本中替换某个值的时候，原始字典不受影响，但是如果修改了某个值，原始字典也会改变。

```python
>>> x = {'username':'admin','machines':['foo','bar','baz']}
>>> y = x.copy()
>>> y['username'] = 'mlh'
>>> y['machines'].remove('bar')
>>> y
{'username': 'mlh', 'machines': ['foo', 'baz']}
>>> x
{'username': 'admin', 'machines': ['foo', 'baz']}
```

避免这个问题可以使用深拷贝，复制其包含所有的值

```python
>>> from copy import deepcopy
>>> d ={}
>>> d['names'] = ['Alfred','Bertand']
>>> c = d.copy()
>>> dc = deepcopy(d)
>>> d['names'].append('Clive')
>>> c
{'names': ['Alfred', 'Bertand', 'Clive']}
>>> dc
{'names': ['Alfred', 'Bertand']}
```

第四章所涉及到的新函数

|函数|描述|
|:--|:--|
|`dict(seq)`|用(键，值)对(或者映射和关键字参数)建立字典|

## 第五章 条件、循环和其他语句

一个有趣的`Hello，World！`

在脚本中执行添加下面的代码并执行，会有意想不到的结果：

```python
#!/use/bin/env python
# _*_ coding:utf-8 __
# 如果在结尾加上一个逗号，那么接下来的语句会和前一条语句一起打印
print 'Hello，',
print 'World!'
# Hello， World!
```

序列解包/递归解包

```python
>>> values = 1,2,3
>>> values
(1, 2, 3)
>>> x,y,z=values
>>> x
1
>>> y
2
>>> z
3
```
例如获取字典中任意的健值对，可以使用`popitem`方法，这个方法将健值作为元组返回
```python
>>> scoundrel = {'name':'Robin','girlfriend':'Marion'}
>>> key,value = scoundrel.popitem()
>>> key
'girlfriend'
>>> value
'Marion'
```

链式赋值

```python
x = y = z()
```

增量赋值

```python
x += 1
```

同一性运算符（`is`）

判断同一性而不是相等性

```python
>>> x = [1,2,3]
>>> y = [2,4]
>>> x is not y
True
>>> del x[2]
>>> y[1] = 1
>>> y.reverse()
>>> x
[1, 2]
>>> y
[1, 2]
>>> x == y
True
>>> x is y
False
```

使用`==`运算符来判断两个对象是否相等，使用`is`判断两者是否等同（同一个对象）

断言

```python
>>> assert 1 < 5 < 10
# 如果条件不成立则直接报错
>>> assert 1 < 5 < 3
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AssertionError
```

并行迭代

```python
>>> names = ['anne','beth','george','damon']
>>> ages = [12,45,32,102]
>>> for i in zip(names,ages):
...   print i
... 
('anne', 12)
('beth', 45)
('george', 32)
('damon', 102)
```

`zip`

```python
>>> zip(names,ages)
[('anne', 12), ('beth', 45), ('george', 32), ('damon', 102)]
>>> dict(zip(names,ages))
{'damon': 102, 'anne': 12, 'beth': 45, 'george': 32}
```

列表推到式-轻量级循环

```python
>>> [x for x in range(10)]
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> [x*x for x in range(10)]
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

```python
>>> [x*x for x in range(10) if x % 3 == 0]
[0, 9, 36, 81]
>>> [x for x in range(10) if x % 3 == 0]
[0, 3, 6, 9]
```

```python
>>> [(x,y) for x in range(3) for y in range(3)]
[(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]
```

`del`

`del`只会把变量名删除，而不会删除所对应的值，实际上在Python中是无法删除值的，因为在内部有垃圾回收机制，当这个值没有被引用的时候，这个值就会被自动删除。

```python
>>> x = [1,2,3]
>>> y = x
>>> del x
>>> y
[1, 2, 3]
```

第五章所涉及到的函数

|函数|描述|
|:--|:--|
|`chr(n)`|当传入序号`n`时，返回`n`所代表的包含一个字符的字符串|
|`eval(source[,globals[,locals]])`|将字符串作为表达式计算，并且返回值|
|`enumerate(seq)`|产生用于迭代的(索引，值)对|
|`ord(c)`|返回单字符字符串的int值|
|`range([start,] stop[, step])`|创建整数的列表|
|`reversed(seq)`|产生`seq`中值的反向版本，用于迭代|
|`sorted(seq[,cmp][,key][,reverse])`|返回`seq`中值排序后的列表|
|`xrange([start,]stop[,step])`|创造`xrange`对象用于迭代|
|`zip(seq1,_seq2...)`|创造用于并行迭代的新序列|

## 第六章 抽象

给函数添加帮助文档

```python
>>> def hello():
...  'Hello World!'
...  return 'Hi'
... 
>>> hello.__doc__
'Hello World!'
```

嵌套作用域

```python
>>> def multiplier(factor):
...   def multiplyByFactor(number):
...       return number*factor
...   return multiplyByFactor
... 
>>> double = multiplier(2)
>>> double(5)
10
```

第六章遇到的新函数

|函数|描述|
|:--|:--|
|`map(func, seq[,seq,...])`|对序列中的每个元素应用函数|
|`filter(func, seq)`|返回其函数为真的元素的列表|
|`reduce(func,seq[,initial])`|等同于`func(func(func(seq[0],seq[1],seq[2],.....)))`|
|`sun(seq)`|返回`seq`中所有元素的和|
|`apply(func[, args[,kwargs]])`|调用函数，可以提供参数|

## 第七章 更加抽象

私有方法

```python
In [1]: class Secretive(object):
   ...:     def __inaccessible(self):
   ...:         print "Bet you can's see me..."
   ...:     def accessible(self):
   ...:         print "The secret message is:"
   ...:         self.__inaccessible()
   ...:         

In [2]: s = Secretive()

In [3]: s.__inaccessible()
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-3-13232f401269> in <module>()
----> 1 s.__inaccessible()

AttributeError: 'Secretive' object has no attribute '__inaccessible'

In [4]: s.accessible()
The secret message is:
Bet you can's see me...
```
```python
# 类的内部定义中，所有以双下划线开始的名字都被翻译成前面加上单下划线和类名的形式
In [5]: Secretive._Secretive__inaccessible
Out[5]: <unbound method Secretive.__inaccessible>
```
```python
# 然而，依旧可以通过单下划线加上加上类名再加上私有方法名称，依旧可以被调用
In [7]: s._Secretive__inaccessible()
Bet you can's see me...
```

单下划线

类中带`_(但下划线)`的方法将不会被`import *`所倒入

```python
from module import *
```

类中共有的成员

```python
In [10]: class MemberCounter(object):
    ...:     menbers = 0
    ...:     def init(self):
    ...:         MemberCounter.menbers += 1
    ...:         

In [11]: m1 = MemberCounter()

In [12]: m1.init()

In [13]: m2 = MemberCounter()

In [14]: m2.init()

In [15]: m2.menbers
Out[15]: 2

# 重新绑定menbers的特性
In [16]: m1.menbers
Out[16]: 2

In [17]: m1.menbers = 'Tow'

In [18]: m1.menbers
Out[18]: 'Tow'

In [19]: m2.menbers
Out[19]: 2
```

指定超类

```python
In [20]: class Filter(object):
    ...:     def init(self):
    ...:         self.blocked = []
    ...:     def filter(self, sequence):
    ...:         return [x for x in sequence if x not in self.blocked]
    ...:     

In [21]: class SPAMFilter(Filter):
    ...:     def init(self):
    ...:         self.blocked = ['SPAM']
    ...:         

In [22]: f = Filter()

In [23]: f.init()

In [24]: f.filter([1,2,3])
Out[24]: [1, 2, 3]

In [25]: s = SPAMFilter()

In [26]: s.init()

In [27]: s.filter(['SPAM','SPAM','SPAM','SPAM','eggs','bacon','SPAM'])
Out[27]: ['eggs', 'bacon']
```

**两个重点：**

1. 在`SPAMFilter`类中重写了`init`方法；
2. `SPAMFilter`类中的`filter`方法是从`Filter`类中继承过来的，所以不需要再次定义它；

检查继承

使用`issubclass()`函数查看一个类是否是另一个的子类

```python
In [29]: issubclass(SPAMFilter, Filter)
Out[29]: True

In [30]: issubclass(Filter, SPAMFilter)
Out[30]: False
```

使用特性__bases__查看已知类的基类

```python
In [31]: SPAMFilter.__bases__
Out[31]: (__main__.Filter,)
```

使用`isinstance()`检查一个对象是否是一个类的实例

```python
In [33]: s = SPAMFilter()

In [34]: isinstance(s, SPAMFilter)
Out[34]: True

# 因为SPAMFilter有继承Filter
In [35]: isinstance(s, Filter)
Out[35]: True

In [36]: isinstance(s, str)
Out[36]: False
```
使用`__class__`特性或者`type()`直接查看当前实例属于那个类
```python
In [37]: s.__class__
Out[37]: __main__.SPAMFilter

In [38]: type(s)
Out[38]: __main__.SPAMFilter
```

本章的新函数

|函数|描述|
|:--|:--|
|`callable(object)`|确定对象是否可调用(比如函数或者方法)|
|`getattr(object, name[, default])`|确定特性的值，可选择提供默认值|
|`hasattr(object, name)`|确定对象是否有个定的特性|
|`isinstance(object, class)`|确定对象是否有类的实例|
|`issubclass(A, B)`|确定A是否为B的子类|
|`random.choice(sequence)`|从非空序列中随机选择元素|
|`setattr(object, name, value)`|设定对象的给定特性为`value`|
|`type(object)`|返回对象的类型|

## 第八章 异常

raise语句

```python
# 使用内建的Exception异常
In [39]: raise Exception
---------------------------------------------------------------------------
Exception                                 Traceback (most recent call last)
<ipython-input-39-fca2ab0ca76b> in <module>()
----> 1 raise Exception

Exception: 

In [40]: raise Exception('Error')
---------------------------------------------------------------------------
Exception                                 Traceback (most recent call last)
<ipython-input-40-24314b2fb2fb> in <module>()
----> 1 raise Exception('Error')

Exception: Error
```

查看内置的异常

```python
In [41]: import exceptions
# 下面所有的异常都可以用在raise中
In [42]: dir(exceptions)
Out[42]: 
['ArithmeticError',
 'AssertionError',
 'AttributeError',
 'BaseException',
 'BufferError',
 'BytesWarning',
 'DeprecationWarning',
 'EOFError',
 'EnvironmentError',
 'Exception',
 'FloatingPointError',
 'FutureWarning',
 'GeneratorExit',
 'IOError',
 'ImportError',
 'ImportWarning',
 'IndentationError',
 'IndexError',
 'KeyError',
 'KeyboardInterrupt',
 'LookupError',
 'MemoryError',
 'NameError',
 'NotImplementedError',
 'OSError',
 'OverflowError',
 'PendingDeprecationWarning',
 'ReferenceError',
 'RuntimeError',
 'RuntimeWarning',
 'StandardError',
 'StopIteration',
 'SyntaxError',
 'SyntaxWarning',
 'SystemError',
 'SystemExit',
 'TabError',
 'TypeError',
 'UnboundLocalError',
 'UnicodeDecodeError',
 'UnicodeEncodeError',
 'UnicodeError',
 'UnicodeTranslateError',
 'UnicodeWarning',
 'UserWarning',
 'ValueError',
 'Warning',
 'ZeroDivisionError',
 ......
```

用一个块捕获两个异常

```python
except (ZeroDivisionError, TypeError, NameError) as e:
```

捕获所有的异常

```python
except Exception as e:
```

本章的新函数

|函数|描述|
|:--|:--|
|`warnings.filterwarnings(action...)`|用于过滤警告|

## 第九章 魔法方法、属性和迭代器

使用super函数

```python
In [2]: class Bird(object):
   ...:     def __init__(self):
   ...:         self.hungry = True
   ...:     def eat(self):
   ...:         if self.hungry:
   ...:             print "Aaaah..."
   ...:             self.hungry = False
   ...:         else:
   ...:             print "No, thanks!"
   ...:         
In [4]: class SonBrid(Bird):
   ...:     def __init__(self):
   ...:         super(SonBrid, self).__init__()
   ...:         self.sound = "Squawk!"
   ...:     def sing(self):
   ...:         print self.sound
   ...:    
In [5]: sb = SonBrid()

In [6]: sb.sing()
Squawk!

In [7]: sb.eat()
Aaaah...

In [8]: sb.eat()
No, thanks!
```

property函数

```python
In [16]: class Rectangle(object):
    ...:     def __init__(self):
    ...:         self.width = 0
    ...:         self.height = 0
    ...:     def setSize(self, size):
    ...:         self.width, self.height = size
    ...:     def getSize(self):
    ...:         return self.width, self.height
    ...:     size = property(getSize, setSize)
    ...:     

In [17]: r = Rectangle()

In [18]: r.width = 10

In [19]: r.height = 5

In [20]: r.size
Out[20]: (10, 5)

In [21]: r.size = 100, 50

In [22]: r.size
Out[22]: (100, 50)
```

静态方法和类成员方法

```python
In [23]: class MyClass(object):
    ...:     @staticmethod
    ...:     def smeth():
    ...:         print 'This is a static method.'
    ...:     @classmethod
    ...:     def cmeth(cls):
    ...:         print 'This is a class method of', cls
    ...:         

In [24]: MyClass.smeth()
This is a static method.

In [25]: MyClass.cmeth()
This is a class method of <class '__main__.MyClass'>
```

`__getattr__`、`__setattr__`和他的朋友们

|属性|描述|
|:--|:--|
|`__getattribute__(self, item)`|当特性被访问时自动调用（只能在新示类中使用）|
|`__getattr__(self, item)`|当特性item被访问且没有相应的特性时自动调用|
|`__setattr__(self, key, value)`|当试图给特性item复制时被自动调用|
|`__delattr__(self, item)`|当试图删除item属性时被调用|

```python
#!/use/bin/env python
# _*_ coding:utf-8 __

class MyClass(object):
    def __init__(self):
        self.width = 0
        self.heigth = 0

    def __getattr__(self, item):
        if item == "size":
            return self.width, self.heigth
        else:
            raise AttributeError

    def __setattr__(self, key, value):
        if key == "size":
            self.width, self.heigth = value
        else:
            self.__dict__[key] = value


c = MyClass()
print c.size  # (0, 0)
c.heigth = 50
c.width = 100
c.size = 500, 1000
print c.size  # (500, 1000)
```

一个简单的迭代器

```python
#!/use/bin/env python
# _*_ coding:utf-8 __

class Fibs(object):
    def __init__(self):
        self.a = 0
        self.b = 1

    def next(self):
        self.a, self.b = self.b, self.a + self.b
        return self.a

    def __iter__(self):
        return self


fibs = Fibs()

for f in fibs:
    if f > 100:
        print f
        break
# 144
```

一个简单的生成器

```python
#!/use/bin/env python
# _*_ coding:utf-8 __

netsted = [[1, 2, ], [3, 4], [5]]


def flatten(netsted):
    for sublist in netsted:
        for element in sublist:
            yield element


for num in flatten(netsted=netsted):
    print num

print list(flatten(netsted=netsted))
```
```bash
1
2
3
4
5
[1, 2, 3, 4, 5]
```

生成器推到式

```python
In [37]: g = ((i+2)**2 for i in range(1,100))

In [38]: g
Out[38]: <generator object <genexpr> at 0x10405d640>

In [39]: g.next()
Out[39]: 9
```

生成器中的send

```python
In [43]: def repeater(value):
    ...:     while True:
    ...:         new = (yield value)
    ...:         if new is not None: value = new
    ...:         

In [44]: r = repeater(42)
# 执行到yield停止
In [45]: r.next()
Out[45]: 42
# 从yield开始执行，并接受一个参数赋值给new
In [46]: r.send('Hello World!')
Out[46]: 'Hello World!'
```

本章的新函数

|函数|描述|
|`iter(obj)`|从一个可迭代的对象得到迭代器|
|`property(fget, fset,fdel, doc)`|返回一个属性，所有的参数都是可选的|
|`super(class, obj)`|返回一个类的超类的绑定实例|

## 第十章 自带电池

模块重载函数reload

只在`Python2`中生效，`python3`已经移除了，可以使用`exec`替代

```python
In [2]: import sys

In [3]: sys.path.append('/Users/ansheng/PycharmProjects/《Python基础编程：第2版.
   ...: 修订版》/第十章 模块')

In [4]: import hello
Hello World!

# 因为模块倒入并不意味着在倒入时执行某些操作，它们主要用于定义，比如变量、函数和类等。
In [5]: import hello

In [6]: 

In [6]: reload(hello)
Hello World!
Out[6]: <module 'hello' from '/Users/ansheng/PycharmProjects/《Python基础编程：第2版.修订版》/第十章 模块/hello.pyc'>

In [7]: reload(hello)
Hello World!
Out[7]: <module 'hello' from '/Users/ansheng/PycharmProjects/《Python基础编程：第2版.修订版》/第十章 模块/hello.pyc'>
```

查看模块加载路径

```python
In [1]: import sys, pprint

In [2]: pprint.pprint(sys.path)
......
```

探究模块中的方法

```python
In [10]: import copy

# 这是一个列表推倒式
In [11]: [n for n in dir(copy) if not n.startswith('_')]
Out[11]: 
['Error',
 'PyStringMap',
 'copy',
 'deepcopy',
 'dispatch_table',
 'error',
 'name',
 't',
 'weakref']
```

`__all__`

```python
# hello5.py
#!/use/bin/env python
# _*_ coding:utf-8 __


__all__ = ['Hello']


class Bash(object):
    def hello(self):
        print 'Hello, World!'


class Hello(object):
    def __init__(self):
        self.name = 'ansheng'

    def SetName(self):
        self.name = 'As'

    def DelName(self):
        self.name = None
```

```python
In [5]: import hello5

In [6]: [x for x in dir(hello5) if not x.startswith('_')]
Out[6]: ['Bash', 'Hello']
```

```python
# 使用 import *的方式倒入的时候只会加载模块中__all__中的所有方法／类
In [3]: from hello5 import *

In [4]: [x for x in dir() if not x.startswith('_')]
Out[4]: ['Hello', 'In', 'Out', 'exit', 'get_ipython', 'quit', 'sys']
```

本章涉及到的新函数

|函数|描述|
|:--|:--|
|`dir(obj)`|返回按字母排序的属性名称列表|
|`help([obj])`|提供交互式帮助或关于特定对象的交互式帮助信息|
|`reload(module)`|返回已经倒入模块的重新载入版本，该函数在Python3.0将要被废弃|

## 第十一章文件和流

本章的新函数

|函数|描述|
|:--|:--|
|`file([name[, mode[, buffering]]])`|打开一个文件并返回一个文件对象|
|`open([name[, mode[, buffering]]])`|file的别名；在打开文件时，使用open而不是file|