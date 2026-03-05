# 量化交易教程：P16：14 金融量化分析-numpy-array通用函数 🧮

在本节课中，我们将要学习NumPy库中一个非常强大的功能——通用函数。通用函数允许我们对数组进行快速、批量的数学运算，这对于金融数据的处理至关重要。

上一节我们介绍了数组对象的索引功能，本节中我们来看看NumPy还提供了哪些高效的批量运算工具。

## 通用函数概述

NumPy的通用函数是一种对数组进行逐元素运算的函数。这意味着，我们可以像对单个数字一样进行加减乘除，但这次是针对整个数组中的所有元素同时进行。

## 一元通用函数

一元通用函数是指只接受一个数组作为输入的函数。以下是一些常用的一元函数及其应用。

![](img/f7dba4c515893403955f18fac5797a9f_1.png)

### 绝对值运算

![](img/f7dba4c515893403955f18fac5797a9f_3.png)

标准库中的`abs`函数可以用于取绝对值。在NumPy中，我们可以使用`np.abs`对整个数组进行绝对值运算。

![](img/f7dba4c515893403955f18fac5797a9f_5.png)

```python
import numpy as np
A = np.array([-5, -3, 0, 3, 5])
result = np.abs(A)
```

![](img/f7dba4c515893403955f18fac5797a9f_7.png)

### 平方根运算

对数组中的每个元素进行开方运算，可以使用`np.sqrt`函数。需要注意的是，负数开方在数学上无定义，NumPy会返回一个特殊值`nan`。

![](img/f7dba4c515893403955f18fac5797a9f_9.png)

![](img/f7dba4c515893403955f18fac5797a9f_11.png)

```python
A = np.array([-5, -3, 0, 3, 5])
result = np.sqrt(A)  # 负数元素将返回 nan
```

### 取整函数

将小数转换为整数有多种方式，NumPy提供了四种主要的取整函数。

以下是四种取整方式的对比：
*   **`np.trunc` / `np.fix`**：向零取整。例如，1.6变为1，-1.6变为-1。
*   **`np.floor`**：向下取整。例如，1.6变为1，-1.6变为-2。
*   **`np.ceil`**：向上取整。例如，1.6变为2，-1.6变为-1。
*   **`np.round`**：四舍五入。例如，1.6变为2，-1.6变为-2。

```python
A = np.array([-1.6, -0.5, 0.5, 1.6])
print(np.trunc(A))  # 向零取整
print(np.floor(A))  # 向下取整
print(np.ceil(A))   # 向上取整
print(np.round(A))  # 四舍五入
```

### 分离整数与小数部分

`np.modf`函数可以将一个数组中的每个元素分解为其整数部分和小数部分，并返回两个独立的数组。

```python
A = np.array([1.6, -1.6, 2.3])
integer_part, fractional_part = np.modf(A)
```

## 特殊浮点数值

在进行数学运算时，有时会遇到无法定义的结果。NumPy使用两个特殊的浮点数值来标记这些情况。

### NaN (Not a Number)

`NaN`表示“不是一个数字”，通常出现在无效的数学运算中，例如0除以0或对负数开方。一个关键特性是，`NaN`不等于任何值，甚至不等于它自身。

```python
# 判断数组中的NaN值
A = np.array([1, 2, np.nan, 4])
has_nan = np.isnan(A)  # 返回布尔数组 [False, False, True, False]

# 过滤掉NaN值
filtered_A = A[~np.isnan(A)]
```

### Inf (Infinity)

`Inf`表示“无穷大”，通常出现在一个非零数除以零的情况下。`Inf`比任何有限的数字都大。

```python
# 判断数组中的Inf值
B = np.array([1, 2, np.inf, 4])
has_inf = np.isinf(B)

# 过滤掉Inf值（因为Inf等于自身，可以直接比较）
filtered_B = B[B != np.inf]
```

## 二元通用函数

二元通用函数接受两个数组作为输入，并对它们进行逐元素的运算。

### 最大值与最小值比较

`np.maximum`和`np.minimum`函数用于逐元素比较两个数组，返回每个位置上较大或较小的值。

```python
A = np.array([3, 4, 1])
B = np.array([2, 5, 0])

max_values = np.maximum(A, B)  # 返回 [3, 5, 1]
min_values = np.minimum(A, B)  # 返回 [2, 4, 0]
```

---

![](img/f7dba4c515893403955f18fac5797a9f_13.png)

本节课中我们一起学习了NumPy的通用函数。我们掌握了一元函数（如`abs`, `sqrt`, 取整函数）和二元函数（如`maximum`, `minimum`）的用法，并了解了处理特殊浮点数值（`NaN`和`Inf`）的方法。这些工具是进行高效金融数据计算和分析的基础。