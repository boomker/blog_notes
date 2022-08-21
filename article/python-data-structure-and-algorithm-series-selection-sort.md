# Python数据结构与算法系列之选择排序

选择排序`（Selection sort）`是一种简单直观的排序算法。它的工作原理如下：

1. 首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置；
2. 然后，再从剩余未排序元素中继续寻找最小（大）元素；
3. 然后放到已排序序列的末尾；
4. 以此类推，直到所有元素均排序完毕；

如有下面一个数组，我们需要进行选择排序，从小到大

```
[8, 6, 2, 3, 1, 5, 7, 4]
```

- Code

```python
"""
Selection Sort
"""

array = [8, 6, 2, 3, 1, 5, 7, 4]

for i in range(len(array) - 1):  # 重复元素个数-1次，因为最后一个元素已经是最大或者最小值了
    min_index = i  # 把第一个没有排序过的元素设置为最小值
    for j in range(i, len(array)):  # 遍历每一个没有排序过的元素
        if array[j] < array[min_index]:  # 如果元素小于现在的最小值
            min_index = j  # 将此元素设置为新的最小值
    array[i], array[min_index] = array[min_index], array[i]  # 将最小值和第一个没有排序过的位置交换

print(array)
```

- Output

```
[1, 2, 3, 4, 5, 6, 7, 8]
```

## 参考

- [常见排序算法 - 选择排序 (Selection Sort)](http://bubkoo.com/2014/01/13/sort-algorithm/selection-sort/)