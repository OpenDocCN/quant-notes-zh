# Python金融量化分析：P12：NumPy数组创建方法详解 🚀

在本节课中，我们将学习如何使用NumPy库创建各种类型的数组。这是进行金融数据分析和量化计算的基础，掌握高效的数组创建方法能极大提升数据处理效率。

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_1.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_3.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_5.png)

上一节我们介绍了NumPy数组（ndarray）的常用属性，本节中我们来看看如何创建它们。

## 使用 `array` 函数创建数组

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_7.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_9.png)

最基本的方法是使用 `np.array()` 函数，它可以将一个Python列表直接转换为NumPy数组。

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_11.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_13.png)

```python
import numpy as np
arr = np.array([1, 2, 3, 4, 5])
```

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_15.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_17.png)

## 创建特殊值数组

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_19.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_21.png)

除了从列表转换，NumPy还提供了一系列函数来快速创建具有特定值的数组。

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_23.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_25.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_27.png)

以下是创建全零、全一和“空”数组的方法：

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_29.png)

*   **`np.zeros(shape)`**： 创建一个指定形状的全零数组。默认数据类型为 `float64`。
    ```python
    a = np.zeros(10) # 创建长度为10的全零数组，类型为float64
    a_int = np.zeros(10, dtype=int) # 创建长度为10的全零整数数组
    ```
*   **`np.ones(shape)`**： 创建一个指定形状的全一数组。默认数据类型同样为 `float64`。
    ```python
    b = np.ones(5) # 创建长度为5的全一数组
    ```
*   **`np.empty(shape)`**： 创建一个指定形状的“空”数组。数组中的值是未初始化的内存残留值，内容随机且不可预测。
    ```python
    c = np.empty(100)
    ```
    > **注意**：`np.empty` 只分配内存空间，不进行初始化（如置零），因此速度比 `zeros` 和 `ones` 稍快。通常在你确定会立刻用新数据覆盖整个数组时使用。

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_31.png)

## 创建序列数组

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_33.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_35.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_37.png)

在数据处理中，经常需要生成一个数值序列。NumPy提供了类似Python内置 `range` 的函数。

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_39.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_41.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_43.png)

以下是创建序列数组的两种主要方法：

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_45.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_47.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_49.png)

*   **`np.arange(start, stop, step)`**： 类似于 `range`，生成一个等差序列数组。**包含起始值，不包含结束值**。步长可以是小数。
    ```python
    seq1 = np.arange(10) # 生成 [0, 1, 2, ..., 9]
    seq2 = np.arange(1, 10, 0.5) # 生成 [1., 1.5, 2., ..., 9.5]
    ```
*   **`np.linspace(start, stop, num)`**： 在指定的区间内，生成**等间隔**的 `num` 个点。**包含起始值和结束值**。第三个参数是点的数量，而非步长。
    ```python
    linear_seq = np.linspace(0, 10, 5) # 生成 [ 0., 2.5, 5., 7.5, 10.]
    ```
    > **应用场景**：`linspace` 在需要均匀采样时非常有用，例如绘制函数图像。假设要绘制 `y = x^2` 在区间 `[-10, 10]` 上的图像，可以生成足够密集的X点，再计算对应的Y值，连接起来就近似于连续曲线。
    > ```python
    > x = np.linspace(-10, 10, 10000) # 生成1万个均匀分布的点
    > y = x ** 2 # 计算对应的y值
    > # (使用matplotlib等绘图库即可画出平滑的抛物线)
    > ```

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_51.png)

## 创建特殊矩阵（了解）

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_53.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_55.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_57.png)

最后，还有一个用于线性代数的函数。

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_59.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_61.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_63.png)

*   **`np.eye(N)`**： 创建一个N×N的单位矩阵（对角线元素为1，其余为0）。在纯粹的金融数据分析中较少使用。
    ```python
    identity_matrix = np.eye(3)
    # 输出：
    # [[1., 0., 0.],
    #  [0., 1., 0.],
    #  [0., 0., 1.]]
    ```

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_65.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_67.png)

![](img/ae4e189fe8cc6c8c5becc2501c3c4330_69.png)

本节课中我们一起学习了NumPy中创建数组的多种方法：从基础的 `array()` 转换，到快速生成特定值的 `zeros()`、`ones()` 和 `empty()`，再到生成数值序列的 `arange()` 和 `linspace()`，最后简单了解了生成单位矩阵的 `eye()`。熟练掌握这些方法，是后续进行高效数组操作和金融计算的基石。