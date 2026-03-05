# 金融量化分析：P16：NumPy统计方法与随机数生成 📊🎲

在本节课中，我们将学习NumPy模块提供的数学统计方法，并了解如何使用NumPy生成随机数。这些功能是金融量化分析中进行数据处理和模拟的基础。

![](img/ddacbe078e3aba07e0b0c13e24596ddd_1.png)

## 概述

上一节我们介绍了NumPy数组的索引和切片操作。本节中，我们来看看NumPy提供的核心数学统计函数以及如何生成随机数数组。

![](img/ddacbe078e3aba07e0b0c13e24596ddd_3.png)

## 数学统计方法

NumPy提供了一系列用于计算数组统计量的方法。

![](img/ddacbe078e3aba07e0b0c13e24596ddd_5.png)

### 求和与平均值

![](img/ddacbe078e3aba07e0b0c13e24596ddd_7.png)

`sum`方法用于计算数组所有元素的总和。`mean`方法用于计算数组元素的算术平均值。

![](img/ddacbe078e3aba07e0b0c13e24596ddd_9.png)

```python
import numpy as np
A = np.array([1, 2, 3, 4, 5])
total = A.sum()    # 计算总和
average = A.mean() # 计算平均值
```

![](img/ddacbe078e3aba07e0b0c13e24596ddd_11.png)

### 方差与标准差

方差和标准差是衡量数据离散程度的重要指标。

**方差**的计算公式为：每个数据与均值的差的平方和，再除以数据个数。
`var`方法用于计算方差。

```python
variance = A.var() # 计算方差
```

**标准差**是方差的平方根，它保持了原始数据的量纲，更常用于实际分析。
`std`方法用于计算标准差。

```python
std_dev = A.std() # 计算标准差
```

标准差可以帮助估计数据的分布范围。例如，对于一组近似正态分布的数据，大约68%的数据会落在`[均值 - 标准差, 均值 + 标准差]`的区间内。

### 最大值与最小值

`max`和`min`方法分别用于查找数组中的最大值和最小值。
`argmax`和`argmin`方法则返回最大值和最小值在数组中的索引位置。

```python
max_value = A.max()      # 最大值
min_value = A.min()      # 最小值
max_index = A.argmax()   # 最大值索引
min_index = A.argmin()   # 最小值索引
```

## 随机数生成

NumPy的`random`子模块提供了强大的随机数生成功能，与Python内置的`random`模块类似，但支持直接生成数组。

以下是NumPy中常用的随机数生成函数：

*   **`np.random.rand`**: 生成指定形状的数组，元素为[0, 1)区间内的均匀分布随机浮点数。
    ```python
    np.random.rand(3, 5) # 生成一个3行5列的随机数组
    ```
*   **`np.random.randint`**: 生成指定范围内的随机整数。可以指定生成的数组形状。
    ```python
    np.random.randint(0, 10, size=(2, 4)) # 生成2行4列，范围在[0,10)的随机整数数组
    ```
*   **`np.random.choice`**: 从给定的一维数组中随机抽取元素。可以指定抽取次数和是否放回。
    ```python
    np.random.choice([1, 3, 4, 5], size=10) # 从列表中随机抽取10次
    ```
*   **`np.random.shuffle`**: 就地打乱一个序列的顺序。
*   **`np.random.uniform`**: 生成指定区间内均匀分布的随机浮点数数组。
    ```python
    np.random.uniform(2.0, 4.0, size=10) # 生成10个[2.0, 4.0)区间内的随机浮点数
    ```

## 总结

![](img/ddacbe078e3aba07e0b0c13e24596ddd_13.png)

本节课中我们一起学习了NumPy的核心统计与随机功能。

我们首先介绍了基础的统计方法，包括求和(`sum`)、求平均值(`mean`)、计算方差(`var`)和标准差(`std`)，以及查找极值及其索引(`max`, `min`, `argmax`, `argmin`)。这些是数据分析中描述数据特征的基本工具。

接着，我们探讨了NumPy的随机数生成能力。通过`np.random`子模块，我们可以高效地生成各种分布的随机数数组，这在进行蒙特卡洛模拟或生成测试数据时非常有用。

NumPy作为科学计算的基础，其提供的数组结构、批量运算、索引切片、通用函数以及本节学习的统计和随机功能，共同构成了后续学习更高级数据分析库（如Pandas）的坚实基石。