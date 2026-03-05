# 金融量化分析：P16：NumPy数组通用函数 🧮

在本节课中，我们将学习NumPy库中的通用函数。这些函数可以对数组进行快速的逐元素运算，是进行高效数值计算的基础。我们将从简单的数学运算开始，逐步介绍取整、特殊值处理等高级功能。

## 概述

上一节我们介绍了NumPy数组的索引功能，本节中我们来看看NumPy提供的另一项核心功能：通用函数。通用函数可以对数组中的每个元素执行相同的操作，实现批量计算，极大地提升了数据处理效率。

## 通用函数简介

NumPy的数组对象支持加减乘除等基本运算，这些运算可以批量执行。对于更复杂的数学运算，NumPy也提供了相应的通用函数来实现批量处理。

## 一元通用函数

一元通用函数是指只接受一个数组作为参数的函数。以下是几个常用的一元函数示例。

### 绝对值运算

![](img/6e6e26b5a3daf5c247ea76442ffa2eaa_1.png)

标准库中有一个名为`abs`的函数，用于取绝对值。我们希望对数组中的所有值取绝对值。

```python
import numpy as np
A = np.array([-5, -2, 0, 2, 5])
# 使用NumPy的abs函数
result = np.abs(A)
# 直接使用Python内置的abs函数也可以
result2 = abs(A)
```

![](img/6e6e26b5a3daf5c247ea76442ffa2eaa_3.png)

### 平方根运算

![](img/6e6e26b5a3daf5c247ea76442ffa2eaa_5.png)

除了绝对值，我们可能还需要进行开方运算。NumPy提供了`sqrt`函数。

```python
A = np.array([4, 9, 16])
# 对数组中的每个元素开平方
result = np.sqrt(A)
```

![](img/6e6e26b5a3daf5c247ea76442ffa2eaa_7.png)

需要注意的是，对负数开方在数学上是不允许的，NumPy会返回一个特殊值`nan`。

```python
A = np.array([-4, 9, 16])
result = np.sqrt(A) # 结果包含 nan
```

![](img/6e6e26b5a3daf5c247ea76442ffa2eaa_9.png)

![](img/6e6e26b5a3daf5c247ea76442ffa2eaa_11.png)

### 指数与对数运算

NumPy还提供了指数运算（`exp`）和对数运算（`log`, `log10`）等数学函数。

```python
A = np.array([1, 2, 3])
exp_result = np.exp(A)   # 计算e的A次方
log_result = np.log(A)   # 计算自然对数
log10_result = np.log10(A) # 计算以10为底的对数
```

## 取整函数

将小数转换为整数有多种方法，NumPy提供了四种主要的取整方式。

以下是四种取整方式的对比：

*   **向零取整**：直接去掉小数部分。
    *   公式：`trunc(x)`
    *   代码：`np.trunc([1.6, -1.6])` 结果为 `[ 1., -1.]`
*   **四舍五入**：根据小数部分决定舍入方向。
    *   公式：`round(x)`
    *   代码：`np.round([1.6, -1.6])` 结果为 `[ 2., -2.]`
*   **向下取整**：取不大于原数的最大整数。
    *   公式：`floor(x)`
    *   代码：`np.floor([1.6, -1.6])` 结果为 `[ 1., -2.]`
*   **向上取整**：取不小于原数的最小整数。
    *   公式：`ceil(x)`
    *   代码：`np.ceil([1.6, -1.6])` 结果为 `[ 2., -1.]`

```python
A = np.array([1.6, -1.6, 2.5, -2.5])
print(np.trunc(A))  # 向零取整
print(np.round(A))  # 四舍五入
print(np.floor(A))  # 向下取整
print(np.ceil(A))   # 向上取整
```

### 分离整数与小数部分

`modf`函数可以将一个数组的整数部分和小数部分分开，返回两个数组。

```python
A = np.array([1.6, -1.6, 3.14])
integer_part, fractional_part = np.modf(A)
```

## 特殊值处理

在数值计算中，有时会遇到非法的数学运算，NumPy使用两个特殊的浮点数值来表示这些情况。

### NaN (Not a Number)

`nan`表示“不是一个数字”，通常出现在未定义的数学运算中，例如0除以0或对负数开方。

```python
# 0除以0的情况
A = np.array([0, 1, 2, 3])
B = np.array([0, 1, 1, 1])
result = A / B  # 第一个元素为 nan
```

一个重要的特性是，`nan`不等于任何值，甚至不等于它自己。

```python
print(np.nan == np.nan)  # 输出 False
```

因此，判断一个数组中的值是否为`nan`，不能使用等号，必须使用专门的`isnan`函数。

```python
arr = np.array([1, 2, np.nan, 4])
# 正确的方法：使用 isnan
nan_mask = np.isnan(arr)
# 过滤掉 nan 值
filtered_arr = arr[~nan_mask]
```

### Inf (Infinity)

`inf`表示“无穷大”，通常出现在一个非零数除以零的情况。

```python
A = np.array([1, 2, 3])
B = np.array([0, 1, 2])
result = A / B  # 第一个元素为 inf
```

与`nan`不同，`inf`是等于它自己的。

```python
print(np.inf == np.inf)  # 输出 True
```

判断一个值是否为`inf`，可以使用`isinf`函数，或者直接与`np.inf`比较。

```python
arr = np.array([1, 2, np.inf, 4])
# 方法一：使用 isinf
inf_mask = np.isinf(arr)
# 方法二：直接比较（因为 inf == inf）
inf_mask2 = arr == np.inf
# 过滤掉 inf 值
filtered_arr = arr[~inf_mask]
```

## 二元通用函数

二元通用函数接受两个数组作为参数，并对它们进行逐元素的运算。

### 最大值与最小值

`maximum`和`minimum`函数用于逐元素比较两个数组，返回每个位置上的最大值或最小值。

```python
A = np.array([3, 4, 1, 8])
B = np.array([2, 5, 4, 6])
max_result = np.maximum(A, B)  # 逐元素取最大值：[3, 5, 4, 8]
min_result = np.minimum(A, B)  # 逐元素取最小值：[2, 4, 1, 6]
```

这与求整个数组最大值（`A.max()`）或最小值（`A.min()`）的操作是不同的。

## 总结

![](img/6e6e26b5a3daf5c247ea76442ffa2eaa_13.png)

本节课中我们一起学习了NumPy的通用函数。我们首先了解了一元函数，如`abs`、`sqrt`和各类取整函数。然后，我们探讨了如何处理计算中出现的特殊值`nan`和`inf`。最后，我们介绍了二元函数`maximum`和`minimum`的用法。掌握这些通用函数是进行高效数组计算和后续金融数据分析的重要基础。