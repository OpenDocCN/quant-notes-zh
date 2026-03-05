# Python金融量化：P5：04 NumPy数组常用属性 🔍

![](img/7eab3cefa36bfabdd0965015cf7f86d6_1.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_3.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_5.png)

在本节课中，我们将学习NumPy数组的几个常用属性。这些属性可以帮助我们快速了解数组的形状、维度、大小和数据类型，是进行数据操作和分析的基础。

![](img/7eab3cefa36bfabdd0965015cf7f86d6_7.png)

上一节我们介绍了NumPy数组的一些常用方法，本节中我们来看看数组的几个核心属性。

![](img/7eab3cefa36bfabdd0965015cf7f86d6_9.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_11.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_13.png)

首先，我们创建一个示例数组来演示这些属性的用法。

![](img/7eab3cefa36bfabdd0965015cf7f86d6_15.png)

```python
import numpy as np

![](img/7eab3cefa36bfabdd0965015cf7f86d6_17.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_19.png)

# 创建一个5行6列的二维数组，元素为0到100之间的随机整数
arr = np.random.randint(0, 100, size=(5, 6))
print(arr)
```

![](img/7eab3cefa36bfabdd0965015cf7f86d6_21.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_23.png)

现在，`arr` 就是一个五行六列的二维数组。创建好之后，我们来逐一查看它的属性。

![](img/7eab3cefa36bfabdd0965015cf7f86d6_25.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_27.png)

以下是NumPy数组的几个常用属性：

![](img/7eab3cefa36bfabdd0965015cf7f86d6_29.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_31.png)

*   **`shape`**：返回数组的形状，即每个维度的大小。
    ```python
    print(arr.shape)  # 输出: (5, 6)
    ```
    这表示数组是一个5行6列的二维结构。

![](img/7eab3cefa36bfabdd0965015cf7f86d6_33.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_35.png)

*   **`ndim`**：返回数组的维度（阶）。
    ```python
    print(arr.ndim)   # 输出: 2
    ```
    对于二维数组，`ndim` 返回2。

![](img/7eab3cefa36bfabdd0965015cf7f86d6_37.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_39.png)

*   **`size`**：返回数组元素的总个数。
    ```python
    print(arr.size)   # 输出: 30
    ```
    计算方式是 5行 * 6列 = 30个元素。

*   **`dtype`**：返回数组元素的数据类型。
    ```python
    print(arr.dtype)  # 输出: int64
    ```
    这表示数组中的每个元素都是64位整数类型。

![](img/7eab3cefa36bfabdd0965015cf7f86d6_41.png)

除了查看，我们还可以操作数组的数据类型。接下来，我们重点讨论一下NumPy数组的数据类型。

首先，创建一个普通数组并查看其类型。
```python
arr1 = np.array([1, 2, 3])
print(arr1.dtype)  # 输出: int64
```

我们可以在创建数组时直接指定元素的数据类型。
```python
# 创建数组时指定数据类型为int32
arr2 = np.array([1, 2, 3], dtype='int32')
print(arr2.dtype)  # 输出: int32
```

![](img/7eab3cefa36bfabdd0965015cf7f86d6_43.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_45.png)

此外，我们还可以修改已有数组的数据类型。
```python
# 修改数组arr2的数据类型为无符号8位整数
arr2 = arr2.astype('uint8')
print(arr2.dtype)  # 输出: uint8
```
**注意**：更安全的做法是使用 `.astype()` 方法创建数据类型的副本，而不是直接对 `dtype` 属性赋值，以避免潜在的数据截断或溢出问题。

那么，为什么需要关心和修改数据类型呢？主要是为了**优化内存使用**。例如，一个包含500万个元素的数组，如果默认使用`int64`（每个元素占8字节），总内存占用约为38MB。如果数据范围允许，将其改为`uint8`（每个元素占1字节），内存占用将骤降至约4.8MB，这对于处理大规模数据至关重要。

![](img/7eab3cefa36bfabdd0965015cf7f86d6_47.png)

![](img/7eab3cefa36bfabdd0965015cf7f86d6_49.png)

本节课中我们一起学习了NumPy数组的四个核心属性：`shape`（形状）、`ndim`（维度）、`size`（大小）和`dtype`（数据类型）。理解并熟练使用这些属性，是高效进行数组操作和内存管理的基础。我们还了解了如何指定和转换数据类型以优化程序性能。