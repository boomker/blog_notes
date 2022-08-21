# Python算法实战系列之冒泡

1. 比较相邻的元素，如果第一个比第二个大，就交换他们两个的位置；
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这步做完后，最后的元素会是最大的数；
3. 针对所有的元素重复以上的步骤，除了最后一个，也就是每次比较之后最大的书不做任何操作；
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较；

- Code

```python
"""
Bubble Sort
"""

array = [45, 32, 12, 23]

for i in range(len(array)):
    for j in range(i + 1, len(array)):
        if array[i] > array[j]:
            array[i], array[j] = array[j], array[i]
print(array)
```

- Output

```
[12, 23, 32, 45]
```

## 参考

- [常见排序算法 - 冒泡排序 (Bubble Sort)](http://bubkoo.com/2014/01/12/sort-algorithm/bubble-sort/)
