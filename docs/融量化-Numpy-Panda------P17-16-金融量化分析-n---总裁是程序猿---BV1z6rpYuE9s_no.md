# 金融量化分析：P17：Numpy统计方法与随机数生成 🧮🎲

在本节课中，我们将学习NumPy模块提供的数学统计方法以及随机数生成功能。这些功能是金融量化分析中进行数据处理和模拟的基础。

## 概述

上一节我们介绍了NumPy数组的索引和切片操作。本节中，我们来看看NumPy提供的核心数学统计函数，以及如何生成用于模拟和分析的随机数。

![](img/fbf88d26276bceb63500b56fb60e1188_1.png)

## 统计方法 📊

NumPy提供了一系列用于计算数组统计量的方法。

### 求和与平均值

![](img/fbf88d26276bceb63500b56fb60e1188_3.png)

`sum`方法用于计算数组中所有元素的总和。`mean`方法用于计算数组元素的算术平均值。

```python
import numpy as np
A = np.array([1, 2, 3, 4, 5])
total = A.sum()    # 求和
average = A.mean() # 求平均值
```

![](img/fbf88d26276bceb63500b56fb60e1188_5.png)

### 方差与标准差

以下是两个重要的统计概念：方差和标准差。它们用于衡量一组数据的离散程度。

**方差**的计算公式为：每个数据与均值的差的平方和，再除以数据个数。
`方差 = Σ(每个数据 - 平均值)² / 数据个数`

![](img/fbf88d26276bceb63500b56fb60e1188_7.png)

在NumPy中，使用`var`方法计算方差。

![](img/fbf88d26276bceb63500b56fb60e1188_9.png)

```python
variance = A.var() # 计算方差
```

![](img/fbf88d26276bceb63500b56fb60e1188_11.png)

**标准差**是方差的平方根。它更常用于实际分析，因为它与原始数据的单位一致。
`标准差 = sqrt(方差)`

在NumPy中，使用`std`方法计算标准差。

```python
std_deviation = A.std() # 计算标准差
```

标准差的一个实用意义是，在数据符合正态分布等常见分布时，可以用“均值 ± 标准差”来估计大部分数据的范围。

### 最大值与最小值

`max`和`min`方法分别用于查找数组中的最大值和最小值。

```python
max_value = A.max() # 最大值
min_value = A.min() # 最小值
```

`argmax`和`argmin`方法则返回最大值和最小值在数组中的索引位置。

```python
max_index = A.argmax() # 最大值索引
min_index = A.argmin() # 最小值索引
```

## 随机数生成 🎲

NumPy的`random`子模块提供了强大的随机数生成功能，它扩展了Python内置`random`模块的能力，支持直接生成数组形式的随机数据。

以下是常用随机数生成函数及其用法：

### 生成随机整数

`randint`函数可以生成指定范围内的随机整数。通过指定`size`参数，可以直接生成任意形状的数组。

```python
# 生成一个0到10之间的随机整数
single_int = np.random.randint(0, 10)
# 生成一个3行5列的二维数组，元素为0到10之间的随机整数
array_2d = np.random.randint(0, 10, size=(3, 5))
```

### 生成随机浮点数

`random`函数（在NumPy中为`rand`）生成[0, 1)区间内的均匀分布随机浮点数。

```python
# 生成10个[0,1)之间的随机浮点数
random_floats = np.random.rand(10)
```

`uniform`函数生成指定区间[a, b)内均匀分布的随机浮点数。

```python
# 生成10个在2.0到4.0之间的随机浮点数
uniform_floats = np.random.uniform(2.0, 4.0, 10)
```

### 其他随机操作

`choice`函数从给定序列中随机选择元素。可以指定`size`参数进行多次选择。

```python
data = [1, 3, 4, 5]
# 从data中随机选择一个数
single_choice = np.random.choice(data)
# 从data中随机选择10个数（可重复）
multi_choice = np.random.choice(data, size=10)
```

`shuffle`函数用于原地打乱数组的顺序。

![](img/fbf88d26276bceb63500b56fb60e1188_13.png)

```python
arr = np.array([1, 2, 3, 4, 5])
np.random.shuffle(arr) # arr的顺序被打乱
```

## 总结

本节课中我们一起学习了NumPy的统计方法和随机数生成。

我们首先介绍了核心的统计函数，包括求和(`sum`)、平均值(`mean`)、方差(`var`)、标准差(`std`)、最值(`max`/`min`)及其索引(`argmax`/`argmin`)。这些是数据分析的基础工具。

接着，我们探索了NumPy的`random`模块。它提供了`randint`、`rand`、`uniform`、`choice`、`shuffle`等函数，不仅能实现Python内置`random`模块的功能，还能通过`size`参数直接生成指定形状的随机数数组，极大提升了批量数据生成的效率。

NumPy作为科学计算的基础包，其数组结构、批量运算、索引切片以及本节学习的统计与随机功能，共同构成了后续学习更高级数据分析库（如Pandas）的坚实基石。