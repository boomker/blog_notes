# Python进阶强化训练之csv|json|xml|excel高

## 如何读写csv数据？

实际案例

我们可以通过**http://table.finance.yahoo.com/table.csv?s=000001.sz**，这个url获取中国股市(深市)数据集，它以csv数据格式存储：

```text
Date,Open,High,Low,Close,Volume,Adj Close
2016-09-15,9.06,9.06,9.06,9.06,000,9.06
2016-09-14,9.17,9.18,9.05,9.06,42148100,9.06
2016-09-13,9.18,9.21,9.14,9.19,46093100,9.19
2016-09-12,9.29,9.32,9.13,9.16,75658100,9.16
2016-09-09,9.40,9.43,9.36,9.38,32743100,9.38
2016-09-08,9.39,9.42,9.38,9.40,29521800,9.40
2016-09-07,9.41,9.42,9.37,9.40,45937300,9.40
2016-09-06,9.42,9.43,9.36,9.41,57473800,9.41
2016-09-05,9.46,9.46,9.40,9.42,46993600,9.42
2016-09-02,9.43,9.46,9.42,9.45,36879600,9.45
2016-09-01,9.49,9.52,9.42,9.45,48013100,9.45
2016-08-31,9.46,9.50,9.43,9.49,48974600,9.49
2016-08-30,9.42,9.47,9.41,9.47,59508100,9.47
2016-08-29,9.41,9.45,9.38,9.42,56523100,9.42
2016-08-26,9.45,9.47,9.40,9.45,50223300,9.45
2016-08-25,9.42,9.45,9.34,9.44,61738900,9.44
2016-08-24,9.41,9.43,9.38,9.43,73228100,9.43
```

请将平安银行这支股票，在2016年中成交量超过`14000000`的记录存储到另一个csv文件中

### 解决方案

使用标准库中的csv模块，可以使用其中`reader`和`writer`完成文件读写

下载数据

```bash
>>> from urllib import urlretrieve
# 获取平安银行股票信息，保存到pingan.csv文件中
>>> urlretrieve('http://table.finance.yahoo.com/table.csv?s=000001.sz', 'pingan.csv')
('pingan.csv', <httplib.HTTPMessage instance at 0x1a997e8>)
```
使用csv模块进行读
```python
>>> import csv
>>> rf = open('pingan.csv', 'rb')
>>> reader = csv.reader(rf)
# 获取的对象是一个可迭代的
>>> reader.next()
['Date', 'Open', 'High', 'Low', 'Close', 'Volume', 'Adj Close']
>>> reader.next()
['2016-09-15', '9.06', '9.06', '9.06', '9.06', '000', '9.06']
```
使用csv模块进行写
```python
>>> wf = open('pingan_copy.csv', 'wb')
>>> writer = csv.writer(wf)
>>> writer.writerow(['2016-09-14', '9.17', '9.18', '9.05', '9.06', '42148100', '9.06'])
>>> writer.writerow(reader.next())                                                     
>>> wf.flush()
```
查看写入的文件内容
```bash
[root@iZ28i253je0Z ~]# cat pingan_copy.csv 
2016-09-14,9.17,9.18,9.05,9.06,42148100,9.06
2016-09-13,9.18,9.21,9.14,9.19,46093100,9.19
```
如上的问题解决方案如下：
```python
#!/use/bin/env python
# _*_ coding:utf-8 _*_

import csv

with open('pingan.csv', 'rb') as rf:
    reader = csv.reader(rf)
    with open('pingan2016.csv', 'wb') as wf:
        writer = csv.writer(wf)
        headers = reader.next()
        writer.writerow(headers)
        for row in reader:
            if row[0] < '2016-01-01':
                break
            if int(row[5]) >= 50000000:
                writer.writerow(row)
```

## 如何读写json数据？

实际案例

在web应用中常用JSON(JavaScript Object Notation)格式传输数据，在python中如何读写json数据?

### 解决方案

使用标准库中的json模块，其中`loads`、`dumps`函数可以完成json数据的读写

将数据类型转换为字符串
```python
>>> import json
# 创建一个列表
>>> l = [1,2,'asd',{'blgo_url','ansheng.me'}]
# 使用dumps转换为字符串
>>> json.dumps(l)
'[1, 2, "asd", {"blgo_url": "ansheng.me"}]'
# 去掉空格
>>> json.dumps(l, separators=[',',':'])
'[1,2,"asd",{"blgo_url":"ansheng.me"}]'
# 排序
>>> d = {'b':None,'a':'111','g':'Null'}
>>> json.dumps(d, sort_keys=True)
'{"a": "111", "b": null, "g": "Null"}'
```
将字符串转换为数据类型
```python
>>> json.loads('[1,2,"asd",{"blgo_url":"ansheng.me"}]')
[1, 2, 'asd', {'blgo_url': 'ansheng.me'}]
```

## 如何解析简单的xml文档？

实际案例

如以下`XML`文档，如何使用python进行解析？

```xml
<?xml version="1.0" encoding="utf-8" ?>
<data>
    <country name="Liechtenstein">
        <rank update="yes">2</rank>
        <year>2008</year>
        <gdppc>141100</gdppc>
        <neighbor name="Austria" direction="E"/>
        <neighbor name="Switzerland" direction="W"/>
    </country>
    <country name="Singapore">
        <rank update="yes">5</rank>
        <year>2011</year>
        <gdppc>59900</gdppc>
        <neighbor name="Malaysia" direction="N"/>
    </country>
    <country name="Panama">
        <rank update="yes">69</rank>
        <year>2011</year>
        <gdppc>13600</gdppc>
        <neighbor name="Costa Rica" direction="W"/>
        <neighbor name="Colombia" direction="E"/>
    </country>
</data>
```

### 解决方案

可以使用标准库中的`xml.etree.ElementTree`，其中的`parse`函数可以解析XML文档

```python
# 导入parse
>>> from xml.etree.ElementTree import parse
>>> f = open('a.xml')
# 获得ElementTree对象
>>> et = parse(f)
# 获取根节点，也就是data
>>> root = et.getroot()
>>> root
<Element 'data' at 0x00000203ECA1E728>
# 查看标签
>>> root.tag
'data'
# 属性
>>> root.attrib
{}
# 文本
>>> root.text.strip()
''
# 获得一个节点的子元素，然后在获取每个子元素的属性
>>> for child in root: print(child.get('name'))
...
Liechtenstein
Singapore
Panama
# 根据标签寻找子元素，每次之寻找第一个
>>> root.find('country')
<Element 'country' at 0x00000203ECBD3228>
# 寻找所有
>>> root.findall('country')
[<Element 'country' at 0x00000203ECBD3228>, <Element 'country' at 0x00000203ECBDBC28>, <Element 'country' at 0x00000203ECBDBDB8>]
# 获得一个生成器对象
>>> root.iterfind('country')
<generator object prepare_child.<locals>.select at 0x00000203ECBC5FC0>
>>> for e in root.iterfind('country'): print(e.get('name'))
...
Liechtenstein
Singapore
Panama
# 获取所有的元素节点
>>> list(root.iter())
[<Element 'data' at 0x00000203ECA1E728>, <Element 'country' at 0x00000203ECBD3228>, <Element 'rank' at 0x00000203ECBDBA98>, <Element 'year' at 0x00000203ECBDBAE8>, <Element 'gdppc' at 0x00000203ECBDBB38>, <Element 'neighbor' at 0x00000203ECBDBB88>, <Element 'neighbor' at 0x00000203ECBDBBD8>, <Element 'country' at 0x00000203ECBDBC28>, <Element 'rank' at 0x00000203ECBDBC78>, <Element 'year' at 0x00000203ECBDBCC8>, <Element 'gdppc' at 0x00000203ECBDBD18>, <Element 'neighbor' at 0x00000203ECBDBD68>, <Element 'country' at 0x00000203ECBDBDB8>, <Element 'rank' at 0x00000203ECBDBE08>, <Element 'year' at 0x00000203ECBDBE58>, <Element 'gdppc' at 0x00000203ECBDBEA8>, <Element 'neighbor' at 0x00000203ECBDBEF8>, <Element 'neighbor' at 0x00000203ECBDBF48>]
# 寻找标签为rank的子节点
>>> list(root.iter('rank'))
[<Element 'rank' at 0x00000203ECBDBA98>, <Element 'rank' at 0x00000203ECBDBC78>, <Element 'rank' at 0x00000203ECBDBE08>]
```
查找的高级用法
```python
# 匹配country下的所有子节点
>>> root.findall('country/*')
[<Element 'rank' at 0x00000203ECBDBA98>, <Element 'year' at 0x00000203ECBDBAE8>, <Element 'gdppc' at 0x00000203ECBDBB38>, <Element 'neighbor' at 0x00000203ECBDBB88>, <Element 'neighbor' at 0x00000203ECBDBBD8>, <Element 'rank' at 0x00000203ECBDBC78>, <Element 'year' at 0x00000203ECBDBCC8>, <Element 'gdppc' at 0x00000203ECBDBD18>, <Element 'neighbor' at 0x00000203ECBDBD68>, <Element 'rank' at 0x00000203ECBDBE08>, <Element 'year' at 0x00000203ECBDBE58>, <Element 'gdppc' at 0x00000203ECBDBEA8>, <Element 'neighbor' at 0x00000203ECBDBEF8>, <Element 'neighbor' at 0x00000203ECBDBF48>]
# 找到所有节点的rank
>>> root.findall('.//rank')
[<Element 'rank' at 0x00000203ECBDBA98>, <Element 'rank' at 0x00000203ECBDBC78>, <Element 'rank' at 0x00000203ECBDBE08>]
# 找到rank的父对象
>>> root.findall('.//rank/..')
[<Element 'country' at 0x00000203ECBD3228>, <Element 'country' at 0x00000203ECBDBC28>, <Element 'country' at 0x00000203ECBDBDB8>]
# 查找country包含name属性的
>>> root.findall('country[@name]')
[<Element 'country' at 0x00000203ECBD3228>, <Element 'country' at 0x00000203ECBDBC28>, <Element 'country' at 0x00000203ECBDBDB8>]
>>> root.findall('country[@age]')
[]
# 查找属性等于特定值的元素
>>> root.findall('country[@name="Singapore"]')
[<Element 'country' at 0x00000203ECBDBC28>]
# 查找必须包含某一个子元素
>>> root.findall('country[rank]')
[<Element 'country' at 0x00000203ECBD3228>, <Element 'country' at 0x00000203ECBDBC28>, <Element 'country' at 0x00000203ECBDBDB8>]
# 查找子元素等于指定的值
>>> root.findall('country[rank="5"]')
[<Element 'country' at 0x00000203ECBDBC28>]
# 根据位置查找，从1开始
>>> root.findall('country')
[<Element 'country' at 0x00000203ECBD3228>, <Element 'country' at 0x00000203ECBDBC28>, <Element 'country' at 0x00000203ECBDBDB8>]
# 根据位置查找
>>> root.findall('country[1]')
[<Element 'country' at 0x00000203ECBD3228>]
# 倒数第一个
>>> root.findall('country[last()]')
[<Element 'country' at 0x00000203ECBDBDB8>]
# 倒数第二个
>>> root.findall('country[last()-1]')
[<Element 'country' at 0x00000203ECBDBC28>]
```

## 如何构建xml文档？

实际案例

某些时候，我们需要将其他格式数据转换为xml，例如下面的字符串如何转换为XML？

```text
Date,Open,High,Low,Close,Volume,Adj Close
2016-09-14,9.17,9.18,9.05,9.06,42148100,9.06
```

### 解决方案

使用标准库中的`xml.etree.ElementTree`，构建`ElementTree`，使用`write`方法写入文件

```python
>>> from xml.etree.ElementTree import Element, ElementTree
>>> from xml.etree.ElementTree import tostring
>>> e = Element('Data')
>>> e.set('name', 'abc')
>>> e.text = '123'
# 查看结果
>>> tostring(e)
b'<Data name="abc">123</Data>'
>>> e2 = Element('Row')
>>> e3 = Element('Open')
>>> e3.text = '9.17'
>>> e2.append(e3)
>>> e.text = None
>>> e.append(e2)
>>> tostring(e)
b'<Data name="abc"><Row><Open>9.17</Open></Row></Data>'
>>> et = ElementTree(e)
>>> et.write('demo.xml')
```
解决如上问题的脚本如下：
```python
import csv
from xml.etree.ElementTree import Element, ElementTree


def pretty(e, level=0):
    if len(e) > 0:
        e.text = '\n' + '\t' * (level + 1)
        for child in e:
            pretty(child, level + 1)
        child.tail = child.tail[:-1]
    e.tail = '\n' + '\t' * level


def csvToXML(fname):
    with open(fname, 'rb') as f:
        reader = csv.reader(f)
        headers = reader.next()

        root = Element('Data')
        for row in reader:
            eRow = Element('Row')
            root.append(eRow)
            for tag, text in zip(headers, row):
                e = Element(tag)
                e.text = text
                eRow.append(e)
    pretty(root)
    return ElementTree(root)


et = csvToXML('pingan.csv')
et.write('pingan.xml')
```
转换好的文件内容为：
```xml
<Data>
        <Row>
                <Date>2016-09-15</Date>
                <Open>9.06</Open>
                <High>9.06</High>
                <Low>9.06</Low>
                <Close>9.06</Close>
                <Volume>000</Volume>
                <Adj Close>9.06</Adj Close>
        </Row>
        <Row>
                <Date>2016-09-14</Date>
                <Open>9.17</Open>
                <High>9.18</High>
                <Low>9.05</Low>
                <Close>9.06</Close>
                <Volume>42148100</Volume>
                <Adj Close>9.06</Adj Close>
        </Row>
		.....
```

## 如何读写excel文件？

实际案例

Microsoft Excel是日常办公中使用最频繁的软件，起数据格式为xls,xlsx,一种非常常用的电子表格。

某小学某班成绩记录在excel了文件中，内容如下：

```text
姓名	语文	数学	外语
小明	95	96	94
张三	85	84	92
王五	86	85	75
小哈	96	92	100
```

利用python读写excel了，添加总分列，计算每人总分

### 解决方案

使用第三方库xlrd和xlwt，这两个库分别用于excel读和写

安装这两个模块

```bash
pip3 install xlrd
pip3 install xlwt
```
脚本如下：
```python
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

# 导入xlrd和xlwt
import xlrd
import xlwt

# 打开excel文件
rbook = xlrd.open_workbook('x.xlsx')
# 表
rsheet = rbook.sheet_by_index(0)

# 添加一个总分的列

# 列
nc = rsheet.ncols
# 第0行，列，文本类型，文字，内容
rsheet.put_cell(0, nc, xlrd.XL_CELL_TEXT, '总分', None)

# 迭代表中的所有数据，计算总分
for row in range(1, rsheet.nrows):
    # 计算每行的总分，跳过第0行,0==姓名,sum对列表进行求和，t等于最后加上拿出来的分数
    t = sum(rsheet.row_values(row, 1))
    # 写入数据
    rsheet.put_cell(row, nc, xlrd.XL_CELL_NUMBER, t, None)

# 写入到文件中
wbook = xlwt.Workbook()
wsheet = wbook.add_sheet(rsheet.name)

# 对其方式，垂直和水平都是剧中
style = xlwt.easyxf('align: vertical center, horizontal center')

# rsheet的每个单元格写入到wsheet中
for r in range(rsheet.nrows):  # 行
    for c in range(rsheet.ncols):  # 列
        wsheet.write(r, c, rsheet.cell_value(r, c), style)

wbook.save('output.xlsx')
```
计算结果如下：
```text
姓名	语文	数学	外语	总分
小明	95	96	94	285
张三	85	84	92	261
王五	86	85	75	246
小哈	96	92	100	288
```