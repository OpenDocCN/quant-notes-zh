# Python金融量化分析：P18：16 金融量化分析-numpy-统计方法和随机数生成 📊

在本节课中，我们将要学习NumPy模块提供的数学统计方法以及随机数生成功能。我们将了解如何计算数据的均值、方差和标准差，并学习如何使用NumPy生成各种随机数数组。

![](img/9ed33e450380b5f139ec373ccde4d46f_1.png)

## 概述

上一节我们介绍了NumPy数组的索引和切片操作。本节中，我们来看看NumPy提供的核心数学统计函数，以及如何利用其`random`子模块生成随机数，这些功能在金融数据分析和模拟中至关重要。

## 统计方法

![](img/9ed33e450380b5f139ec373ccde4d46f_3.png)

NumPy提供了一系列用于数据统计的方法。以下是几个核心的统计函数及其应用。

![](img/9ed33e450380b5f139ec373ccde4d46f_5.png)

### 求和与平均值

`sum`方法用于计算数组中所有元素的总和。`mean`方法用于计算数组元素的算术平均值。

```python
import numpy as np
A = np.array([1, 2, 3, 4, 5])
total = A.sum()    # 求和
average = A.mean() # 求平均值
```

![](img/9ed33e450380b5f139ec373ccde4d46f_7.png)

![](img/9ed33e450380b5f139ec373ccde4d46f_9.png)

### 方差与标准差

![](img/9ed33e450380b5f139ec373ccde4d46f_11.png)

方差和标准差是衡量数据离散程度的重要指标。

*   **方差**：每个数据与平均值之差的平方的平均数。公式为：
    **`方差 = Σ(每个数据 - 平均值)² / 数据个数`**
    它表示整组数据的离散程度。方差越大，数据波动越大。
    ```python
    variance = A.var() # 计算方差
    ```

*   **标准差**：方差的平方根。公式为：
    **`标准差 = sqrt(方差)`**
    标准差常用于根据“均值±标准差”来估计数据的分布范围。
    ```python
    std_dev = A.std() # 计算标准差
    ```

举例来说，对于数组`[1, 2, 3, 4, 5]`，其平均值为3。方差计算过程为：`((1-3)² + (2-3)² + (3-3)² + (4-3)² + (5-3)²) / 5 = 2.0`。标准差则为`sqrt(2.0) ≈ 1.414`。

在工业生产中，例如测量零件尺寸，方差和标准差越小，代表产品的一致性越好，良品率越高。

### 最大值与最小值

`max`和`min`方法分别用于查找数组中的最大值和最小值。

```python
max_value = A.max() # 查找最大值
min_value = A.min() # 查找最小值
```

`argmax`和`argmin`方法则返回最大值和最小值在数组中的索引位置。

```python
max_index = A.argmax() # 最大值索引
min_index = A.argmin() # 最小值索引
```

## 随机数生成

NumPy的`random`子模块提供了强大的随机数生成功能，它扩展了Python内置`random`模块的能力，支持直接生成指定形状的随机数数组。

以下是`np.random`中一些常用函数及其与内置`random`模块的对比：

*   **`random` / `rand`**：生成[0, 1)区间内的随机浮点数。NumPy版支持生成数组。
    ```python
    # 内置random
    import random
    x = random.random()
    # NumPy版
    import numpy as np
    arr = np.random.rand(3, 5) # 生成一个3行5列的随机数组
    ```

*   **`randint`**：生成指定范围内的随机整数。NumPy版支持生成数组。
    ```python
    # 内置random
    x = random.randint(0, 10)
    # NumPy版
    arr = np.random.randint(0, 10, size=(2, 4)) # 生成一个2行4列的随机整数数组
    ```

*   **`choice`**：从给定序列中随机选择元素。NumPy版支持生成多次选择的结果数组。
    ```python
    # 内置random
    x = random.choice([1, 3, 4, 5])
    # NumPy版
    arr = np.random.choice([1, 3, 4, 5], size=10) # 随机选择10次，返回数组
    ```

*   **`shuffle`**：将序列顺序随机打乱。两者用法类似。
    ```python
    a_list = [1, 3, 4, 5]
    np.random.shuffle(a_list)
    ```

*   **`uniform`**：生成指定区间内均匀分布的随机浮点数。NumPy版支持生成数组。
    ```python
    # 内置random
    x = random.uniform(2.0, 4.0)
    # NumPy版
    arr = np.random.uniform(2.0, 4.0, size=10) # 生成10个在[2.0,4.0)的随机数
    ```

![](img/9ed33e450380b5f139ec373ccde4d46f_13.png)

NumPy的`random`模块基本上重写了内置`random`的所有函数，并增加了直接批量生成数组的功能，极大方便了数据模拟和科学计算。

## 总结

本节课中我们一起学习了NumPy的统计方法和随机数生成。

1.  我们掌握了核心的统计函数：求和(`sum`)、平均值(`mean`)、方差(`var`)、标准差(`std`)、最值(`max`/`min`)及其索引(`argmax`/`argmin`)。
2.  我们理解了方差和标准差在衡量数据离散程度方面的意义。
3.  我们学习了NumPy的`random`子模块，它提供了比Python内置模块更强大的随机数生成功能，特别是能够直接生成指定维度的随机数数组，这对于金融数据模拟和批量计算非常高效。

NumPy作为数据分析的基础包，其数组结构、批量运算、索引切片以及本节介绍的统计和随机功能，共同构成了处理数值数据的核心工具集。