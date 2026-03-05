# 量化交易：P16：金融量化分析-numpy-统计方法和随机数生成 📊

在本节课中，我们将学习NumPy模块提供的数学统计方法，以及如何生成随机数。这些功能是金融数据分析和量化策略开发的基础工具。

![](img/20ca1153eeb4d7d0a9c3c502f390018c_1.png)

上一节我们介绍了NumPy数组的索引和切片操作，本节中我们来看看NumPy内置的统计函数。

## 统计方法

![](img/20ca1153eeb4d7d0a9c3c502f390018c_3.png)

NumPy提供了一系列用于数学和统计分析的方法。

![](img/20ca1153eeb4d7d0a9c3c502f390018c_5.png)

### 求和与平均值

`sum`方法用于对数组中的所有值进行求和。这与Python内置的`sum`函数功能类似。

![](img/20ca1153eeb4d7d0a9c3c502f390018c_7.png)

`mean`方法用于计算数组所有元素的算术平均值。

![](img/20ca1153eeb4d7d0a9c3c502f390018c_9.png)

![](img/20ca1153eeb4d7d0a9c3c502f390018c_11.png)

以下是相关代码示例：
```python
import numpy as np
A = np.array([1, 2, 3, 4, 5])
sum_result = A.sum()  # 求和
mean_result = A.mean() # 求平均值
```

### 方差与标准差

接下来介绍两个重要的数学概念：方差和标准差。它们用于衡量一组数据的离散程度。

方差的计算公式为：
**方差 = Σ(每个数据 - 平均值)² / 数据个数**

以数组`[1, 2, 3, 4, 5]`为例，其平均值为3。方差计算过程为：`((1-3)² + (2-3)² + (3-3)² + (4-3)² + (5-3)²) / 5 = 2.0`。方差越大，表示数据越分散。

标准差是方差的平方根。其计算公式为：
**标准差 = sqrt(方差)**

对于同一个数组，标准差为`sqrt(2.0) ≈ 1.414`。标准差常用于估计数据的分布范围，例如“平均值 ± 标准差”可以涵盖大部分数据点。

以下是NumPy中的实现：
```python
var_result = A.var()  # 计算方差
std_result = A.std()  # 计算标准差
```

### 最大值与最小值

`max`和`min`方法分别用于查找数组中的最大值和最小值。

`argmax`和`argmin`方法则返回最大值和最小值在数组中的索引位置（下标）。

以下是相关代码：
```python
max_value = A.max()      # 最大值
min_value = A.min()      # 最小值
max_index = A.argmax()   # 最大值索引
min_index = A.argmin()   # 最小值索引
```

在介绍了基础的统计方法后，我们接下来看看NumPy中另一个实用功能：随机数生成。

## 随机数生成

NumPy的随机数模块`np.random`功能比Python内置的`random`模块更强大，特别支持批量生成数组形式的随机数。

![](img/20ca1153eeb4d7d0a9c3c502f390018c_13.png)

以下是`np.random`中一些常用函数及其与`random`模块的对比：

*   `np.random.rand()`: 生成一个[0, 1)区间内的随机浮点数。类似于`random.random()`。
*   `np.random.randint()`: 生成指定范围内的随机整数。类似于`random.randint()`，但支持通过`size`参数批量生成数组。
    ```python
    # 生成一个0到10之间的随机整数
    single_int = np.random.randint(0, 10)
    # 生成一个3行5列的二维随机整数数组
    array_2d = np.random.randint(0, 10, size=(3, 5))
    ```
*   `np.random.choice()`: 从给定序列中随机选择元素。类似于`random.choice()`，同样支持批量生成。
    ```python
    arr = [1, 2, 3, 4, 5]
    # 随机选择一个元素
    single_choice = np.random.choice(arr)
    # 随机选择10个元素（可重复）
    multi_choice = np.random.choice(arr, size=10)
    ```
*   `np.random.shuffle()`: 随机打乱序列的顺序。功能与`random.shuffle()`相同。
*   `np.random.uniform()`: 生成指定区间内均匀分布的随机浮点数。类似于`random.uniform()`，支持批量生成。
    ```python
    # 生成一个2.0到4.0之间的随机浮点数
    single_float = np.random.uniform(2.0, 4.0)
    # 生成10个该区间内的随机浮点数
    multi_float = np.random.uniform(2.0, 4.0, size=10)
    ```

基本上，`random`模块中的常用函数在`np.random`中都有对应版本，并且增加了直接生成数组的能力，这在处理批量数据时非常高效。

本节课中我们一起学习了NumPy的核心统计方法（求和、均值、方差、标准差、最值）以及强大的随机数生成功能。这些工具是进行数据描述性分析和生成模拟数据的基础。NumPy作为一个基础数学包，还包含更多高级功能（如傅里叶变换、线性代数运算等），但本章节涵盖的内容已足够应对常见的金融数据分析任务。下一模块我们将进入数据分析的核心包——Pandas的学习。