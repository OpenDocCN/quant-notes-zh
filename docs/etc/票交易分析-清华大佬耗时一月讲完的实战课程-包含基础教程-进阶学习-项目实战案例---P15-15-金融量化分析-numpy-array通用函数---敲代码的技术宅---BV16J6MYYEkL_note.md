# Python金融量化：P15：金融量化分析-numpy-array通用函数 🧮

在本节课中，我们将要学习NumPy库中的通用函数。这些函数可以对数组进行快速的、元素级的数学运算，是进行高效数值计算的基础。

上一节我们介绍了数组对象的索引功能，本节中我们来看看NumPy提供的强大通用函数。

## 通用函数简介

NumPy的通用函数可以对数组进行批量运算。除了基本的加减乘除，NumPy还提供了许多其他数学运算的向量化版本。

## 一元通用函数

一元函数是指只接受一个数组作为参数的函数。以下是常用的一元函数及其说明。

### 绝对值运算

![](img/d544ddbb3b47dffc5e71c78dd290f647_1.png)

`np.abs` 函数用于计算数组中每个元素的绝对值。

![](img/d544ddbb3b47dffc5e71c78dd290f647_3.png)

```python
import numpy as np
a = np.array([-5, -3, 0, 3, 5])
result = np.abs(a)  # 结果为 [5 3 0 3 5]
```

### 平方根运算

![](img/d544ddbb3b47dffc5e71c78dd290f647_5.png)

`np.sqrt` 函数用于计算数组中每个元素的平方根。对于负数，会返回 `nan`。

![](img/d544ddbb3b47dffc5e71c78dd290f647_7.png)

```python
a = np.array([4, 9, 16])
result = np.sqrt(a)  # 结果为 [2. 3. 4.]
```

![](img/d544ddbb3b47dffc5e71c78dd290f647_9.png)

### 指数与对数运算

![](img/d544ddbb3b47dffc5e71c78dd290f647_11.png)

NumPy提供了 `np.exp`（指数）、`np.log`（自然对数）、`np.log10`（以10为底的对数）等函数。

```python
a = np.array([1, 2, 3])
result_exp = np.exp(a)  # 计算 e^a
result_log = np.log(a)  # 计算 ln(a)
```

### 四种取整方式

在处理小数时，有四种不同的取整方式，它们对应不同的数学规则。

以下是四种取整方式的对比：

1.  **向零取整 (`np.trunc`)**: 直接舍弃小数部分。
    *   公式：`trunc(x) = sign(x) * floor(|x|)`
    *   示例：`np.trunc([1.6, -1.6])` 结果为 `[ 1. -1.]`

2.  **向下取整 (`np.floor`)**: 取不大于原数的最大整数。
    *   公式：`floor(x) = max{n ∈ Z | n ≤ x}`
    *   示例：`np.floor([1.6, -1.6])` 结果为 `[ 1. -2.]`

3.  **向上取整 (`np.ceil`)**: 取不小于原数的最小整数。
    *   公式：`ceil(x) = min{n ∈ Z | n ≥ x}`
    *   示例：`np.ceil([1.6, -1.6])` 结果为 `[ 2. -1.]`

4.  **四舍五入 (`np.round`)**: 根据小数部分进行四舍五入。
    *   规则：小数部分 ≥ 0.5 时向上取整，否则向下取整。
    *   示例：`np.round([1.6, -1.6, 1.4])` 结果为 `[ 2. -2.  1.]`

### 分离整数与小数部分

`np.modf` 函数可以将数组中每个元素的整数部分和小数部分分开，返回两个数组。

```python
a = np.array([1.6, -1.6, 3.0])
fractional_part, integer_part = np.modf(a)
# fractional_part 为 [ 0.6 -0.6  0. ]
# integer_part 为 [ 1. -1.  3.]
```

## 特殊浮点数值

在数值计算中，会遇到两种特殊的浮点数值：`nan` 和 `inf`。

### 非数字 (nan)

`nan` 代表“Not a Number”，用于表示未定义或不可表示的数字，例如 `0/0` 或负数的平方根。

**重要特性**：`nan` 不等于任何值，甚至不等于它自己。因此，判断一个值是否为 `nan` 必须使用 `np.isnan` 函数。

```python
a = np.array([0, 1, 2])
b = a / a  # 结果为 [nan  1.  1.]
print(np.isnan(b))  # 结果为 [ True False False]
print(b[0] == np.nan)  # 结果为 False，这是错误的判断方式
```

### 无穷大 (inf)

`inf` 代表“Infinity”，用于表示超出浮点数表示范围的数值，例如 `5/0`。

**重要特性**：`inf` 等于它自己。可以使用 `==` 或 `np.isinf` 进行判断。

```python
a = np.array([5, 6])
b = np.array([0, 0])
c = a / b  # 结果为 [inf inf]
print(c == np.inf)  # 结果为 [ True  True]
print(np.isinf(c))  # 结果为 [ True  True]
```

## 二元通用函数

二元函数是指接受两个数组作为参数的函数。除了基本的加(`+`)、减(`-`)、乘(`*`)、除(`/`)，还有两个重要的函数。

### 元素级最大值与最小值

`np.maximum` 和 `np.minimum` 函数用于逐元素比较两个数组，返回每个位置上的最大值或最小值。

```python
a = np.array([3, 4, 5])
b = np.array([2, 5, 1])
max_result = np.maximum(a, b)  # 结果为 [3 5 5]
min_result = np.minimum(a, b)  # 结果为 [2 4 1]
```

这与 `a.max()`（求数组a自身的最大值）有本质区别。

---

![](img/d544ddbb3b47dffc5e71c78dd290f647_13.png)

本节课中我们一起学习了NumPy的通用函数。我们了解了如何对数组进行批量数学运算，包括取绝对值、开方、取整等一元函数，以及处理 `nan` 和 `inf` 特殊值的方法，最后学习了逐元素比较的二元函数。掌握这些函数是进行高效数据计算和分析的关键步骤。