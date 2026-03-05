# Python金融量化分析：P17：NumPy通用函数 🧮

在本节课中，我们将学习NumPy库中的通用函数。通用函数可以对数组中的每个元素进行快速、批量的数学运算，这是NumPy高效计算的核心功能之一。

上一节我们介绍了数组对象的索引功能，本节中我们来看看NumPy提供了哪些强大的通用函数。

## 通用函数简介

NumPy的数组对象支持加减乘除等基本运算，这些运算可以批量执行。通用函数则进一步扩展了这种能力，允许我们对数组中的所有元素执行更复杂的数学运算。

## 一元通用函数

![](img/1cf7a67fb8f8a617e3aa5e8baa038ed1_1.png)

一元函数是指只接受一个数组作为参数的函数。以下是几个常用的一元函数示例。

![](img/1cf7a67fb8f8a617e3aa5e8baa038ed1_3.png)

### 绝对值运算

![](img/1cf7a67fb8f8a617e3aa5e8baa038ed1_5.png)

标准库中的 `abs` 函数用于取绝对值。在NumPy中，我们可以使用 `np.abs` 对整个数组执行此操作。

![](img/1cf7a67fb8f8a617e3aa5e8baa038ed1_7.png)

```python
import numpy as np
A = np.array([-5, -2, 0, 2, 5])
result = np.abs(A)
# 结果: array([5, 2, 0, 2, 5])
```

![](img/1cf7a67fb8f8a617e3aa5e8baa038ed1_9.png)

### 平方根运算

![](img/1cf7a67fb8f8a617e3aa5e8baa038ed1_11.png)

对数组中的每个元素进行开方运算，可以使用 `np.sqrt` 函数。需要注意的是，负数开方会产生特殊值 `nan`。

```python
A = np.array([-5, -2, 0, 2, 5])
result = np.sqrt(A)
# 结果: array([nan, nan, 0., 1.41421356, 2.23606798])
```

### 取整函数

Python提供了多种取整方式，NumPy也提供了对应的向量化版本。

以下是四种主要的取整方式及其含义：
*   **向零取整**：直接去掉小数部分。`np.trunc`
*   **向下取整**：取不大于原数的最大整数。`np.floor`
*   **向上取整**：取不小于原数的最小整数。`np.ceil`
*   **四舍五入**：根据小数部分进行四舍五入。`np.round`

```python
A = np.array([-1.6, -0.5, 0.5, 1.6])
print(np.trunc(A))  # 向零取整: [-1. -0.  0.  1.]
print(np.floor(A))  # 向下取整: [-2. -1.  0.  1.]
print(np.ceil(A))   # 向上取整: [-1. -0.  1.  2.]
print(np.round(A))  # 四舍五入: [-2. -0.  0.  2.]
```

### 分离整数与小数部分

`np.modf` 函数可以将数组中每个元素的整数部分和小数部分分开，并返回两个独立的数组。

```python
A = np.array([1.6, -1.6, 2.3])
integer_part, fractional_part = np.modf(A)
# integer_part: array([ 1., -1.,  2.])
# fractional_part: array([ 0.6, -0.6,  0.3])
```

### 特殊浮点数值：`nan` 与 `inf`

在数值计算中，会遇到两种特殊的浮点数值：
*   **`nan`**：表示“不是一个数字”，例如 `0.0 / 0.0` 或对负数开方。
*   **`inf`**：表示“无穷大”，例如一个非零数除以 `0.0`。

判断数组中是否存在这些特殊值，不能使用普通的等号比较，而应使用专门的函数。

```python
A = np.array([0, 1, 2, 0])
B = np.array([1, 2, 0, 0])
C = A / B  # 结果: array([ 0.,  0.5,  inf,  nan])

# 判断 nan
nan_mask = np.isnan(C)
# 判断 inf
inf_mask = np.isinf(C)

# 过滤掉 nan 和 inf
valid_data = C[~np.isnan(C) & ~np.isinf(C)]
```

## 二元通用函数

二元函数是指接受两个数组作为参数的函数。NumPy将基本运算（如加减乘除）都向量化了。

以下是两个重要的二元函数：

### 逐元素最大值/最小值

`np.maximum` 和 `np.minimum` 函数用于比较两个数组对应位置上的元素，并返回每个位置上的最大值或最小值。

```python
A = np.array([3, 4, 1, 8])
B = np.array([2, 5, 6, 7])

max_result = np.maximum(A, B)  # 结果: array([3, 5, 6, 8])
min_result = np.minimum(A, B)  # 结果: array([2, 4, 1, 7])
```

## 总结

![](img/1cf7a67fb8f8a617e3aa5e8baa038ed1_13.png)

本节课中我们一起学习了NumPy的通用函数。我们首先了解了一元函数，如 `np.abs`、`np.sqrt` 和各种取整函数，并认识了 `nan` 和 `inf` 这两种特殊值及其判断方法。接着，我们学习了二元函数，特别是 `np.maximum` 和 `np.minimum` 的用法。掌握这些通用函数是进行高效数组计算的基础。