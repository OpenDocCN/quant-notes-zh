# Python量化交易：P11：11 金融量化分析-numpy-array创建 🧮

在本节课中，我们将学习如何使用NumPy创建各种类型的数组。上一节我们介绍了ndarray的常用属性，本节中我们来看看如何从零开始生成数组。

## 概述

NumPy提供了多种创建数组的函数，除了基础的`array()`方法，还有用于生成特定数值（如全零、全一）或特定序列（如等差数列）的便捷函数。理解这些创建方法对于后续的数据处理和计算至关重要。

## 创建特定数值的数组

![](img/fac10b648b60f49dde7796cc61b26475_1.png)

![](img/fac10b648b60f49dde7796cc61b26475_3.png)

![](img/fac10b648b60f49dde7796cc61b26475_5.png)

以下是几种创建填充了特定数值的数组的方法。

### 创建全零数组

使用`np.zeros()`函数可以创建一个指定形状且所有元素均为0的数组。默认情况下，数组的数据类型是浮点型(`float64`)。

![](img/fac10b648b60f49dde7796cc61b26475_7.png)

```python
import numpy as np
a = np.zeros(10)
print(a.dtype)  # 输出：float64
```

![](img/fac10b648b60f49dde7796cc61b26475_9.png)

如果想创建整数类型的全零数组，可以通过`dtype`参数指定。

```python
a_int = np.zeros(10, dtype=int)
print(a_int.dtype)  # 输出：int64
```

![](img/fac10b648b60f49dde7796cc61b26475_11.png)

### 创建全一数组

![](img/fac10b648b60f49dde7796cc61b26475_13.png)

与`zeros()`类似，`np.ones()`函数用于创建所有元素为1的数组。

![](img/fac10b648b60f49dde7796cc61b26475_15.png)

![](img/fac10b648b60f49dde7796cc61b26475_17.png)

```python
b = np.ones(5)
print(b)  # 输出：[1. 1. 1. 1. 1.]
```

![](img/fac10b648b60f49dde7796cc61b26475_19.png)

同样，可以通过`dtype`参数指定数据类型。

![](img/fac10b648b60f49dde7796cc61b26475_21.png)

### 创建“空”数组

![](img/fac10b648b60f49dde7796cc61b26475_23.png)

`np.empty()`函数会分配指定形状的内存空间，但**不会初始化内存中的值**。这意味着数组中的元素值是内存中残留的、未定义的随机值。

![](img/fac10b648b60f49dde7796cc61b26475_25.png)

![](img/fac10b648b60f49dde7796cc61b26475_27.png)

```python
c = np.empty(10)
print(c)
```

**为什么需要`empty`函数？**
创建数组需要两步：1. 向计算机申请内存空间；2. 将内存空间初始化为特定值（如0）。`zeros`和`ones`会执行这两步，而`empty`只执行第一步（申请内存），因此速度更快。当你确定后续操作会覆盖数组中的所有值时，使用`empty`可以提高效率。如果不确定，则使用`zeros`或`ones`更安全。

![](img/fac10b648b60f49dde7796cc61b26475_29.png)

![](img/fac10b648b60f49dde7796cc61b26475_31.png)

## 创建序列数组

![](img/fac10b648b60f49dde7796cc61b26475_33.png)

以下是创建具有规律性序列的数组的方法。

![](img/fac10b648b60f49dde7796cc61b26475_35.png)

### 使用`arange`创建等差数列

![](img/fac10b648b60f49dde7796cc61b26475_37.png)

`np.arange()`函数类似于Python内置的`range()`，但功能更强大，可以生成数组。它接受起始值、结束值（不包含）和步长。

![](img/fac10b648b60f49dde7796cc61b26475_39.png)

```python
# 生成 0 到 99 的整数序列
seq1 = np.arange(100)
# 生成 5 到 15，步长为2的序列
seq2 = np.arange(5, 15, 2)
# arange 支持浮点数步长
seq3 = np.arange(0, 1, 0.1)
```

![](img/fac10b648b60f49dde7796cc61b26475_41.png)

### 使用`linspace`创建线性间隔数组

![](img/fac10b648b60f49dde7796cc61b26475_43.png)

![](img/fac10b648b60f49dde7796cc61b26475_45.png)

`np.linspace()`函数用于在指定的区间内，生成**指定数量**的等间隔点。它与`arange`的关键区别在于：`linspace`的第三个参数是点的**数量**，而不是步长；并且默认包含结束点。

![](img/fac10b648b60f49dde7796cc61b26475_47.png)

```python
# 在 0 到 100 之间生成 101 个等间隔的点（包含0和100）
points = np.linspace(0, 100, 101)
```

![](img/fac10b648b60f49dde7796cc61b26475_49.png)

**`linspace`的应用场景：**
它在科学计算中非常有用，特别是在需要均匀采样以绘制函数图像时。例如，要绘制函数 y = x² 在区间[-10, 10]上的图像，可以生成大量密集的x点，然后计算对应的y值。

![](img/fac10b648b60f49dde7796cc61b26475_51.png)

![](img/fac10b648b60f49dde7796cc61b26475_53.png)

```python
x = np.linspace(-10, 10, 10000)  # 生成1万个点
y = x ** 2  # 计算y值
# 之后可以使用matplotlib等库将(x, y)点连接起来绘制平滑曲线
```

![](img/fac10b648b60f49dde7796cc61b26475_55.png)

## 创建特殊矩阵

最后介绍一个用于线性代数的特殊创建函数。

![](img/fac10b648b60f49dde7796cc61b26475_57.png)

### 创建单位矩阵

![](img/fac10b648b60f49dde7796cc61b26475_59.png)

![](img/fac10b648b60f49dde7796cc61b26475_61.png)

`np.eye(N)`函数用于创建一个N×N的单位矩阵（即主对角线元素为1，其余元素为0的方阵）。这在涉及矩阵运算的线性代数问题中会用到。

![](img/fac10b648b60f49dde7796cc61b26475_63.png)

![](img/fac10b648b60f49dde7796cc61b26475_65.png)

```python
identity_matrix = np.eye(3)
print(identity_matrix)
# 输出：
# [[1. 0. 0.]
#  [0. 1. 0.]
#  [0. 0. 1.]]
```

![](img/fac10b648b60f49dde7796cc61b26475_67.png)

## 总结

![](img/fac10b648b60f49dde7796cc61b26475_69.png)

本节课中我们一起学习了多种创建NumPy数组的方法：
1.  **`np.zeros()` / `np.ones()`**：创建填充特定常数的数组。
2.  **`np.empty()`**：快速分配内存空间，但不初始化值，适用于后续会完全覆盖的场景。
3.  **`np.arange()`**：创建具有固定步长的等差数列，功能比`range()`更强。
4.  **`np.linspace()`**：在指定区间内创建固定数量的等间隔点，常用于函数绘图和均匀采样。
5.  **`np.eye()`**：创建单位矩阵，用于线性代数运算。

![](img/fac10b648b60f49dde7796cc61b26475_71.png)

![](img/fac10b648b60f49dde7796cc61b26475_73.png)

掌握这些数组创建方法是进行高效数值计算和数据分析的基础。下一节，我们将学习如何对已创建的数组进行索引和切片操作。