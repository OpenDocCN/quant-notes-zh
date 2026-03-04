# Python金融量化：P11：NumPy数组创建 🚀

在本节课中，我们将学习如何使用NumPy创建各种类型的数组。这是进行金融数据分析和计算的基础。

![](img/42dcc7242a3ef2ccfb5864b027450f83_1.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_3.png)

上一节我们介绍了NumPy数组的常用属性，本节中我们来看看如何创建数组。

![](img/42dcc7242a3ef2ccfb5864b027450f83_5.png)

## 创建特定内容的数组

![](img/42dcc7242a3ef2ccfb5864b027450f83_7.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_9.png)

除了使用`np.array()`将列表转换为数组外，NumPy还提供了多种便捷的函数来创建具有特定内容的数组。

![](img/42dcc7242a3ef2ccfb5864b027450f83_11.png)

以下是几种创建特定内容数组的方法：

![](img/42dcc7242a3ef2ccfb5864b027450f83_13.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_15.png)

*   **创建全零数组**：使用`np.zeros()`函数。默认创建的是浮点数类型（`float64`）的数组。
    ```python
    import numpy as np
    a = np.zeros(10) # 创建长度为10的全零数组，类型为float64
    a_int = np.zeros(10, dtype=int) # 创建整数类型的全零数组
    ```

![](img/42dcc7242a3ef2ccfb5864b027450f83_17.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_19.png)

*   **创建全一数组**：使用`np.ones()`函数。同样，默认类型是浮点数。
    ```python
    b = np.ones(10) # 创建长度为10的全一数组
    b_int = np.ones(10, dtype=int) # 创建整数类型的全一数组
    ```

![](img/42dcc7242a3ef2ccfb5864b027450f83_21.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_23.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_25.png)

*   **创建“空”数组**：使用`np.empty()`函数。这个函数只分配内存空间，但**不会初始化内存中的值**。因此，数组中的值是内存中残留的、未定义的随机值。它的优点是比`np.zeros()`少一步初始化操作，速度稍快，但前提是你后续会覆盖掉所有值。
    ```python
    c = np.empty(100) # 创建一个长度为100的“空”数组，内容随机
    ```

![](img/42dcc7242a3ef2ccfb5864b027450f83_27.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_29.png)

## 创建数值序列数组

![](img/42dcc7242a3ef2ccfb5864b027450f83_31.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_33.png)

在数据处理中，经常需要生成一个数值序列。NumPy为此提供了强大的工具。

![](img/42dcc7242a3ef2ccfb5864b027450f83_35.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_37.png)

以下是创建数值序列数组的方法：

![](img/42dcc7242a3ef2ccfb5864b027450f83_39.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_41.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_43.png)

*   **使用`np.arange()`**：功能类似于Python内置的`range()`，但可以生成数组，并且步长可以是小数。
    ```python
    seq1 = np.arange(100) # 生成0到99的整数数组
    seq2 = np.arange(5, 10) # 生成5到9的整数数组
    seq3 = np.arange(0, 1, 0.1) # 生成0到1之间，步长为0.1的数组
    ```

![](img/42dcc7242a3ef2ccfb5864b027450f83_45.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_47.png)

*   **使用`np.linspace()`**：用于创建**线性空间**数组。与`arange`指定步长不同，`linspace`指定的是要生成的**元素总数量**。它生成的数组是**前闭后闭**区间 `[start, stop]`。
    ```python
    lin1 = np.linspace(0, 100, 101) # 将0到100均匀分成101份，生成101个元素的数组
    lin2 = np.linspace(-10, 10, 10000) # 将-10到10均匀分成10000份
    ```
    这个函数在科学计算中非常有用，例如绘制函数图像时，需要在一个区间内取大量均匀分布的点。
    ```python
    # 示例：生成绘制y=x^2图像的数据点
    x = np.linspace(-10, 10, 10000)
    y = x ** 2 # 计算对应的y值
    # (后续可以使用matplotlib等库绘制x和y的图像)
    ```

![](img/42dcc7242a3ef2ccfb5864b027450f83_49.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_51.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_53.png)

## 创建特殊矩阵

![](img/42dcc7242a3ef2ccfb5864b027450f83_55.png)

对于线性代数运算，NumPy也提供了创建特殊矩阵的函数。

![](img/42dcc7242a3ef2ccfb5864b027450f83_57.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_59.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_61.png)

以下是创建特殊矩阵的方法：

![](img/42dcc7242a3ef2ccfb5864b027450f83_63.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_65.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_67.png)

*   **创建单位矩阵**：使用`np.eye()`函数。单位矩阵是一个方阵，其主对角线上的元素为1，其余元素为0。
    ```python
    identity_matrix = np.eye(5) # 创建一个5x5的单位矩阵
    ```

![](img/42dcc7242a3ef2ccfb5864b027450f83_69.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_71.png)

![](img/42dcc7242a3ef2ccfb5864b027450f83_73.png)

本节课中我们一起学习了多种创建NumPy数组的方法，包括创建全零、全一数组，生成数值序列，以及创建单位矩阵。掌握这些方法是高效进行数组操作和数值计算的第一步。下一节，我们将学习如何对数组进行索引和切片，以获取我们需要的部分数据。