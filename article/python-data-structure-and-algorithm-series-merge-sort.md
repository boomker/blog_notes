# Python数据结构与算法系列之归并排序

归并排序`(Merge sort)`主要思想是分治法`(divide and conquer)`，就是要将`n`个元素的序列划分为`两个序列`，再将两个序列划分为4个序列，直到每个序列只有一个元素，最后，再将两个有序序列归并成一个有序的序列。

- Code

```python
"""
Merge Sort
"""


def merge(left_array, right_array):
    """
    对数组left_array和right_array进行归并
    """
    temp_array = []  # 新的已排序好的临时列表
    left_index = 0  # left_array列表的下标
    right_index = 0  # right_array列表的下标
    while left_index < len(left_array) and right_index < len(right_array):
        """
        对两个列表中的元素 两两对比，将最小的元素，放到result中，并对当前列表下标加1
        """
        if left_array[left_index] <= right_array[right_index]:  # 如果左边的值小于等于右边的值
            temp_array.append(left_array[left_index])  # 临时数组加入左边的值
            left_index += 1  # left_array数组的下标+1
        else:
            temp_array.append(right_array[right_index])  # 临时数组加入右边的值
            right_index += 1  # right_array数组的下标+1
    temp_array += left_array[left_index:]
    temp_array += right_array[right_index:]
    return temp_array


def merge_sort(array):
    """
    对数组array进行拆分
    """
    if len(array) <= 1:  # 如果数组的元素少于或只有一个
        return array  # 直接把数组返回
    middle = int(len(array) / 2)  # 把数组进行拆分
    left_array = merge_sort(array[:middle])  # 左边的数组
    right_array = merge_sort(array[middle:])  # 右边的数组
    return merge(left_array, right_array)  # 对排序好的两个列表合并，产生一个新的排序好的列表


print(merge_sort([8, 6, 2, 3, 1, 5, 7, 4]))
```

- Output

```
[1, 2, 3, 4, 5, 6, 7, 8]
```

## 参考

- [Python-归并排序](http://code.py40.com/algorithm/2017/06/29/python-归并排序/)
- [归并排序详解(python实现)](http://www.cnblogs.com/piperck/p/6030122.html)
- [python归并排序--递归实现](http://www.jianshu.com/p/3ad5373465fd)