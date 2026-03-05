# Python金融量化投资分析：P14：13金融量化分析-numpy-array通用函数 🧮

在本节课中，我们将要学习NumPy库中的通用函数。这些函数可以对数组进行快速的、元素级的数学运算，是进行金融数据批量处理和分析的基础工具。

上一节我们介绍了数组对象的索引功能，本节中我们来看看NumPy提供的强大运算工具。

## 通用函数概述

NumPy的通用函数可以对数组中的每个元素执行相同的数学运算，而无需编写循环。这使得代码更简洁，运行效率更高。

## 一元通用函数

一元函数是指只接受一个数组作为参数的函数。以下是常见的数学运算函数。

### 绝对值运算

![](img/ba7d7395c6aef5f34dcaeb97864b7de5_1.png)

`abs`函数用于计算数组中每个元素的绝对值。

```python
import numpy as np
A = np.array([-5, -2, 0, 2, 5])
result = np.abs(A)
```

![](img/ba7d7395c6aef5f34dcaeb97864b7de5_3.png)

### 平方根运算

![](img/ba7d7395c6aef5f34dcaeb97864b7de5_5.png)

`sqrt`函数用于计算数组中每个元素的平方根。需要注意的是，负数开方会得到`nan`（非数字）值。

![](img/ba7d7395c6aef5f34dcaeb97864b7de5_7.png)

```python
A = np.array([-5, -2, 0, 2, 5])
result = np.sqrt(A)  # 负数元素的结果为 nan
```

![](img/ba7d7395c6aef5f34dcaeb97864b7de5_9.png)

### 指数与对数运算

![](img/ba7d7395c6aef5f34dcaeb97864b7de5_11.png)

NumPy也提供了指数（`exp`）和对数（`log`）等数学函数。

```python
A = np.array([1, 2, 3])
exp_result = np.exp(A)  # 计算 e 的 A 次方
log_result = np.log(A)   # 计算自然对数
```

### 四种取整方式

在处理金融数据时，有时需要对小数进行取整。NumPy提供了四种不同的取整方式。

以下是四种取整方式的说明：
*   **向零取整**：直接舍弃小数部分。函数为 `np.trunc`。
*   **向下取整**：取不大于原数的最大整数。函数为 `np.floor`。
*   **向上取整**：取不小于原数的最小整数。函数为 `np.ceil`。
*   **四舍五入**：根据小数部分进行四舍五入。函数为 `np.round`。

```python
A = np.array([-1.6, -1.0, 1.0, 1.6])
print(np.trunc(A))   # 向零取整: [-1. -1.  1.  1.]
print(np.floor(A))   # 向下取整: [-2. -1.  1.  1.]
print(np.ceil(A))    # 向上取整: [-1. -1.  1.  2.]
print(np.round(A))   # 四舍五入: [-2. -1.  1.  2.]
```

### 分离整数与小数部分

`modf`函数可以将数组中每个元素的整数部分和小数部分分离，并分别返回两个数组。

```python
A = np.array([1.2, 2.7, -3.4])
integer_part, fractional_part = np.modf(A)
```

## 特殊浮点数值

在数值计算中，会遇到两种特殊的浮点数值：`nan`和`inf`。

### nan (Not a Number)

`nan`表示“不是一个数字”，通常由未定义的数学运算产生，例如 `0.0 / 0.0` 或对负数开平方。

```python
# 产生 nan 的例子
result1 = 0.0 / 0.0
result2 = np.sqrt(-1)
```

**重要特性**：`nan`不等于任何值，甚至不等于它自己。因此，判断一个值是否为`nan`，不能使用等号，必须使用`np.isnan`函数。

```python
arr = np.array([1, 2, np.nan, 4])
# 错误做法：arr == np.nan 会返回全部 False
# 正确做法：
mask = np.isnan(arr)  # 返回布尔数组 [False, False, True, False]
# 过滤掉 nan 值
clean_arr = arr[~np.isnan(arr)]
```

### inf (Infinity)

`inf`表示“无穷大”，通常由一个非零数除以零产生。

```python
# 产生 inf 的例子
result = 5.0 / 0.0
```

`inf`比任何有限数都大。判断一个值是否为`inf`，可以使用`np.isinf`函数。

```python
arr = np.array([1, 2, np.inf, 4])
mask = np.isinf(arr)  # 返回布尔数组 [False, False, True, False]
# 过滤掉 inf 值
clean_arr = arr[~np.isinf(arr)]
```

## 二元通用函数

二元函数是指接受两个数组作为参数的函数，它们会对两个数组中对应位置的元素进行操作。

### 最大值与最小值

`maximum`和`minimum`函数用于逐元素比较两个数组，并返回对应位置的最大值或最小值。

```python
A = np.array([3, 4, 1])
B = np.array([2, 5, 0])

max_result = np.maximum(A, B)  # 结果: [3, 5, 1]
min_result = np.minimum(A, B)  # 结果: [2, 4, 0]
```

这与求整个数组最大/最小值的`A.max()`或`np.max(A)`不同。

## 总结

![](img/ba7d7395c6aef5f34dcaeb97864b7de5_13.png)

本节课中我们一起学习了NumPy的通用函数。我们首先了解了一元函数，如`abs`、`sqrt`和各种取整函数，它们能对数组的每个元素进行数学变换。接着，我们认识了`nan`和`inf`这两个特殊的浮点数值，并学会了如何使用`np.isnan`和`np.isinf`来识别和处理它们。最后，我们介绍了二元函数`maximum`和`minimum`，用于逐元素比较两个数组。掌握这些通用函数，是高效进行金融数据清洗和计算分析的关键一步。