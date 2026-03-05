# Python量化交易：P16：金融量化分析-numpy-统计方法和随机数生成 📊

在本节课中，我们将学习NumPy模块提供的数学统计方法和随机数生成功能。这些功能是进行金融数据分析和模拟的基础。

## 概述

上一节我们介绍了NumPy数组的索引和切片操作。本节中，我们来看看NumPy提供的核心统计函数以及如何生成随机数数组，这些在量化分析中用于计算指标和模拟市场情景至关重要。

![](img/beffa71b9eeb1b73e62b9be1f6e2a1f1_1.png)

## 统计方法 📈

NumPy提供了一系列用于计算数组统计值的方法。

### 求和与平均值

![](img/beffa71b9eeb1b73e62b9be1f6e2a1f1_3.png)

`sum`方法用于计算数组中所有元素的总和。`mean`方法用于计算算术平均值。

```python
import numpy as np
A = np.array([1, 2, 3, 4, 5])
total = A.sum()    # 输出：15
average = A.mean() # 输出：3.0
```

![](img/beffa71b9eeb1b73e62b9be1f6e2a1f1_5.png)

### 方差与标准差

方差和标准差是衡量数据离散程度的重要指标。

*   **方差**：每个数据与平均值之差的平方的平均值。公式为：**方差 = Σ(每个值 - 平均值)² / 数据个数**。方差越大，数据波动越大。
*   **标准差**：方差的平方根。公式为：**标准差 = √方差**。它使得量纲与原始数据一致，更常用于估计数据范围。

![](img/beffa71b9eeb1b73e62b9be1f6e2a1f1_7.png)

![](img/beffa71b9eeb1b73e62b9be1f6e2a1f1_9.png)

```python
var_A = A.var()   # 计算数组A的方差
std_A = A.std()   # 计算数组A的标准差
```

举例说明，对于数组 `[1, 2, 3, 4, 5]`，平均值为3。
*   方差计算过程：`[(1-3)² + (2-3)² + (3-3)² + (4-3)² + (5-3)²] / 5 = 2.0`
*   标准差则为：`√2.0 ≈ 1.414`

![](img/beffa71b9eeb1b73e62b9be1f6e2a1f1_11.png)

在工业生产中，例如测量零件尺寸，方差或标准差越小，代表产品一致性越高，良品率可能越高。

### 最大值与最小值

`max`和`min`方法用于查找数组中的最大值和最小值。

```python
max_value = A.max() # 输出：5
min_value = A.min() # 输出：1
```

`argmax`和`argmin`方法返回最大值和最小值所在的**索引位置**。

```python
max_index = A.argmax() # 输出：4 (最后一个元素的位置)
min_index = A.argmin() # 输出：0 (第一个元素的位置)
```

## 随机数生成 🎲

NumPy的`random`子模块提供了强大的随机数生成功能，其优势在于能直接生成指定形状的随机数数组。

以下是`numpy.random`中一些常用函数及其与Python标准库`random`模块的对比：

### 生成随机浮点数

`random`函数生成[0.0, 1.0)区间内的随机浮点数。

```python
# 生成一个随机数
single_float = np.random.random()
# 生成一个包含10个随机数的数组
float_array = np.random.random(10)
# 生成一个3行5列的二维随机数组
float_matrix = np.random.random((3, 5))
```

### 生成随机整数

`randint`函数生成指定范围内的随机整数。

```python
# 生成一个0到10（不含10）的随机整数
single_int = np.random.randint(0, 10)
# 生成一个包含10个0到10随机整数的数组
int_array = np.random.randint(0, 10, 10)
# 生成一个3行5列的二维随机整数数组
int_matrix = np.random.randint(0, 10, (3, 5))
```

### 从给定序列中随机选择

`choice`函数从给定的一维数组中随机抽取元素。

```python
arr = np.array([1, 3, 4, 5])
# 随机抽取一个元素
single_choice = np.random.choice(arr)
# 随机抽取10次（可重复），形成一个新数组
multi_choice = np.random.choice(arr, 10)
```

### 打乱数组顺序

`shuffle`函数将数组本身顺序随机打乱。

```python
arr_to_shuffle = np.array([1, 3, 4, 5])
np.random.shuffle(arr_to_shuffle)
# arr_to_shuffle 的顺序已被随机打乱
```

### 生成特定分布的随机数

`uniform`函数生成指定区间内均匀分布的随机浮点数。

![](img/beffa71b9eeb1b73e62b9be1f6e2a1f1_13.png)

```python
# 生成一个[2.0, 4.0)区间内的随机数
single_uniform = np.random.uniform(2.0, 4.0)
# 生成10个[2.0, 4.0)区间内的随机数
uniform_array = np.random.uniform(2.0, 4.0, 10)
```

## 总结

本节课中我们一起学习了NumPy的统计与随机功能。

我们首先介绍了核心的统计方法，包括计算总和(`sum`)、平均值(`mean`)、方差(`var`)、标准差(`std`)，以及查找极值及其位置(`max`, `min`, `argmax`, `argmin`)。这些是数据分析的基础工具。

接着，我们探讨了NumPy强大的随机数生成能力。通过`numpy.random`子模块，我们可以高效地生成各种形状和分布的随机数数组，例如使用`random`、`randint`、`choice`、`shuffle`和`uniform`等函数，这为蒙特卡洛模拟等量化分析技术提供了便利。

NumPy作为科学计算的基础，其数组结构、批量运算、索引切片、通用函数以及本节学习的统计与随机功能，共同构成了Python数据分析的基石。在后续课程中，我们将基于这些知识，学习更高级的数据分析库Pandas。