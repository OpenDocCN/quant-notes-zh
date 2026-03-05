# Python金融量化投资分析：14：NumPy统计方法与随机数生成 📊🎲

在本节课中，我们将学习NumPy模块提供的数学统计方法和随机数生成功能。这些功能是进行金融数据分析和量化建模的基础工具。

![](img/b880afa1013b495c571b29daa1242362_1.png)

## 概述

上一节我们介绍了NumPy数组的索引和切片操作。本节中，我们来看看NumPy提供的核心数学统计函数，以及如何生成符合特定分布的随机数数组，这在模拟金融数据和计算风险指标时非常有用。

![](img/b880afa1013b495c571b29daa1242362_3.png)

## 数学与统计方法

![](img/b880afa1013b495c571b29daa1242362_5.png)

NumPy提供了一系列用于计算数组统计量的方法。

### 求和与平均值

![](img/b880afa1013b495c571b29daa1242362_7.png)

![](img/b880afa1013b495c571b29daa1242362_9.png)

`sum`方法用于计算数组中所有元素的总和。`mean`方法用于计算数组元素的算术平均值。

![](img/b880afa1013b495c571b29daa1242362_11.png)

```python
import numpy as np
A = np.array([1, 2, 3, 4, 5])
total = A.sum()    # 求和
average = A.mean() # 求平均值
```

### 方差与标准差

方差和标准差是衡量数据离散程度的重要指标。

*   **方差**的计算公式为：`方差 = Σ(每个数据 - 平均值)² / 数据个数`
    它表示数据偏离平均值的平均平方距离。方差越大，数据波动越大。
    ```python
    variance = A.var() # 计算方差
    ```

*   **标准差**是方差的平方根。
    公式为：`标准差 = sqrt(方差)`
    标准差具有与原数据相同的量纲，更便于解释。例如，在正态分布中，约有68%的数据落在“平均值 ± 1倍标准差”的范围内。
    ```python
    std_dev = A.std() # 计算标准差
    ```

### 最大值与最小值

`max`和`min`方法用于查找数组中的最大值和最小值。
`argmax`和`argmin`方法则返回最大值和最小值所在的**索引位置**。

```python
max_value = A.max()      # 最大值
min_value = A.min()      # 最小值
max_index = A.argmax()   # 最大值索引
min_index = A.argmin()   # 最小值索引
```

## 随机数生成

NumPy的`random`子模块提供了强大的随机数生成功能，并且支持直接生成数组，比Python内置的`random`模块更适合处理批量数据。

以下是`np.random`中一些常用函数及其与内置`random`模块的对比：

*   **生成随机浮点数**：`np.random.rand()` 生成[0, 1)区间的随机数。可以传入形状参数直接生成数组。
    ```python
    # 生成一个10个元素的随机数组
    arr1 = np.random.rand(10)
    # 生成一个3行5列的随机数组
    arr2 = np.random.rand(3, 5)
    ```

*   **生成随机整数**：`np.random.randint(low, high, size)` 在指定区间[low, high)内生成随机整数。`size`参数决定输出数组的形状。
    ```python
    # 生成10个0到10（不含10）的随机整数
    int_arr = np.random.randint(0, 10, 10)
    ```

*   **从给定序列中随机选择**：`np.random.choice(a, size)` 从数组`a`中随机抽取元素。`size`参数决定抽取次数和输出形状。
    ```python
    choices = np.random.choice([1, 3, 5, 7], size=10)
    ```

![](img/b880afa1013b495c571b29daa1242362_13.png)

*   **打乱数组顺序**：`np.random.shuffle(x)` 将数组`x`的元素顺序随机打乱（原地修改）。
    ```python
    arr = np.array([1, 2, 3, 4, 5])
    np.random.shuffle(arr)
    ```

*   **生成均匀分布随机数**：`np.random.uniform(low, high, size)` 在指定区间[low, high)内生成均匀分布的随机浮点数。
    ```python
    uniform_arr = np.random.uniform(2.0, 4.0, 100)
    ```

## 总结

本节课中我们一起学习了NumPy的统计与随机数功能。
我们掌握了如何使用`sum`、`mean`、`var`、`std`等函数计算数组的基本统计量。
同时，也熟悉了通过`np.random`模块生成各种随机数数组的方法，这为后续的金融数据模拟和蒙特卡洛分析打下了基础。
NumPy作为科学计算的基础，其高效的数据处理能力是进行量化分析不可或缺的一环。