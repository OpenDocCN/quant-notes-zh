# 金融量化分析：P11：NumPy数组创建 🧮

在本节课中，我们将学习如何使用NumPy库创建各种类型的数组。这是进行金融数据分析和量化计算的基础。我们将介绍多种创建数组的函数，并解释它们的特点和适用场景。

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_1.png)

上一节我们介绍了NumPy数组（ndarray）的一些常用属性，本节中我们来看看如何创建它们。

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_3.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_5.png)

## 使用 `array` 函数创建数组

我们已经知道，可以使用 `np.array()` 方法将一个列表直接转换成一个NumPy数组。

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_7.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_9.png)

```python
import numpy as np
arr = np.array([1, 2, 3, 4, 5])
```

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_11.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_13.png)

## 创建特殊数组的函数

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_15.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_17.png)

除了 `array` 方法，NumPy还提供了一系列函数来快速创建具有特定内容的数组。

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_19.png)

以下是创建全零、全一和“空”数组的方法。

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_21.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_23.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_25.png)

*   **`np.zeros(shape, dtype=float)`**：创建指定形状的全零数组。默认数据类型为浮点型（`float64`）。
    ```python
    a = np.zeros(10) # 创建长度为10的全零数组，默认为浮点数
    a_int = np.zeros(10, dtype=int) # 创建长度为10的全零整数数组
    ```

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_27.png)

*   **`np.ones(shape, dtype=float)`**：创建指定形状的全一数组。默认数据类型同样为浮点型。
    ```python
    b = np.ones(5) # 创建长度为5的全一数组，默认为浮点数
    ```

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_29.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_31.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_33.png)

*   **`np.empty(shape, dtype=float)`**：创建指定形状的“空”数组。此函数只分配内存空间，但**不会初始化数组元素的值**，数组中的值是内存中残留的随机数据。其速度比 `zeros` 快，适用于你确定后续会覆盖所有数据的情况。
    ```python
    c = np.empty(100) # 创建长度为100的数组，内容未初始化
    ```

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_35.png)

## 创建序列数组

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_37.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_39.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_41.png)

在Python列表中，我们使用 `range` 函数生成一个整数序列。NumPy提供了功能更强大的对应函数。

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_43.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_45.png)

以下是创建序列数组的两个主要函数。

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_47.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_49.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_51.png)

*   **`np.arange(start, stop, step)`**：生成一个在给定区间内，按照指定步长均匀间隔的数组。与 `range` 类似，区间是前闭后开的 `[start, stop)`。**步长可以是小数**，这是比原生 `range` 更强大的地方。
    ```python
    seq1 = np.arange(100) # 生成 0 到 99 的整数数组
    seq2 = np.arange(0, 100, 0.5) # 生成 0 到 99.5，步长为0.5的数组
    ```

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_53.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_55.png)

*   **`np.linspace(start, stop, num)`**：在指定的起始值和结束值之间，返回**等间隔**的 `num` 个数字。区间是前闭后闭的 `[start, stop]`。第三个参数 `num` 指定的是要生成的**元素数量**，而不是步长。这在需要均匀采样时非常有用，例如绘制函数图像。
    ```python
    # 在0到10之间均匀生成100个点
    points = np.linspace(0, 10, 100)
    # 这100个点中，相邻两点间的距离是固定的：(10-0)/(100-1)
    ```

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_57.png)

## 创建单位矩阵

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_59.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_61.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_63.png)

最后，还有一个在线性代数中常用的函数。

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_65.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_67.png)

*   **`np.eye(N)`**：创建一个 N x N 的单位矩阵。单位矩阵是一个方阵，其主对角线上的元素为1，其余元素为0。在纯粹的金融数据分析中可能不常用，但在涉及线性代数运算时会用到。
    ```python
    identity_matrix = np.eye(3)
    # 输出：
    # [[1. 0. 0.]
    #  [0. 1. 0.]
    #  [0. 0. 1.]]
    ```

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_69.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_71.png)

![](img/50582ead6e5b2a0570ca1f6bbb6ef49a_73.png)

本节课中我们一起学习了多种创建NumPy数组的方法，包括从列表转换、创建特殊值数组（全零、全一、空数组）、生成数值序列（`arange`, `linspace`）以及创建单位矩阵。理解这些方法是高效处理金融数据的第一步，为后续的数据计算和分析打下坚实基础。