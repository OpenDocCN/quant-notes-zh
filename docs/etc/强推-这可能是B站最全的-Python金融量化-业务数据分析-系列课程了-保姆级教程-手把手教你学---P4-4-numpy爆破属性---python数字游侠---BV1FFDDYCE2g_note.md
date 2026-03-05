Python金融量化+业务数据分析：P4：NumPy数组属性详解 🔍

![](img/820efdd49b90c49697a6b9a3d6c03bae_1.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_3.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_5.png)

在本节课中，我们将要学习NumPy数组的几个核心属性。上一节我们介绍了NumPy数组的创建和基本方法，本节中我们来看看如何获取和修改数组的元信息，例如形状、维度和数据类型。

![](img/820efdd49b90c49697a6b9a3d6c03bae_7.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_9.png)

首先，我们创建一个示例数组来演示这些属性的用法。

![](img/820efdd49b90c49697a6b9a3d6c03bae_11.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_13.png)

```python
import numpy as np
arr = np.random.randint(0, 100, size=(5, 6))
```
以上代码创建了一个5行6列的二维数组`arr`，其元素是0到100之间的随机整数。

![](img/820efdd49b90c49697a6b9a3d6c03bae_15.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_17.png)

创建好数组后，我们可以查看其各项属性。以下是NumPy数组的几个常用属性及其说明：

![](img/820efdd49b90c49697a6b9a3d6c03bae_19.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_21.png)

*   **`shape`**：返回数组的形状，即每个维度的大小。对于二维数组，返回值为`(行数, 列数)`。
    ```python
    arr.shape  # 输出: (5, 6)
    ```

![](img/820efdd49b90c49697a6b9a3d6c03bae_23.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_25.png)

*   **`ndim`**：返回数组的维度（轴）的数量。
    ```python
    arr.ndim  # 输出: 2
    ```

![](img/820efdd49b90c49697a6b9a3d6c03bae_27.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_29.png)

*   **`size`**：返回数组中元素的总个数。
    ```python
    arr.size  # 输出: 30
    ```

![](img/820efdd49b90c49697a6b9a3d6c03bae_31.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_33.png)

*   **`dtype`**：返回数组中元素的数据类型。
    ```python
    arr.dtype  # 输出: dtype('int64')
    ```

![](img/820efdd49b90c49697a6b9a3d6c03bae_35.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_37.png)

需要注意的是，`type(arr)`返回的是数组对象本身的类型`numpy.ndarray`，而`arr.dtype`返回的是数组中存储的元素的数据类型。

![](img/820efdd49b90c49697a6b9a3d6c03bae_39.png)

接下来，我们重点探讨一下数组的数据类型`dtype`。理解并控制数据类型对于管理内存和计算精度至关重要。

我们可以在创建数组时直接指定其数据类型。

![](img/820efdd49b90c49697a6b9a3d6c03bae_41.png)

```python
arr2 = np.array([1, 2, 3], dtype='int32')
arr2.dtype  # 输出: dtype('int32')
```

此外，我们还可以在数组创建后，通过给`dtype`属性赋值来修改其数据类型（注意：这可能会改变数据的表示方式）。

![](img/820efdd49b90c49697a6b9a3d6c03bae_43.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_45.png)

```python
arr2.dtype = 'uint8'
arr2.dtype  # 输出: dtype('uint8')
```

`int32`、`uint8`等标识了每个数组元素在内存中占用的位数。例如，默认的`int64`表示每个元素占用64位（8字节）内存。如果数组存储了海量数据（如500万个元素），使用`int64`将占用约40MB内存。若将数据类型改为`uint8`（每个元素1字节），内存占用将大幅缩减至约5MB。这在处理大规模数据集时，是优化程序性能的有效手段。

![](img/820efdd49b90c49697a6b9a3d6c03bae_47.png)

![](img/820efdd49b90c49697a6b9a3d6c03bae_49.png)

本节课中我们一起学习了NumPy数组的四个核心属性：`shape`（形状）、`ndim`（维度）、`size`（元素总数）和`dtype`（数据类型）。我们不仅学会了如何查看这些属性，还掌握了在创建时指定或创建后修改数据类型`dtype`的方法，这对于控制内存使用和适应不同计算需求非常重要。