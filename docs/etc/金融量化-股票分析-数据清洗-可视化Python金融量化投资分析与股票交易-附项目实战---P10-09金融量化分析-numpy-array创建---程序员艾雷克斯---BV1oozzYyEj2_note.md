# Python金融量化投资分析与股票交易：P10：09金融量化分析-numpy-array创建 📊

在本节课中，我们将学习如何使用NumPy库创建数组。数组是NumPy的核心数据结构，是进行金融数据分析和量化计算的基础。我们将介绍多种创建数组的方法，包括从列表转换、创建特殊数组以及生成数值序列。

![](img/0d1b8da91ab7628121be24b830571ee0_1.png)

![](img/0d1b8da91ab7628121be24b830571ee0_3.png)

![](img/0d1b8da91ab7628121be24b830571ee0_5.png)

上一节我们介绍了ndarray的常用属性，本节中我们来看看如何创建数组。

## 从列表创建数组

![](img/0d1b8da91ab7628121be24b830571ee0_7.png)

![](img/0d1b8da91ab7628121be24b830571ee0_9.png)

最基本的方法是使用`np.array()`函数，它可以将一个Python列表直接转换为NumPy数组。

![](img/0d1b8da91ab7628121be24b830571ee0_11.png)

![](img/0d1b8da91ab7628121be24b830571ee0_13.png)

```python
import numpy as np
list_data = [1, 2, 3, 4, 5]
arr = np.array(list_data)
```

![](img/0d1b8da91ab7628121be24b830571ee0_15.png)

![](img/0d1b8da91ab7628121be24b830571ee0_17.png)

## 创建特殊数组

![](img/0d1b8da91ab7628121be24b830571ee0_19.png)

![](img/0d1b8da91ab7628121be24b830571ee0_21.png)

除了从列表转换，NumPy还提供了一系列函数来快速创建具有特定内容的数组。

![](img/0d1b8da91ab7628121be24b830571ee0_23.png)

![](img/0d1b8da91ab7628121be24b830571ee0_25.png)

![](img/0d1b8da91ab7628121be24b830571ee0_27.png)

以下是创建全零数组、全一数组和空数组的方法。

![](img/0d1b8da91ab7628121be24b830571ee0_29.png)

![](img/0d1b8da91ab7628121be24b830571ee0_31.png)

![](img/0d1b8da91ab7628121be24b830571ee0_33.png)

*   **全零数组**：使用`np.zeros()`函数。默认创建的数据类型是浮点数（`float64`）。
    ```python
    arr_zeros = np.zeros(10) # 创建长度为10的全零数组，类型为float64
    arr_zeros_int = np.zeros(10, dtype=int) # 创建长度为10的全零整数数组
    ```
*   **全一数组**：使用`np.ones()`函数。同样，默认数据类型是浮点数。
    ```python
    arr_ones = np.ones(10) # 创建长度为10的全一数组，类型为float64
    arr_ones_int = np.ones(10, dtype=int) # 创建长度为10的全一整数数组
    ```
*   **空数组**：使用`np.empty()`函数。此函数只分配内存空间，但不初始化数组元素，因此数组中的值是内存中的随机残留值。其速度比`np.zeros()`快，但使用时需确保后续会覆盖所有值。
    ```python
    arr_empty = np.empty(100) # 创建长度为100的空数组，值为内存随机值
    ```

![](img/0d1b8da91ab7628121be24b830571ee0_35.png)

## 创建数值序列数组

![](img/0d1b8da91ab7628121be24b830571ee0_37.png)

![](img/0d1b8da91ab7628121be24b830571ee0_39.png)

![](img/0d1b8da91ab7628121be24b830571ee0_41.png)

在数据处理中，经常需要生成一个范围内的数值序列。NumPy为此提供了两个重要的函数。

![](img/0d1b8da91ab7628121be24b830571ee0_43.png)

![](img/0d1b8da91ab7628121be24b830571ee0_45.png)

![](img/0d1b8da91ab7628121be24b830571ee0_47.png)

以下是使用`np.arange()`和`np.linspace()`创建序列的方法。

![](img/0d1b8da91ab7628121be24b830571ee0_49.png)

![](img/0d1b8da91ab7628121be24b830571ee0_51.png)

![](img/0d1b8da91ab7628121be24b830571ee0_53.png)

*   **`np.arange()`**：功能类似于Python内置的`range()`，但返回的是数组。它接受起始值、结束值和步长参数，遵循“前闭后开”原则。
    ```python
    arr_arange1 = np.arange(100) # 生成0到99的整数数组
    arr_arange2 = np.arange(5, 16, 2) # 生成5到15，步长为2的数组：[5, 7, 9, 11, 13, 15]
    arr_arange3 = np.arange(0, 1, 0.1) # 步长可以是小数
    ```
*   **`np.linspace()`**：用于创建等差数列。它接受起始值、结束值和元素数量参数。与`arange`不同，它生成的数组包含结束值，并且元素间隔均匀。
    ```python
    arr_linspace = np.linspace(0, 10, 5) # 在0到10之间生成5个等间隔的数：[0., 2.5, 5., 7.5, 10.]
    ```
    这个函数在需要均匀采样时非常有用，例如绘制函数图像。假设要绘制函数 **`y = x²`** 在区间[-10, 10]上的图像，可以生成足够密集的X点，再计算对应的Y值。
    ```python
    x = np.linspace(-10, 10, 10000) # 生成10000个点
    y = x ** 2 # 计算y值
    # (使用matplotlib等绘图库即可画出平滑曲线)
    ```

![](img/0d1b8da91ab7628121be24b830571ee0_55.png)

## 创建单位矩阵

![](img/0d1b8da91ab7628121be24b830571ee0_57.png)

![](img/0d1b8da91ab7628121be24b830571ee0_59.png)

![](img/0d1b8da91ab7628121be24b830571ee0_61.png)

最后，NumPy还提供了创建单位矩阵的函数`np.eye()`。单位矩阵是线性代数中的概念，其主对角线元素为1，其余元素为0。在金融量化中，如果不涉及复杂的线性代数运算，此函数使用频率较低。

![](img/0d1b8da91ab7628121be24b830571ee0_63.png)

![](img/0d1b8da91ab7628121be24b830571ee0_65.png)

![](img/0d1b8da91ab7628121be24b830571ee0_67.png)

```python
identity_matrix = np.eye(3) # 创建3x3的单位矩阵
# 输出：
# [[1. 0. 0.]
#  [0. 1. 0.]
#  [0. 0. 1.]]
```

![](img/0d1b8da91ab7628121be24b830571ee0_69.png)

![](img/0d1b8da91ab7628121be24b830571ee0_71.png)

![](img/0d1b8da91ab7628121be24b830571ee0_73.png)

本节课中我们一起学习了多种创建NumPy数组的方法。我们掌握了从列表转换的`np.array()`，快速创建特殊数组的`np.zeros()`、`np.ones()`和`np.empty()`，生成数值序列的`np.arange()`和`np.linspace()`，以及创建单位矩阵的`np.eye()`。理解并熟练运用这些方法是后续进行高效数组操作和金融数据分析的第一步。