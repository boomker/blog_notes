# Python进阶强化训练之字符串处理技巧

## 如何拆分含有多种分隔符的字符串？

实际案例

我们要把某个字符串依据分隔符号拆分不同的字符段，该字符串包含多种不同的分隔符，例如：

```text
s = 'asd;aad|dasd|dasd,sdasd|asd,,Adas|sdasd;Asdasd,d|asd'
```

其中`<,>,<;>,<|>,<\t>`都是分隔符，如何处理？

### 解决方案

 连续使用`split()`方法，每次处理一种分隔符

```python
# 使用Python2
def mySplit(s,ds):
    res = [s]
    for d in ds:
        t = []
        map(lambda x: t.extend(x.split(d)), res)
        res = t
    return [x for x in res if x]

s = 'asd;aad|dasd|dasd,sdasd|asd,,Adas|sdasd;Asdasd,d|asd'
result = mySplit(s, ';,|\t')
print(result)
```

```bash
C:\Users\Administrator>C:\Python\Python27\python.exe E:\python-intensive-training\s2.py
['asd', 'aad', 'dasd', 'dasd', 'sdasd', 'asd', 'Adas', 'sdasd', 'Asdasd', 'd', 'asd']
```

使用正则表达式的`re.split()`方法，一次性拆分字符串


```python
>>> import re
>>> re.split('[,;\t|]+','asd;aad|dasd|dasd,sdasd|asd,,Adas|sdasd;Asdasd,d|asd')
['asd', 'aad', 'dasd', 'dasd', 'sdasd', 'asd', 'Adas', 'sdasd', 'Asdasd', 'd', 'asd']
```

## 如何判断字符串a是否以字符串b开头或结尾？

实际案例

如某目录有如下文件：

```text
quicksort.c
graph.py
heap.java
install.sh
stack.cpp
......
```
现在需要给`.sh`和`.py`结尾的文件夹上可执行权限

### 解决方案

使用字符串的`startswith()`和`endswith()`方法

```bash
>>> import os, stat
>>> os.listdir('./')
['heap.java', 'quicksort.c', 'stack.cpp', 'install.sh', 'graph.py']
>>> [name for name in os.listdir('./') if name.endswith(('.sh','.py'))] 
['install.sh', 'graph.py']
>>> os.chmod('install.sh', os.stat('install.sh').st_mode | stat.S_IXUSR)
```

```bash
[root@iZ28i253je0Z t]# ls -l install.sh 
-rwxr--r-- 1 root root 0 Sep 15 18:13 install.sh
```

## 如何调整字符串中文本的格式？

实际案例

某软件的日志文件，其中日期格式为`yyy-mm-dd`:

```text
2016-09-15 18:27:26 statu unpacked python3-pip:all
2016-09-15 19:27:26 statu half-configured python3-pip:all
2016-09-15 20:27:26 statu installd python3-pip:all
2016-09-15 21:27:26 configure asdasdasdas:all python3-pip:all
```

需要把其中日期改为美国日期的格式`mm/dd/yyy`,`2016-09-15 --> 09/15/2016`,要如何处理？

### 解决方案

使用正则表达式`re.sub()`方法做字符串替换

利用正则表达式的捕获组，捕获每个部分内容，在替换字符串中各个捕获组的顺序。

```python
>>> log = '2016-09-15 18:27:26 statu unpacked python3-pip:all'
>>> import re
# 按顺序
>>> re.sub('(\d{4})-(\d{2})-(\d{2})', r'\2/\3/\1' , log)
'09/15/2016 18:27:26 statu unpacked python3-pip:all'
# 使用正则表达式的分组
>>> re.sub('(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})', r'\g<month>/\g<day>/\g<year>' , log)
'09/15/2016 18:27:26 statu unpacked python3-pip:all'
```

## 如何将多个小字符串拼接成一个大的字符串？

实际案例

在设计某网络程序时，我们自定义了一个基于`UDP`的网络协议，按照固定次序向服务器传递一系列参数：

```text
hwDetect:		"<0112>"
gxDepthBits:	"<32>"
gxResolution:	"<1024x768>"
gxRefresh:		"<60>"
fullAlpha:		"<1>"
lodDist:		"<100.0>"
DistCull:		"<500.0>"
```

在程序中我们将各个参数按次序收集到列表中：
```text
["<0112>","<32>","<1024x768>","<60>","<1>","<100.0>","<500.0>"]
```
最终我们要把各个参数拼接成一个数据包进行发送：
```text
"<0112><32><1024x768><60><1><100.0><500.0>"
```

### 结局方案

迭代列表，连续使用`'+'`操作依次拼接每一个字符串

```bash
>>> for n in ["<0112>","<32>","<1024x768>","<60>","<1>","<100.0>","<500.0>"]:
...  result += n
...
>>> result
'<0112><32><1024x768><60><1><100.0><500.0>'
```

使用`str.join()`方法，更加快速的拼接列表中所有字符串

```pythn
>>> result = ''.join(["<0112>","<32>","<1024x768>","<60>","<1>","<100.0>","<500.0>"])
>>> result
'<0112><32><1024x768><60><1><100.0><500.0>'
```

如果列表中有数字，可以使用生成器进行转换:

```python
>>> hello = [222,'sd',232,'2e',0.2]
>>> ''.join(str(x) for x in hello)
'222sd2322e0.2'
```

## 如何对字符串进行左, 右, 居中对齐？

实际案例

某个字典中存储了一系列属性值：

```python
{
  'ip':'127.0.0.1',
  'blog': 'www.ansheng.me',
  'title': 'Hello world',
  'port': '80'
}
```
在程序中，我们想以以下格式将其内容输出，如何处理？

```text
ip    : 127.0.0.1 
blog  : www.ansheng.me 
title : Hello world 
port  : 80 
```

### 解决方案

使用字符串的`str.ljust()`,`str.rjust`,`str.cente()`进行左右居中对齐

```python
>>> info = {'ip':'127.0.0.1','blog': 'www.ansheng.me','title': 'Hello world','port': '80'}
# 获取字典中的keys最大长度
>>> max(map(len, info.keys()))
5
>>> w = max(map(len, info.keys()))
>>> for k in info:
...   print(k.ljust(w), ':',info[k])
...
# 获取到的结果
port  : 80
blog  : www.ansheng.me
ip    : 127.0.0.1
title : Hello world
```

使用`format()`方法，传递类似`'<20'`,`'>20'`,`'^20'`参数完成同样任务

```python
>>> for k in info:
...   print(format(k,'^'+str(w)), ':',info[k])
...
port  : 80
blog  : www.ansheng.me
 ip   : 127.0.0.1
title : Hello world
```

## 如何去掉字符串中不需要的字符？

实际案例

1. 过滤掉用户输入卡后多余的空白字符: `  anshengm.com@gmail.com`
2. 过滤某windows下编辑文本中的'\r': `hello word\r\n`
3. 去掉文本中的unicode组合符号(音调): 'ní hǎo, chī fàn'

### 解决方案

字符串`strip()`,`lstrip()`,`rstrip()`方法去掉字符串两端字符

```python
>>> email = '  anshengm.com@gmail.com     '
>>> email.strip()
'anshengm.com@gmail.com'
>>> email.lstrip()
'anshengm.com@gmail.com     '
>>> email.rstrip()
'  anshengm.com@gmail.com'
>>>
```

删除某个固定位置的字符，可以使用切片+拼接的方法

```python
>>> s[:3] + s[4:]
'abc123'
```

字符串的`replace()`方法或正则表达式`re.sub()`删除任意位置字符

```python
>>> s = '\tabc\t123\txyz'
>>> s.replace('\t', '')
'abc123xyz'
```

使用`re.sub()`删除多个

```python
>>> import re
>>> re.sub('[\t\r]','', string)
'abc123xyzopq'
```

字符串`translate()`方法，可以同时删除多种不同字符

```python
>>> import string
>>> s = 'abc123xyz'
>>> s.translate(string.maketrans('abcxyz','xyzabc'))                    
'xyz123abc'
```

```python
>>> s = '\rasd\t23\bAds'
>>> s.translate(None, '\r\t\b')                                     
'asd23Ads'
```

```python
# python2.7
>>> i = u'ní hǎo, chī fàn'
>>> i
u'ni\u0301 ha\u030co, chi\u0304 fa\u0300n'
>>> i.translate(dict.fromkeys([0x0301, 0x030c, 0x0304, 0x0300]))
u'ni hao, chi fan'
```