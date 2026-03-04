# 金融量化分析：P11：NumPy数组创建 🏗️

在本节课中，我们将学习如何使用NumPy库创建各种类型的数组。这是进行金融数据分析和计算的基础。

![](img/aefe1b6832a2e620e60ea52b1da199b0_1.png)

上一节我们介绍了NumPy数组的常用属性，本节中我们来看看如何创建它们。

![](img/aefe1b6832a2e620e60ea52b1da199b0_3.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_5.png)

## 从列表创建数组

最基本的方法是使用 `np.array()` 函数，它可以将一个Python列表直接转换为NumPy数组。

![](img/aefe1b6832a2e620e60ea52b1da199b0_7.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_9.png)

```python
import numpy as np
arr = np.array([1, 2, 3, 4, 5])
```

![](img/aefe1b6832a2e620e60ea52b1da199b0_11.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_13.png)

## 创建特殊数组

![](img/aefe1b6832a2e620e60ea52b1da199b0_15.png)

除了从列表转换，NumPy还提供了一系列函数来快速创建具有特定内容的数组。

![](img/aefe1b6832a2e620e60ea52b1da199b0_17.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_19.png)

以下是创建全零数组、全一数组和“空”数组的方法。

![](img/aefe1b6832a2e620e60ea52b1da199b0_21.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_23.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_25.png)

*   **全零数组**：使用 `np.zeros(shape)`。默认创建浮点数（`float64`）类型的数组。
    ```python
    # 创建一个长度为10的全零数组
    a_zeros = np.zeros(10)
    # 指定数据类型为整数
    a_zeros_int = np.zeros(10, dtype=int)
    ```

![](img/aefe1b6832a2e620e60ea52b1da199b0_27.png)

*   **全一数组**：使用 `np.ones(shape)`。同样默认创建浮点数数组。
    ```python
    # 创建一个长度为5的全一数组
    a_ones = np.ones(5)
    ```

![](img/aefe1b6832a2e620e60ea52b1da199b0_29.png)

*   **空数组**：使用 `np.empty(shape)`。这个函数只分配内存空间，**不会初始化数组元素的值**，因此数组内容将是内存中的随机残留值。
    ```python
    # 创建一个长度为100的“空”数组
    a_empty = np.empty(100)
    ```
    **注意**：`np.empty` 的速度比 `np.zeros` 快，因为它跳过了初始化为零的步骤。但你必须确保之后会覆盖数组中的所有值，否则程序可能因为使用未初始化的随机值而出错。

![](img/aefe1b6832a2e620e60ea52b1da199b0_31.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_33.png)

## 创建序列数组

![](img/aefe1b6832a2e620e60ea52b1da199b0_35.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_37.png)

在Python中，我们使用 `range()` 生成整数序列。NumPy提供了功能更强大的对应函数。

![](img/aefe1b6832a2e620e60ea52b1da199b0_39.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_41.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_43.png)

以下是创建数值序列的两种主要方法。

![](img/aefe1b6832a2e620e60ea52b1da199b0_45.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_47.png)

*   **`np.arange`**：类似于 `range()`，但功能更强，可以生成浮点数序列。
    ```python
    # 生成 0 到 99 的整数序列
    seq1 = np.arange(100)
    # 生成 5 到 15，步长为2的序列
    seq2 = np.arange(5, 15, 2)
    # 生成 0 到 1，步长为0.1的浮点数序列
    seq3 = np.arange(0, 1, 0.1)
    ```

![](img/aefe1b6832a2e620e60ea52b1da199b0_49.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_51.png)

*   **`np.linspace`**：在指定的起始值和结束值之间，**生成指定数量的等间隔数值**。它包含结束值。
    ```python
    # 在 0 到 100 之间生成 101 个等间隔的数（包含0和100）
    lin_seq1 = np.linspace(0, 100, 101)
    # 在 -10 到 10 之间生成 10000 个点，用于绘制平滑的函数图像
    x = np.linspace(-10, 10, 10000)
    y = x ** 2  # 计算 y = x^2
    ```
    `np.linspace` 在需要均匀采样或绘制函数图像时非常有用，因为它能精确控制生成点的数量。

![](img/aefe1b6832a2e620e60ea52b1da199b0_53.png)

## 创建单位矩阵

![](img/aefe1b6832a2e620e60ea52b1da199b0_55.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_57.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_59.png)

最后，还有一个用于线性代数的特殊创建函数。

![](img/aefe1b6832a2e620e60ea52b1da199b0_61.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_63.png)

*   **`np.eye`**：创建单位矩阵（对角线为1，其余为0的方阵）。
    ```python
    # 创建一个 3x3 的单位矩阵
    identity_matrix = np.eye(3)
    ```
    如果不进行线性代数运算，这个函数使用频率较低。

![](img/aefe1b6832a2e620e60ea52b1da199b0_65.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_67.png)

![](img/aefe1b6832a2e620e60ea52b1da199b0_69.png)

本节课中我们一起学习了多种创建NumPy数组的方法，包括从列表转换、创建特殊值数组（零、一、空）、生成数值序列（`arange`, `linspace`）以及创建单位矩阵。掌握这些方法是高效处理金融数据的第一步。下一节，我们将学习如何操作和计算这些数组。