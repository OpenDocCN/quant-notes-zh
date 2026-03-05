# 数据分析+金融量化+数据清洗：P5：04 NumPy数组属性 📊

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_1.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_3.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_5.png)

在本节课中，我们将学习NumPy数组的几个核心属性。这些属性可以帮助我们快速了解数组的形状、维度、大小和数据类型，是进行数据操作和分析的基础。

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_7.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_9.png)

上一节我们介绍了NumPy数组的一些常用方法，本节中我们来看看数组的几个关键属性。

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_11.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_13.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_15.png)

首先，我们创建一个示例数组来演示这些属性。

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_17.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_19.png)

```python
import numpy as np
arr = np.random.randint(0, 100, size=(5, 6))
```

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_21.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_23.png)

现在，`arr` 是一个5行6列的二维数组，其元素是0到100之间随机生成的整数。

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_25.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_27.png)

以下是NumPy数组的几个常用属性：

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_29.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_31.png)

*   **`shape`**：返回数组的形状，即每个维度的大小。
    ```python
    arr.shape  # 输出: (5, 6)
    ```
*   **`ndim`**：返回数组的维度（轴）的数量。
    ```python
    arr.ndim   # 输出: 2
    ```
*   **`size`**：返回数组中元素的总个数。
    ```python
    arr.size   # 输出: 30
    ```
*   **`dtype`**：返回数组中元素的数据类型。
    ```python
    arr.dtype  # 输出: dtype('int64')
    ```

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_33.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_35.png)

接下来，我们重点探讨一下数组的数据类型 `dtype`。

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_37.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_39.png)

数据类型决定了数组中每个元素在内存中占据的空间大小和解释方式。理解并控制`dtype`对于管理内存和确保计算精度至关重要。

我们可以在创建数组时直接指定数据类型。

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_41.png)

```python
arr2 = np.array([1, 2, 3], dtype='int32')
print(arr2.dtype)  # 输出: int32
```

此外，我们也可以修改已有数组的数据类型。

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_43.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_45.png)

```python
arr2.dtype = 'uint8'
print(arr2.dtype)  # 输出: uint8
```

这里，`int32`表示每个元素占用32位内存，而`uint8`表示无符号的8位整数（即一个字节）。为什么要修改数据类型呢？假设一个数组存储了500万个整数，默认的`int64`类型会使内存占用非常大。通过将其改为`uint8`，可以显著减少内存使用，这在处理大规模数据时非常有用。

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_47.png)

![](img/3ecd8a5b5f8dfc4b0bb9566daff8781e_49.png)

本节课中我们一起学习了NumPy数组的四个核心属性：`shape`（形状）、`ndim`（维度）、`size`（大小）和`dtype`（数据类型）。掌握这些属性是理解和操作多维数组数据的第一步，也为后续进行高效的数据清洗和量化分析打下了基础。