# Python数据结构与算法系列之插入排序

插入排序`（英语：Insertion Sort）`是一种简单直观的排序算法。它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

```
[8, 6, 2, 3, 1, 5, 7, 4]
```

- Code

```python
"""
Insertion Sort
"""

array = [8, 6, 2, 3, 1, 5, 7, 4]

for Index in range(1, len(array)):  # 将第一个元素标记为已排序过的元素，所以就从1开始
    i = Index  # 当前值的索引
    temp = array[i]  # 当前值作为临时变量
    while i > 0 and temp < array[i - 1]:  # 所以大于0并且临时变量小于当前索引的前一个元素
        array[i] = array[i - 1]  # 当前索引的值等于前一个值
        i -= 1  # 把排序过的元素往左移一格
    array[i] = temp  # 当前值就等于临时变量

print(array)
```

- Output

```
[1, 2, 3, 4, 5, 6, 7, 8]
```

## 参考

- [维基百科 - 插入排序](https://zh.wikipedia.org/wiki/插入排序)
- [经典排序算法 – 插入排序Insertion sort](http://www.cnblogs.com/kkun/archive/2011/11/23/insertion_sort.html)
