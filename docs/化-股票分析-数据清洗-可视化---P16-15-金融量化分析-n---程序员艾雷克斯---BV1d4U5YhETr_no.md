# Python金融量化投资分析：P16：15 金融量化分析-numpy-array通用函数 🧮

在本节课中，我们将要学习NumPy库中的通用函数。这些函数可以对数组进行高效的批量数学运算，是金融量化分析中进行数据处理和计算的基础。

上一节我们介绍了数组对象的索引功能，本节中我们来看看NumPy提供的强大数学运算工具。

## 通用函数概述

NumPy的通用函数可以对数组进行逐元素的批量运算。除了基本的加减乘除，NumPy还提供了许多其他数学函数。

### 绝对值运算

![](img/8f97c0d220fe522fcfbf0a6b145ee8f1_1.png)

`abs`函数用于计算数组中每个元素的绝对值。

```python
import numpy as np
A = np.array([-5, -3, 0, 3, 5])
result = np.abs(A)
```

![](img/8f97c0d220fe522fcfbf0a6b145ee8f1_3.png)

![](img/8f97c0d220fe522fcfbf0a6b145ee8f1_5.png)

在NumPy中，也可以直接使用Python内置的`abs`函数，但建议使用`np.abs`以确保兼容性。

![](img/8f97c0d220fe522fcfbf0a6b145ee8f1_7.png)

### 平方根运算

`sqrt`函数用于计算数组中每个元素的平方根。需要注意的是，负数无法进行实数范围内的开方运算。

![](img/8f97c0d220fe522fcfbf0a6b145ee8f1_9.png)

![](img/8f97c0d220fe522fcfbf0a6b145ee8f1_11.png)

```python
A = np.array([-5, -3, 0, 3, 5])
result = np.sqrt(A)  # 负数元素将返回nan
```

### 取整函数

NumPy提供了多种取整方式，以下是四种主要的取整函数及其含义：

1.  **向零取整** (`np.trunc`): 直接去掉小数部分。
    *   例如：`np.trunc(1.6) = 1`, `np.trunc(-1.6) = -1`
2.  **向下取整** (`np.floor`): 取不大于原数的最大整数。
    *   例如：`np.floor(1.6) = 1`, `np.floor(-1.6) = -2`
3.  **向上取整** (`np.ceil`): 取不小于原数的最小整数。
    *   例如：`np.ceil(1.6) = 2`, `np.ceil(-1.6) = -1`
4.  **四舍五入** (`np.round`): 根据小数部分进行四舍五入。
    *   例如：`np.round(1.6) = 2`, `np.round(-1.6) = -2`

```python
A = np.array([-1.6, -0.5, 0.5, 1.6])
print(np.trunc(A))  # 向零取整
print(np.floor(A))  # 向下取整
print(np.ceil(A))   # 向上取整
print(np.round(A))  # 四舍五入
```

### 分离整数与小数部分

`modf`函数可以将数组中每个元素的整数部分和小数部分分离，并分别返回两个数组。

```python
A = np.array([1.2, 2.7, -3.8])
integer_part, fractional_part = np.modf(A)
```

## 特殊数值：nan与inf

在数值计算中，会遇到两种特殊的浮点数值：`nan`和`inf`。

*   **`nan` (Not a Number)**: 表示“不是一个数字”。它不等于任何值，甚至不等于它自己。在无效的数学运算中会产生，例如：
    *   `0.0 / 0.0`
    *   对负数开平方
*   **`inf` (Infinity)**: 表示“无穷大”。它比任何有限数都大。在数学运算中，一个非零数除以零会产生`inf`。

以下是处理这些特殊值的函数：

*   **`np.isnan(array)`**: 判断数组中的每个元素是否为`nan`，返回布尔数组。
*   **`np.isinf(array)`**: 判断数组中的每个元素是否为`inf`，返回布尔数组。

```python
# 示例：生成包含nan和inf的数组
A = np.array([1, 2, 0, 4])
B = np.array([2, 0, 0, 2])
C = A / B  # 结果中会出现inf (2/0) 和 nan (0/0)

# 判断并过滤nan
nan_mask = np.isnan(C)
filtered_C = C[~nan_mask]  # 使用布尔索引过滤掉nan值

# 判断并过滤inf
inf_mask = np.isinf(C)
filtered_C_no_inf = C[~inf_mask]
```

## 二元通用函数

除了处理单个数组的一元函数，NumPy也提供了处理两个数组的二元函数。

以下是两个常用的二元比较函数：

*   **`np.maximum(array1, array2)`**: 逐元素比较两个数组，返回较大值组成的新数组。
*   **`np.minimum(array1, array2)`**: 逐元素比较两个数组，返回较小值组成的新数组。

```python
A = np.array([3, 4, 1, 7])
B = np.array([2, 5, 6, 3])

max_result = np.maximum(A, B)  # 结果: [3, 5, 6, 7]
min_result = np.minimum(A, B)  # 结果: [2, 4, 1, 3]
```

## 总结

![](img/8f97c0d220fe522fcfbf0a6b145ee8f1_13.png)

本节课中我们一起学习了NumPy的通用函数。我们了解了如何进行基本的数学运算（如绝对值、平方根），掌握了四种不同的取整方法，学会了如何处理特殊的数值`nan`和`inf`，并认识了用于数组间比较的二元函数。这些工具是进行金融数据清洗和计算分析的基石，熟练掌握它们将为后续更复杂的量化分析任务打下坚实基础。