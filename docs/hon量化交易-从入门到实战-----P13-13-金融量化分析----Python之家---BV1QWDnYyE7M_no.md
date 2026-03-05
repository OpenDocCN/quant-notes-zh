# Python量化交易：13：金融量化分析-numpy-array布尔型索引 🎯

![](img/bbc525778c439275aedded78ada45a35_1.png)

![](img/bbc525778c439275aedded78ada45a35_3.png)

![](img/bbc525778c439275aedded78ada45a35_5.png)

在本节课中，我们将要学习NumPy中一个非常强大且高效的功能——布尔型索引。通过它，我们可以轻松地根据条件筛选数组中的元素，这比使用Python内置的`filter`函数要简洁得多。

![](img/bbc525778c439275aedded78ada45a35_6.png)

![](img/bbc525778c439275aedded78ada45a35_8.png)

## 概述

![](img/bbc525778c439275aedded78ada45a35_10.png)

![](img/bbc525778c439275aedded78ada45a35_12.png)

![](img/bbc525778c439275aedded78ada45a35_14.png)

上一节我们介绍了NumPy数组的常规索引和切片。本节中，我们来看看如何使用布尔型索引来根据条件快速筛选数组元素。这是一种在数据处理和分析中极为常用的技术。

![](img/bbc525778c439275aedded78ada45a35_16.png)

![](img/bbc525778c439275aedded78ada45a35_18.png)

## 布尔型索引的原理

![](img/bbc525778c439275aedded78ada45a35_20.png)

![](img/bbc525778c439275aedded78ada45a35_22.png)

布尔型索引的原理可以分为两步。

第一步是进行数组与标量的比较运算。例如，对于一个数组`A`，执行`A > 5`的操作。这并非新功能，它返回一个与`A`形状相同的布尔数组，其中每个元素表示`A`中对应位置的元素是否满足条件（大于5）。

![](img/bbc525778c439275aedded78ada45a35_24.png)

![](img/bbc525778c439275aedded78ada45a35_26.png)

```python
# 示例：数组与标量比较
import numpy as np
A = np.array([8, 3, 6, 2, 9])
bool_array = A > 5
print(bool_array)  # 输出：[ True False  True False  True]
```

![](img/bbc525778c439275aedded78ada45a35_28.png)

第二步是将这个布尔数组作为索引传入原数组。NumPy会将布尔数组中值为`True`的位置所对应的原数组元素筛选出来。

![](img/bbc525778c439275aedded78ada45a35_30.png)

![](img/bbc525778c439275aedded78ada45a35_31.png)

![](img/bbc525778c439275aedded78ada45a35_33.png)

```python
# 示例：使用布尔数组进行索引
result = A[bool_array]
print(result)  # 输出：[8 6 9]
```

![](img/bbc525778c439275aedded78ada45a35_35.png)

![](img/bbc525778c439275aedded78ada45a35_37.png)

在实际使用中，我们通常将这两步合并为一行简洁的代码：`A[A > 5]`。

![](img/bbc525778c439275aedded78ada45a35_39.png)

![](img/bbc525778c439275aedded78ada45a35_41.png)

## 与Python基础方法的对比

![](img/bbc525778c439275aedded78ada45a35_43.png)

![](img/bbc525778c439275aedded78ada45a35_45.png)

以下是使用Python基础方法`filter`和NumPy布尔型索引筛选数组中大于5的元素的对比。

![](img/bbc525778c439275aedded78ada45a35_47.png)

**使用Python的`filter`函数：**
```python
A_list = [8, 3, 6, 2, 9]
filtered = list(filter(lambda x: x > 5, A_list))
print(filtered)  # 输出：[8, 6, 9]
```
这种方法代码较长，且需要转换为列表。

![](img/bbc525778c439275aedded78ada45a35_49.png)

![](img/bbc525778c439275aedded78ada45a35_51.png)

**使用NumPy布尔型索引：**
```python
A = np.array([8, 3, 6, 2, 9])
filtered = A[A > 5]
print(filtered)  # 输出：[8 6 9]
```
这种方法非常简洁直观，效率也更高。

![](img/bbc525778c439275aedded78ada45a35_53.png)

![](img/bbc525778c439275aedded78ada45a35_55.png)

![](img/bbc525778c439275aedded78ada45a35_56.png)

## 组合条件筛选

![](img/bbc525778c439275aedded78ada45a35_58.png)

有时我们需要根据多个条件来筛选数组。例如，筛选出数组中所有“大于5的偶数”。

![](img/bbc525778c439275aedded78ada45a35_60.png)

![](img/bbc525778c439275aedded78ada45a35_62.png)

**方法一：分步操作**
我们可以先筛选出大于5的数，再从结果中筛选偶数。
```python
A = np.array([8, 3, 6, 2, 9])
# 第一步：筛选大于5的数
B = A[A > 5]  # B = [8, 6, 9]
# 第二步：从B中筛选偶数
result = B[B % 2 == 0]
print(result)  # 输出：[8 6]
```

**方法二：单步操作（使用位运算符`&`）**
为了更高效，我们可以将两个条件组合，并使用位运算符`&`（表示“与”关系）。**注意：每个条件必须用括号括起来**。
```python
A = np.array([8, 3, 6, 2, 9])
result = A[(A > 5) & (A % 2 == 0)]
print(result)  # 输出：[8 6]
```
**重要提示：** 这里必须使用位运算符`&`，而不是Python关键字`and`。因为`&`是NumPy数组重载的按位与运算符，可以用于元素级别的逻辑“与”操作，而`and`关键字无法在此处使用。

## “或”条件筛选

![](img/bbc525778c439275aedded78ada45a35_64.png)

如果我们需要筛选满足条件A“或”条件B的元素，例如“大于5的数或偶数”，可以使用位运算符`|`（表示“或”关系）。

![](img/bbc525778c439275aedded78ada45a35_66.png)

```python
A = np.array([8, 3, 6, 2, 9])
# 筛选大于5 或 是偶数的元素
result = A[(A > 5) | (A % 2 == 0)]
print(result)  # 输出：[8 3 6 2 9]
```
在这个例子中，元素3（大于5）和元素2（偶数）都被筛选了出来。

## 总结

![](img/bbc525778c439275aedded78ada45a35_68.png)

![](img/bbc525778c439275aedded78ada45a35_70.png)

![](img/bbc525778c439275aedded78ada45a35_72.png)

本节课中我们一起学习了NumPy的布尔型索引。我们掌握了其两步原理：先通过比较运算生成布尔数组，再利用该数组进行索引筛选。我们对比了其与Python基础方法的优劣，并学习了如何使用位运算符`&`和`|`来组合多个条件进行“与”和“或”的筛选。这个功能强大且高效，是进行数据筛选和清洗的利器。