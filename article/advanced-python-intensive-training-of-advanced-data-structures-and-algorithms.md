# Python进阶强化训练之数据结构与算法进阶

## 如何在列表、字典、集合中根据条件筛选数据？

**实际问题**

1. 过滤列表中的负数
2. 筛选出字典种值高于90的项
3. 筛选出集合种能被3整出的元素

围绕上面三个问题我们来进行讨论，比如下面有一个列表：

```python
>>> from random import randint
>>> li = [randint(-10, 10) for _ in range(10)]
>>> li
[-10, -9, 1, 10, -3, -7, -6, -7, 4, -5]
```
我们常规的做法就是通过`for`循环对列表中的每一个值进行迭代，然后判断如果值大于等于0，就确定这个值是一个整数，否则就丢弃，比如下面的代码：
```python
>>> result = []
>>> for n in li:
# 如果这个元素大于等于0
...   if n >= 0:
# 加入的result列表中
...     result.append(n)
...
>>> result
[1, 10, 4]
```

## 实例

本篇所有的代码均在`Python 3.5.x`种运行，如果你使用的是`python 2.7.x`，那么请自行测试，在此之前，请导入一下模块用于测试：

```python
# 用于生成随机数
>>> from random import randint
# 准确测量小段代码的执行时间
>>> import timeit
```

请仔细阅读下面的代码，看完后你将会有不一样的收获。

### 列表

filter函数

生成一个随机列表

```python
>>> li = [randint(-10, 10) for _ in range(10)]
>>> li
[6, -8, 9, 3, 3, 8, 9, -4, 9, -6]
```

```python
# x=列表中的一个元素，有多少个元素就迭代多少次
>>> result = filter(lambda x: x >=0, li)
>>> for n in result:
...  print(n)
...
6
9
3
3
8
9
9
```

列表解析

生成一个随机列表

```python
>>> li = [randint(-10, 10) for _ in range(10)]
>>> li
[8, -5, -2, 8, 9, 4, -6, -5, 5, 4]
```

```python
>>> [x for x in li if x >=0 ]
[8, 8, 9, 4, 5, 4]
```

filter与列表解析性能对比

使用`filter`执行时间

```python
>>> timeit.Timer('filter(lambda x: x >=0, [4, -1, 1, 3, -10, 5, -8, 0, 6, 3])').timeit()
0.38938787838583266
```

使用`列表解析`执行时间

```python
>>> timeit.Timer('[x for x in [4, -1, 1, 3, -10, 5, -8, 0, 6, 3] if x >=0 ]').timeit()
1.1142896312373978
```

通过以上的测试可以看出`filter`的执行时间明显比`列表解析`要快些，当然这并不是一个非常准确的数字，还是有待考察的。

### 字典

先随机生成一个字典：

```python
>>> dic = { x: randint(60, 100) for x in range(1, 21) }
>>> dic
{1: 61, 2: 75, 3: 69, 4: 70, 5: 79, 6: 90, 7: 74, 8: 85, 9: 77, 10: 86, 11: 93, 12: 96, 13: 86, 14: 79, 15: 60, 16: 84, 17: 70, 18: 72, 19: 61, 20: 87}
```

字典解析

```python
>>> { k: v for k, v in dic.items() if v > 90 }
{11: 93, 12: 96}
```

### 集合

生成一个集合：

```python
>>> li = [randint(-10, 10) for _ in range(10)]
>>> s = set(li)
>>> s
{0, 1, 3, 4, 7, -9, -8}
```

集合解析

```python
>>> { x for x in s if x % 3 == 0 }
{0, 3, -9}
```

## 如何为元组中的每个元素命名、提高程序可读性？

**实际问题**

某校的学生信息系统中的数据存储格式如下：
```python
(名字,年龄,性别,邮箱地址)
```
比如有如下学生信息:
```python
student1 = ('Hello', 15, 'Schoolboy', 'hello@gmail.com')
student2 = ('World', 16, 'Girls', 'World@gmail.com')
student3 = ('ansheng', 20, 'Schoolboy', 'anshengme.com@gmail.com')
.....
```
通常我们会以如下方式进行取值：

```python
>>> student1[2]
'Schoolboy'
>>> student1[3]
'hello@gmail.com'
```

在代码比较多的情况下，使用大量的索引进行访问会降低程序的可读性，如何解决这个问题呢？

### 方案1

定义类似于其他语言的枚举类型，也就是定义一系列的数值常量.

```python
# 创建一个学生
>>> student = ('ansheng', 20, 'Schoolboy', 'anshengme.com@gmail.com')
# 定义常量
>>> NAME, AGE, SEX, EMAIL = range(4)
# 通过常量进行取值
>>> student[NAME]
'ansheng'
>>> student[AGE]
20
>>> student[EMAIL]
'anshengme.com@gmail.com'
```

### 方案2

使用标准库中的`collections.namedtuple`替代内置的`tuple`

```python
>>> from collections import namedtuple
>>> Student = namedtuple('Student', ['name','age','sex','email'])
>>> s = Student('ansheng', 20, 'Schoolboy', 'anshengme.com@gmail.com')
>>> s
Student(name='ansheng', age=20, sex='Schoolboy', email='anshengme.com@gmail.com')
# 使用属性进行访问
>>> s.name
'ansheng'
>>> s.age
20
>>> s.sex
'Schoolboy'
```
`s`是`tuple`的一个子类
```python
>>> isinstance(s, tuple)
True
```

## 如何统计序列中元素的出现频度？

**实际问题**

1. 某随机的列表中，找出出现次数最高的三个元素，他们的出现次数是多少？
2. 对某英文文章的单词进行词频统计，找出出现次数最高的10个单词，他们出现的次数是多少？

**解决方案**

使用`collections.Counter`对象，将序列传入`Counter`的构造器，得到`Counter`对象是元素频度的字典，`Counter.most_common(n)`方法得到频度最高的`n`个元素的列表

### 实例

常规的解决方法

生成随机序列的列表

```python
>>> from random import randint
>>> li = [randint(0, 20) for _ in range(30)]
>>> li
[14, 11, 10, 13, 19, 10, 3, 17, 12, 13, 18, 5, 10, 1, 9, 17, 1, 8, 3, 15, 8, 3, 20, 10, 9, 20, 6, 13, 8, 20]
```
以字典的形式创建每个数字出现的次数，
```python
# 默认的出现次数为0
>>> count = dict.fromkeys(li, 0)
>>> count
{1: 0, 3: 0, 5: 0, 6: 0, 8: 0, 9: 0, 10: 0, 11: 0, 12: 0, 13: 0, 14: 0, 15: 0, 17: 0, 18: 0, 19: 0, 20: 0}
```
每遇到一个`x(列表中的数)`，就去字典中让值`+1`
```python
>>> for x in li:
...  count[x] += 1
...
>>> count
{1: 2, 3: 3, 5: 1, 6: 1, 8: 3, 9: 2, 10: 4, 11: 1, 12: 1, 13: 3, 14: 1, 15: 1, 17: 2, 18: 1, 19: 1, 20: 3}
```

然后循环`count`找到最大的三个数字，取出来就好。

使用`collections.Counter`对象

```python
# 导入Counter
>>> from collections import Counter
```

```python
>>> count = Counter(li)
>>> count
Counter({10: 4, 3: 3, 8: 3, 13: 3, 20: 3, 1: 2, 9: 2, 17: 2, 5: 1, 6: 1, 11: 1, 12: 1, 14: 1, 15: 1, 18: 1, 19: 1})
>>> count.most_common(3)
[(10, 4), (3, 3), (8, 3)]
```

英文文章词频统计实例

```python
>>> from collections import Counter
>>> import re
>>> txt = open('jquery.cookie.js').read()
>>> count = Counter(re.split('\W+', txt))
>>> count
Counter({'s': 20, 'cookie': 18, 'options': 16, 'value': 12, 'function': 11, 'key': 10, 'var': 10, 'return': 9, 'expires': 8, 'if': 8, 'config': 8, 't': 6, 'result': 5, 'the': 5, 'a': 5, 'it': 5, '1': 4, 'decode': 4, 'i': 4, 'converter': 4, 'factory': 4, 'undefined': 4, 'cookies': 4, 'read': 3, 'domain': 3, 'g': 3, 'encode': 3, 'raw': 3, 'path': 3, 'name': 3, 'replace': 3, 'typeof': 3, 'parts': 3, 'we': 3, 'define': 3, 'document': 3, 'is': 3, 'pluses': 3, 'jquery': 3, 'If': 3, '': 2, 'else': 2, 'for': 2, 'object': 2, 'can': 2, 'json': 2, 'join': 2, 'days': 2, 'not': 2, 'jQuery': 2, 'in': 2, 'l': 2, 'parse': 2, 'ignore': 2, 'split': 2, 'isFunction': 2, 'unusable': 2, 'stringifyCookieValue': 2, 'secure': 2, 'extend': 2, 'decodeURIComponent': 2, 'parseCookieValue': 2, 'JSON': 2, '0': 2, 'defaults': 2, 'extending': 1, 'storing': 1, 'Copyright': 1, 'thus': 1, 'use': 1, 'place': 1, 'require': 1, 'max': 1, 'length': 1, 'according': 1, 'AMD': 1, 'carhartl': 1, 'first': 1, 'globals': 1, 'catch': 1, 'https': 1, 'are': 1, 'try': 1, 'Write': 1, 'spaces': 1, 'written': 1, 'array': 1, 'age': 1, 'supported': 1, 'attribute': 1, 'under': 1, 'exports': 1, 'that': 1, 'Klaus': 1, 'RFC2068': 1, 'Replace': 1, 'as': 1, 'shift': 1, 'Prevent': 1, '864e': 1, 'slice': 1, 'prevents': 1, 'stringify': 1, 'loop': 1, 'e': 1, 'at': 1, 'v1': 1, '5': 1, 'com': 1, 'when': 1, 'toUTCString': 1, 'setTime': 1, 'CommonJS': 1, 'false': 1, 'amd': 1, 'Plugin': 1, 'quoted': 1, 'couldn': 1, 'an': 1, 'no': 1, 'github': 1, 'argument': 1, 'Must': 1, 'license': 1, 'second': 1, 'break': 1, 'Browser': 1, 'IE': 1, 'Date': 1, 'to': 1, 'prevent': 1, 'To': 1, 'Released': 1, 'by': 1, 'with': 1, 'Cookie': 1, 'Also': 1, 'indexOf': 1, 'there': 1, 'side': 1, 'MIT': 1, 'odd': 1, 'case': 1, 'number': 1, 'encodeURIComponent': 1, 'calling': 1, 'Hartl': 1, 'unescape': 1, 'all': 1, 'removeCookie': 1, 'Read': 1, 'new': 1, 'assign': 1, 'fresh': 1, 'server': 1, '2013': 1, 'String': 1, 'empty': 1, '4': 1, 'This': 1, 'alter': 1})
>>> count.most_common(10)
[('s', 20), ('cookie', 18), ('options', 16), ('value', 12), ('function', 11), ('key', 10), ('var', 10), ('return', 9), ('expires', 8), ('if', 8)]
```

## 如何根据字典中值的大小, 对字典中的项排序？

**实际问题**

某班英语成绩以字典形式进行存储，格式为：

```python
{
   'ansheng': 79,
   'Jim': 66,
   'Hello': 99,
   ...
}
```

要求根据成绩高低，计算学生排名。

**解决方案**

使用内置函数`sorted()`，但是默认情况下`sorted()`并不能对字典进行排序，这里提供了两种解决方法：


1. 利用`zip()`将字典数据转化为元组然后把值传给`sorted()`进行排序
2. 传递`sorted()`函数的key参数

### 实例

先创建一个成绩单：

```python
>>> from random import randint
>>> Transcripts = { x: randint(60,100) for x in 'xyzabc' }
>>> Transcripts
{'z': 61, 'x': 74, 'b': 81, 'c': 65, 'y': 88, 'a': 98}
```
使用`sorted()`进行排序的时候是以字典的`key`进行的
```python
>>> sorted(Transcripts)
['a', 'b', 'c', 'x', 'y', 'z']
```

第一种解决方法

获取字典的所有建
```python
>>> Transcripts.keys()
dict_keys(['z', 'x', 'b', 'c', 'y', 'a'])
```
获取字典所有的值
```python
>>> Transcripts.values()
dict_values([61, 74, 81, 65, 88, 98])
```
通过`zip()`把字典转换为元组
```python
>>> T = zip(Transcripts.values(), Transcripts.keys())
```
通过`sorted()`进行排序得到结果
```python
>>> sorted(T)
[(61, 'z'), (65, 'c'), (74, 'x'), (81, 'b'), (88, 'y'), (98, 'a')]
```

元组在进行比较的时候是先从第一个元素进行比较，如果比较值为`True`，则后面的就不进行比较：
```python
>>> (100, 'a') > (50, 'b')
True
>>> (50, 'a') > (50, 'b')
# a不大于b，返回False
False
```

第二种解决方法

```python
>>> Transcripts.items()
dict_items([('z', 61), ('x', 74), ('b', 81), ('c', 65), ('y', 88), ('a', 98)])
# key需要传入一个函数，每次迭代Transcripts.items()的时候，把第一个元素传入进去，然后进行排序
>>> sorted(Transcripts.items(), key=lambda x: x[1])
[('z', 61), ('c', 65), ('x', 74), ('b', 81), ('y', 88), ('a', 98)]
```

## 如何快速找到多个字典中的公共键(key)？

在每一个字典中都会出现的键称之为`公共键`。

```python
>>> from random import randint, sample
# abcdefg是随机产生的key，每次随机取3-6个key
>>> sample('abcdefg', randint(3, 6))
['g', 'f', 'c', 'a', 'e', 'd']
```

生成随机的字典

```python
>>> s1 = { x: randint(1, 4) for x in sample('abcdefg', randint(3, 6)) }
>>> s2 = { x: randint(1, 4) for x in sample('abcdefg', randint(3, 6)) }
>>> s3 = { x: randint(1, 4) for x in sample('abcdefg', randint(3, 6)) }
>>> s1
{'c': 3, 'g': 1, 'e': 2}
>>> s2
{'a': 2, 'f': 2, 'g': 3, 'e': 1, 'd': 2}
>>> s3
{'a': 2, 'c': 2, 'e': 2, 'f': 4}
```
**解决方案**

传统的做法如下:

```python
# 生成一个列表
>>> res = []
# 循环s1字典的所有键
>>> for k in s1:
# 如果键在s2和s3中都存在就添加到res列表中
...   if k in s2 and k in s3:
...     res.append(k)
...
>>> res
# 得到的键e，在s2和s3中都存在
['e']
```

利用集合(set)的交集操作

使用字典的`keys()`方法，得到一个字典的`keys`的集合

```pyhton
>>> s1.keys() & s2.keys() & s3.keys()
{'e'}
```

1. 使用`map`函数，得到所有字典的`keys`的集合，然后使用`reduce`函数，取所有字典的`keys`的集合的交集

```python
>>> from functools import reduce
>>> reduce(lambda a, b: a & b, map(dict.keys, [s1, s2, s3]))
{'e'}
```

## 如何让字典保持有序？



**解决方案**

使用`collections.OrderedDict`，以`OrderedDict`替代内置字典`Dict`，依次将数据存入`OrderedDict`

```python
# 导入OrderedDict模块
>>> from collections import OrderedDict
```

```python
# 创建一个OrderedDict()对象
>>> dic = OrderedDict()
# 添加数据
>>> dic['H1'] = ('a', 'b')
>>> dic['H2'] = ('ab', 'cd')
>>> dic['H3'] = ('ww', 'ss')
# 变量字典中的数据
>>> for n in dic: print(n)
...
H1
H2
H3
>>> for n in dic: print(n)
...
H1
H2
H3
```

## 如何实现用户的历史记录功能(最多n条)？

**解决方案**

可以使用容量为`n`的队列存储历史纪录，使用标准库`collections`中的`deque`，它是一个双端循环队列。

如果要保存到文件中，可以在程序堆出前，可以使用`pickle`将队列对象存入文件，再次运行程序时将其导入。

小例子

制作一个简单的猜数字小游戏，添加历史纪录的功能，显示用户最近猜过的数字，限定最近5条。

```python
# 导入deque模块
>>> from collections import deque
# 创建一个队列，容量初始值为空，最多放5个值
>>> q = deque([], 5)
>>> q
deque([], maxlen=5)
>>> q.append(1)
>>> q.append(2)
>>> q.append(3)
>>> q.append(4)
>>> q.append(5)
>>> q
deque([1, 2, 3, 4, 5], maxlen=5)
# 当超过5个值的时候，第一个就会被挤出去
>>> q.append(6)
>>> q
deque([2, 3, 4, 5, 6], maxlen=5)
```

实例的脚本文件为：

```python
from random import randint
from collections import deque

# 随机生成1-100的数字
N = randint(1, 100)
# 创建一个队列histort，默认为空，最大限度值为5个
histort = deque([], 5)


def guess(k):
    # 数字猜对
    if k == N:
        print('right')
        return True
    if k < N:
        print('%s is less-than N' % (k))
    else:
        print('%s is greater-than N' % (k))
    return False


while True:
    line = input('please input a number: ')
    if line.isdigit():
        k = int(line)
        histort.append(k)
        if guess(k):
            break
    # 如果输入'history'就输出队列中的内容
    elif line == 'history':
        print(list(histort))
```
演示截图
![deque-history](../images/2016/12/1483066399.gif)