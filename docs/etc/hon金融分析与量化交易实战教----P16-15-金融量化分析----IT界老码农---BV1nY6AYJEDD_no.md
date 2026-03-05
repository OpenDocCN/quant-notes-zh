# Python金融量化分析：P16：NumPy数组通用函数

## 概述
在本节课中，我们将要学习NumPy库中的通用函数。这些函数可以对数组进行快速的、元素级的数学运算，是进行金融数据批量处理和分析的基础工具。

## 通用函数简介
上一节我们介绍了NumPy数组的索引功能，本节中我们来看看NumPy提供的通用函数。通用函数可以对数组中的每个元素执行相同的操作，例如加减乘除等基本运算，以及更复杂的数学函数。

## 一元通用函数
一元通用函数是指只接受一个数组作为参数的函数。它们可以对数组中的每个元素执行独立的计算。

![](img/a4c8038e4fb3eaf41805be99fc2cdbb3_1.png)

### 绝对值运算
NumPy提供了`np.abs`函数来计算数组中每个元素的绝对值。虽然Python内置的`abs`函数有时也能处理NumPy数组，但使用`np.abs`是更规范的做法。

![](img/a4c8038e4fb3eaf41805be99fc2cdbb3_3.png)

```python
import numpy as np
A = np.array([-5, -3, 0, 3, 5])
result = np.abs(A)
```

![](img/a4c8038e4fb3eaf41805be99fc2cdbb3_5.png)

### 平方根运算
对于平方根运算，需要使用`np.sqrt`函数。需要注意的是，对负数开方在数学上无定义，NumPy会返回一个特殊值`nan`。

![](img/a4c8038e4fb3eaf41805be99fc2cdbb3_7.png)

![](img/a4c8038e4fb3eaf41805be99fc2cdbb3_9.png)

```python
A = np.array([-5, -3, 0, 3, 5])
result = np.sqrt(A)  # 负数元素将返回 nan
```

![](img/a4c8038e4fb3eaf41805be99fc2cdbb3_11.png)

### 取整函数
NumPy提供了多种取整方式，以满足不同的数学需求。以下是四种主要的取整函数：

1.  **`np.floor`**：向下取整（向负无穷方向）。
    *   公式：`floor(x) = max{n ∈ Z | n ≤ x}`
    *   示例：`np.floor(1.6) = 1`, `np.floor(-1.6) = -2`

2.  **`np.ceil`**：向上取整（向正无穷方向）。
    *   公式：`ceil(x) = min{n ∈ Z | n ≥ x}`
    *   示例：`np.ceil(1.6) = 2`, `np.ceil(-1.6) = -1`

3.  **`np.round`**：四舍五入。
    *   规则：小数部分≥0.5时向上取整，<0.5时向下取整。
    *   示例：`np.round(1.6) = 2`, `np.round(-1.6) = -2`

4.  **`np.trunc`**：截断取整（向零方向）。
    *   效果：直接去掉小数部分。
    *   示例：`np.trunc(1.6) = 1`, `np.trunc(-1.6) = -1`

### 分离整数与小数部分
`np.modf`函数可以将数组中每个元素的整数部分和小数部分分离，并分别返回两个数组。

```python
A = np.array([1.2, 2.7, -3.8])
integer_part, fractional_part = np.modf(A)
```

### 特殊值判断：nan与inf
在数值计算中，会遇到两种特殊的浮点数值：

*   **`nan`**：表示“不是一个数字”（Not a Number），例如0除以0或对负数开方。
*   **`inf`**：表示“无穷大”（Infinity），例如一个非零数除以0。

NumPy提供了专门的函数来判断这些值：

*   **`np.isnan(array)`**：判断数组中的元素是否为`nan`。
*   **`np.isinf(array)`**：判断数组中的元素是否为`inf`。

**重要提示**：`nan`不等于任何值，甚至不等于它自己。因此，不能使用`array == np.nan`来判断，必须使用`np.isnan(array)`。

```python
A = np.array([1, 2, 0, 4])
B = np.array([2, 0, 0, 2])
C = A / B  # 结果包含 inf 和 nan
# 过滤掉 nan 和 inf
filtered_C = C[~np.isnan(C) & ~np.isinf(C)]
```

## 二元通用函数
二元通用函数接受两个数组作为参数，并对它们对应位置的元素进行计算。

### 最大值与最小值
`np.maximum`和`np.minimum`函数用于逐元素比较两个数组，并返回对应位置的最大值或最小值。

```python
A = np.array([3, 4, 1, 8])
B = np.array([2, 5, 4, 6])
max_result = np.maximum(A, B)  # 结果: [3, 5, 4, 8]
min_result = np.minimum(A, B)  # 结果: [2, 4, 1, 6]
```

![](img/a4c8038e4fb3eaf41805be99fc2cdbb3_13.png)

## 总结
本节课中我们一起学习了NumPy的通用函数。我们首先了解了一元通用函数，如`np.abs`、`np.sqrt`和各种取整函数，并重点学习了如何处理特殊的`nan`和`inf`值。接着，我们介绍了二元通用函数`np.maximum`和`np.minimum`。掌握这些函数对于高效地进行金融数据的批量数学运算至关重要。