# Python金融分析与量化交易实战教程：P9：Numpy 数组创建方法

在本节课中，我们将学习如何使用 NumPy 库创建不同类型的数组。NumPy 是 Python 中非常重要的科学计算库，广泛应用于金融分析和量化交易中。接下来，我们将介绍几种常见的数组创建方法，包括创建全零数组、全一数组、空数组等。

## 1. 创建全零数组

在 NumPy 中，使用 `np.zeros()` 函数可以创建一个元素全为零的数组。我们可以通过该方法快速初始化一个数组。

**示例：**

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_1.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_3.png)

```python
import numpy as np
A = np.zeros(10)  # 创建一个包含 10 个零的数组
```

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_5.png)

### 说明
默认情况下，`np.zeros()` 创建的是浮点型数组。如果你希望创建整数类型的数组，可以使用 `dtype` 参数指定：

```python
A = np.zeros(10, dtype=int)  # 创建一个包含 10 个零的整数类型数组
```

## 2. 创建全一数组

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_7.png)

除了全零数组，NumPy 还提供了 `np.ones()` 函数来创建元素全为一的数组。使用方法类似。

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_8.png)

**示例：**

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_10.png)

```python
import numpy as np
B = np.ones(10)  # 创建一个包含 10 个一的数组
```

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_11.png)

### 说明
同样，默认情况下返回的是浮点型数组。如果需要创建整数类型的全一数组，可以指定 `dtype` 参数：

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_13.png)

```python
B = np.ones(10, dtype=int)  # 创建一个包含 10 个一的整数类型数组
```

## 3. 创建空数组

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_15.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_16.png)

使用 `np.empty()` 函数可以创建一个空数组。空数组的内容是随机的，它并没有被初始化。

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_18.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_19.png)

**示例：**

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_21.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_23.png)

```python
import numpy as np
C = np.empty(10)  # 创建一个包含 10 个元素的空数组
```

### 说明
`np.empty()` 仅分配内存空间，并不会初始化数组的值，因此数组中的值是随机的，通常由之前程序的内存残留值决定。

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_25.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_27.png)

## 4. 创建指定范围的数组

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_29.png)

在 NumPy 中，我们可以使用 `np.arange()` 函数创建一个指定范围内的数组。与 Python 内建的 `range()` 类似，`np.arange()` 允许我们生成一个数值序列。

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_31.png)

**示例：**

```python
import numpy as np
D = np.arange(0, 100, 2)  # 创建一个从 0 到 100，步长为 2 的数组
```

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_33.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_35.png)

### 说明
`np.arange()` 可以设置起始值、结束值和步长。与 `range()` 不同，`np.arange()` 支持浮动步长：

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_37.png)

```python
E = np.arange(0, 1, 0.1)  # 创建一个从 0 到 1，步长为 0.1 的数组
```

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_39.png)

## 5. 创建线性空间

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_41.png)

`np.linspace()` 是一个用于创建线性空间的函数。它将指定区间内的数值等间隔分布。与 `np.arange()` 不同，`np.linspace()` 是通过指定间隔数来生成数组，而不是通过步长。

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_43.png)

**示例：**

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_45.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_46.png)

```python
import numpy as np
F = np.linspace(0, 100, 5)  # 创建一个从 0 到 100 的线性空间，包含 5 个数
```

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_47.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_49.png)

### 说明
`np.linspace()` 的三个参数分别是：起始值、终止值和元素个数。生成的数组元素均匀分布在指定区间内。

```python
G = np.linspace(0, 1, 100)  # 创建一个从 0 到 1 的线性空间，包含 100 个元素
```

## 6. 创建单位矩阵

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_51.png)

如果你需要创建一个单位矩阵，可以使用 `np.eye()` 函数。单位矩阵是对角线元素为 1，其余元素为 0 的方阵。

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_53.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_55.png)

**示例：**

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_57.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_59.png)

```python
import numpy as np
H = np.eye(3)  # 创建一个 3x3 的单位矩阵
```

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_61.png)

### 说明
`np.eye()` 的参数是矩阵的大小，它会返回一个大小为 `n x n` 的单位矩阵。

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_63.png)

## 总结

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_65.png)

![](img/3d9ef5d266b1a1cb8e9935bb8b96cb3a_66.png)

在本节课中，我们学习了使用 NumPy 创建不同类型的数组的方法，包括全零数组、全一数组、空数组、指定范围的数组、线性空间和单位矩阵。这些方法是进行金融数据分析和量化交易时常用的工具，掌握它们将帮助你更好地处理数据。