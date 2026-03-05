# Python金融分析：P8：NumPy基础与应用 📊

在本节课中，我们将学习如何使用NumPy，这是一个高效的科学计算和数据分析基础包。NumPy是许多数据分析和量化交易相关库的基础之一。在后续的课程中，我们还会进一步探讨基于NumPy的其他包，如Pandas。让我们从NumPy的核心功能和安装方法开始，逐步了解如何在实际项目中应用它。

## 1. NumPy概述

![](img/75d9dd856075c6c501a4d96f998cf15e_1.png)

NumPy是一个用于处理大规模数据的高效库。它最主要的特点是提供了一个多维数据结构——`ndarray`，这是一个高效的数组对象，适合进行批量计算和数据处理。

### 主要特点：
- **多维数据结构**：支持高维数组，适合进行矩阵和向量操作。
- **高效计算**：对大量数据的批量计算非常高效，避免了Python原生列表的低效。
- **强大功能**：提供了许多数学函数，包括线性代数、傅里叶变换、随机数生成等。

![](img/75d9dd856075c6c501a4d96f998cf15e_3.png)

![](img/75d9dd856075c6c501a4d96f998cf15e_4.png)

## 2. 安装与引用NumPy

![](img/75d9dd856075c6c501a4d96f998cf15e_6.png)

### 安装方法

![](img/75d9dd856075c6c501a4d96f998cf15e_8.png)

使用以下命令通过`pip`安装NumPy：

```bash
pip install numpy
```

### 引用方法

在Python中，引用NumPy的常见方式是使用以下代码：

```python
import numpy as np
```

这样，你可以通过`np`来调用NumPy中的函数和方法。

## 3. 基本应用

![](img/75d9dd856075c6c501a4d96f998cf15e_10.png)

### 创建NumPy数组

![](img/75d9dd856075c6c501a4d96f998cf15e_12.png)

![](img/75d9dd856075c6c501a4d96f998cf15e_13.png)

NumPy提供了`np.array`方法，将Python的列表或其他类型的数据转换为NumPy数组。

![](img/75d9dd856075c6c501a4d96f998cf15e_15.png)

```python
import numpy as np

![](img/75d9dd856075c6c501a4d96f998cf15e_17.png)

# 创建一个一维数组
arr = np.array([1, 2, 3, 4])
print(arr)
```

![](img/75d9dd856075c6c501a4d96f998cf15e_19.png)

### 数组运算

![](img/75d9dd856075c6c501a4d96f998cf15e_21.png)

NumPy数组支持直接的数学运算。例如，假设你有两个数组`A`和`B`，你可以直接进行加减乘除等操作，而不需要显式地使用循环。

```python
A = np.array([1, 2, 3])
B = np.array([4, 5, 6])

![](img/75d9dd856075c6c501a4d96f998cf15e_23.png)

![](img/75d9dd856075c6c501a4d96f998cf15e_24.png)

# 数组相加
C = A + B
print(C)
```

![](img/75d9dd856075c6c501a4d96f998cf15e_26.png)

### 示例：公司市值转换

![](img/75d9dd856075c6c501a4d96f998cf15e_28.png)

假设你有若干公司市值的列表，现在需要将这些市值从美元转换为人民币。我们可以通过NumPy高效地实现。

![](img/75d9dd856075c6c501a4d96f998cf15e_30.png)

```python
import numpy as np

# 假设美元市值
usd_values = np.random.uniform(100, 200, 50)  # 随机生成50个100到200之间的数

# 汇率
usd_to_cny = 6.8

# 将美元市值转换为人民币
cny_values = usd_values * usd_to_cny
print(cny_values)
```

## 4. NumPy数组的核心概念

![](img/75d9dd856075c6c501a4d96f998cf15e_32.png)

### ndarray对象

![](img/75d9dd856075c6c501a4d96f998cf15e_34.png)

![](img/75d9dd856075c6c501a4d96f998cf15e_35.png)

NumPy的核心对象是`ndarray`。它是一个多维数组对象，可以包含数字、字符串或其他类型的数据，但通常是用于数字计算。数组的所有元素类型必须相同。

![](img/75d9dd856075c6c501a4d96f998cf15e_37.png)

![](img/75d9dd856075c6c501a4d96f998cf15e_39.png)

### 数组与Python列表的区别

![](img/75d9dd856075c6c501a4d96f998cf15e_41.png)

与Python列表不同，NumPy数组的所有元素类型是相同的，且数组的大小是固定的，无法动态调整。这个特性使得NumPy数组在进行批量计算时非常高效。

![](img/75d9dd856075c6c501a4d96f998cf15e_43.png)

### 数组创建

![](img/75d9dd856075c6c501a4d96f998cf15e_45.png)

你可以通过列表、元组或其他数组来创建NumPy数组。下面是几种创建方法：

```python
arr1 = np.array([1, 2, 3])  # 从列表创建
arr2 = np.array([1.5, 2.5, 3.5])  # 从浮点数列表创建
```

## 5. 常见的NumPy属性

### 数组类型

你可以使用`dtype`属性来查看数组元素的类型。

![](img/75d9dd856075c6c501a4d96f998cf15e_47.png)

```python
arr = np.array([1, 2, 3])
print(arr.dtype)  # 输出: int64
```

![](img/75d9dd856075c6c501a4d96f998cf15e_49.png)

NumPy支持多种数据类型，包括整数型、浮点型和布尔型等。常见的数据类型如下：

![](img/75d9dd856075c6c501a4d96f998cf15e_51.png)

- `int8`、`int16`、`int32`、`int64`：不同大小的整数
- `float32`、`float64`：不同精度的浮点数
- `bool`：布尔类型
- `complex`：复数类型

![](img/75d9dd856075c6c501a4d96f998cf15e_53.png)

### 数组形状

![](img/75d9dd856075c6c501a4d96f998cf15e_55.png)

`shape`属性返回数组的维度。对于二维数组，它返回一个元组，表示行和列的数量。

```python
arr = np.array([[1, 2, 3], [4, 5, 6]])
print(arr.shape)  # 输出: (2, 3)
```

### 数组大小

使用`size`属性可以查看数组中元素的总数。

```python
arr = np.array([1, 2, 3, 4])
print(arr.size)  # 输出: 4
```

## 6. 数组的基本操作

### 转置数组

你可以使用`T`属性来转置二维数组，将行列互换。

```python
arr = np.array([[1, 2, 3], [4, 5, 6]])
print(arr.T)  # 输出转置后的数组
```

![](img/75d9dd856075c6c501a4d96f998cf15e_57.png)

### 切片与索引

![](img/75d9dd856075c6c501a4d96f998cf15e_59.png)

NumPy数组支持切片和索引，操作方法与Python列表类似。

```python
arr = np.array([1, 2, 3, 4, 5])
print(arr[1:4])  # 输出: [2, 3, 4]
```

## 7. 总结

![](img/75d9dd856075c6c501a4d96f998cf15e_61.png)

在本节课中，我们介绍了NumPy的基本概念、安装方法以及如何使用NumPy进行数组操作和数据转换。我们学到了如何创建数组、进行数组运算以及常见的数组属性和方法。NumPy是进行数据分析和量化交易的基础工具之一，掌握它能帮助你更高效地处理金融数据。

![](img/75d9dd856075c6c501a4d96f998cf15e_63.png)

在接下来的课程中，我们将继续深入探讨如何利用NumPy进行更复杂的数据分析与量化交易模型的构建。