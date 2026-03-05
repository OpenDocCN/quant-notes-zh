# Python金融量化投资分析：P11：NumPy数组创建 🚀

在本节课中，我们将学习如何使用NumPy库创建各种类型的数组。这是进行金融数据分析和量化计算的基础。

![](img/1f1911438242f3e5bc6468d768efd61c_1.png)

![](img/1f1911438242f3e5bc6468d768efd61c_3.png)

上一节我们介绍了NumPy数组的常用属性，本节中我们来看看如何创建数组。

![](img/1f1911438242f3e5bc6468d768efd61c_5.png)

## 创建数组的方法 📝

![](img/1f1911438242f3e5bc6468d768efd61c_7.png)

我们已经知道，可以使用`np.array()`方法将列表转换为数组。除此之外，NumPy还提供了多种便捷的函数来创建特定类型的数组。

![](img/1f1911438242f3e5bc6468d768efd61c_9.png)

以下是几种创建数组的核心方法：

![](img/1f1911438242f3e5bc6468d768efd61c_11.png)

![](img/1f1911438242f3e5bc6468d768efd61c_13.png)

*   **`np.zeros(shape, dtype=float)`**：创建指定形状的全零数组。默认数据类型为浮点数`float64`。
    ```python
    import numpy as np
    a = np.zeros(10) # 创建长度为10的全零浮点数数组
    b = np.zeros(10, dtype=int) # 创建长度为10的全零整数数组
    ```

![](img/1f1911438242f3e5bc6468d768efd61c_15.png)

![](img/1f1911438242f3e5bc6468d768efd61c_17.png)

![](img/1f1911438242f3e5bc6468d768efd61c_19.png)

*   **`np.ones(shape, dtype=float)`**：创建指定形状的全一数组。默认数据类型同样为浮点数。
    ```python
    c = np.ones(5) # 创建长度为5的全一浮点数数组
    ```

![](img/1f1911438242f3e5bc6468d768efd61c_21.png)

![](img/1f1911438242f3e5bc6468d768efd61c_23.png)

*   **`np.empty(shape, dtype=float)`**：创建指定形状的“空”数组。此函数只分配内存空间，但不初始化数组元素，因此数组中的值是内存中的残留数据（随机值）。其速度比`np.zeros()`略快，适用于后续会完全覆盖数组内容的情况。
    ```python
    d = np.empty(100) # 创建长度为100的未初始化数组
    ```

![](img/1f1911438242f3e5bc6468d768efd61c_25.png)

![](img/1f1911438242f3e5bc6468d768efd61c_27.png)

*   **`np.arange(start, stop, step)`**：创建等差序列数组。参数与Python内置的`range()`函数类似，但`step`步长可以是小数。
    ```python
    e = np.arange(10) # 创建数组 [0, 1, 2, ..., 9]
    f = np.arange(0, 10, 0.5) # 创建数组 [0., 0.5, 1., ..., 9.5]
    ```

![](img/1f1911438242f3e5bc6468d768efd61c_29.png)

![](img/1f1911438242f3e5bc6468d768efd61c_31.png)

*   **`np.linspace(start, stop, num)`**：创建在指定区间内均匀分布的数组。参数`num`指定要生成的样本数量（数组长度），区间是**前闭后闭**的 `[start, stop]`。这在需要等间隔采样（如绘制函数图像）时非常有用。
    ```python
    g = np.linspace(0, 10, 5) # 创建数组 [ 0., 2.5, 5., 7.5, 10.]
    ```

![](img/1f1911438242f3e5bc6468d768efd61c_33.png)

![](img/1f1911438242f3e5bc6468d768efd61c_35.png)

*   **`np.eye(N)`**：创建N×N的单位矩阵（对角线元素为1，其余为0）。主要用于线性代数运算。
    ```python
    h = np.eye(3) # 创建3x3的单位矩阵
    # 输出：
    # [[1., 0., 0.],
    #  [0., 1., 0.],
    #  [0., 0., 1.]]
    ```

![](img/1f1911438242f3e5bc6468d768efd61c_37.png)

![](img/1f1911438242f3e5bc6468d768efd61c_39.png)

## 应用示例：绘制函数图像 📈

![](img/1f1911438242f3e5bc6468d768efd61c_41.png)

![](img/1f1911438242f3e5bc6468d768efd61c_43.png)

![](img/1f1911438242f3e5bc6468d768efd61c_45.png)

为了帮助理解`np.linspace()`的用途，我们来看一个简单的示例：如何用离散点来近似绘制连续的函数图像。

![](img/1f1911438242f3e5bc6468d768efd61c_47.png)

![](img/1f1911438242f3e5bc6468d768efd61c_49.png)

假设我们要绘制函数 **`y = x²`** 在区间 `[-10, 10]` 上的图像。数学上的函数是连续的，但计算机只能处理有限个点。我们可以使用`np.linspace`在这个区间上生成大量密集的点，然后计算每个点对应的y值，最后将这些点连接起来，就能得到近似的函数曲线。

![](img/1f1911438242f3e5bc6468d768efd61c_51.png)

![](img/1f1911438242f3e5bc6468d768efd61c_53.png)

![](img/1f1911438242f3e5bc6468d768efd61c_55.png)

```python
import numpy as np
import matplotlib.pyplot as plt # 用于绘图的库

![](img/1f1911438242f3e5bc6468d768efd61c_57.png)

# 在-10到10之间生成1000个等间隔的点
x = np.linspace(-10, 10, 1000)
# 计算每个x对应的y值（利用了NumPy的广播机制，后续课程会讲）
y = x ** 2

![](img/1f1911438242f3e5bc6468d768efd61c_59.png)

![](img/1f1911438242f3e5bc6468d768efd61c_61.png)

# 绘制图像
plt.plot(x, y)
plt.title('y = x²')
plt.xlabel('x')
plt.ylabel('y')
plt.grid(True)
plt.show()
```

![](img/1f1911438242f3e5bc6468d768efd61c_63.png)

![](img/1f1911438242f3e5bc6468d768efd61c_65.png)

![](img/1f1911438242f3e5bc6468d768efd61c_67.png)

通过这个例子，我们可以看到`np.linspace`在生成用于计算和可视化的数据点时的强大作用。

![](img/1f1911438242f3e5bc6468d768efd61c_69.png)

![](img/1f1911438242f3e5bc6468d768efd61c_71.png)

![](img/1f1911438242f3e5bc6468d768efd61c_73.png)

本节课中我们一起学习了使用NumPy创建数组的多种方法，包括全零/全一数组、空数组、等差序列、均匀分布序列和单位矩阵。理解并掌握这些数组创建函数，是后续进行高效数值计算和数据分析的重要基石。