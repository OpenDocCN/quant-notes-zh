Python三剑客：P5：01 numpy剑客修炼_04 numpy数组属性 🔍

![](img/df5957b190de0175508638f6da8fa344_1.png)

![](img/df5957b190de0175508638f6da8fa344_3.png)

![](img/df5957b190de0175508638f6da8fa344_5.png)

在本节课中，我们将要学习NumPy数组的几个核心属性。上一节我们介绍了NumPy数组的一些常用方法，本节中我们来看看数组本身自带的一些重要属性，它们能帮助我们快速了解数组的形状、维度、大小和数据类型。

![](img/df5957b190de0175508638f6da8fa344_7.png)

![](img/df5957b190de0175508638f6da8fa344_9.png)

首先，我们创建一个示例数组来演示这些属性。

![](img/df5957b190de0175508638f6da8fa344_11.png)

![](img/df5957b190de0175508638f6da8fa344_13.png)

![](img/df5957b190de0175508638f6da8fa344_15.png)

```python
import numpy as np
arr = np.random.randint(0, 100, size=(5, 6))
```

![](img/df5957b190de0175508638f6da8fa344_17.png)

![](img/df5957b190de0175508638f6da8fa344_19.png)

现在，`arr` 就是一个5行6列的二维数组，其元素是0到100之间随机生成的整数。

![](img/df5957b190de0175508638f6da8fa344_21.png)

![](img/df5957b190de0175508638f6da8fa344_23.png)

以下是NumPy数组的几个常用属性：

![](img/df5957b190de0175508638f6da8fa344_25.png)

![](img/df5957b190de0175508638f6da8fa344_27.png)

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

![](img/df5957b190de0175508638f6da8fa344_29.png)

![](img/df5957b190de0175508638f6da8fa344_31.png)

这就是NumPy数组的几个核心属性。接下来，我们重点探讨一下数组的数据类型 `dtype`。

![](img/df5957b190de0175508638f6da8fa344_33.png)

![](img/df5957b190de0175508638f6da8fa344_35.png)

首先，创建一个数组时，其元素类型通常是自动推断的。

![](img/df5957b190de0175508638f6da8fa344_37.png)

![](img/df5957b190de0175508638f6da8fa344_39.png)

```python
arr1 = np.array([1, 2, 3])
print(arr1.dtype)  # 输出: int64
```

我们也可以在创建数组时，通过 `dtype` 参数直接指定元素类型。

![](img/df5957b190de0175508638f6da8fa344_41.png)

```python
arr2 = np.array([1, 2, 3], dtype='int32')
print(arr2.dtype)  # 输出: int32
```

此外，我们还可以直接修改已有数组的 `dtype` 属性来改变其元素类型。

![](img/df5957b190de0175508638f6da8fa344_43.png)

![](img/df5957b190de0175508638f6da8fa344_45.png)

```python
arr2.dtype = 'uint8'
print(arr2.dtype)  # 输出: uint8
```

`int32` 表示每个数组元素占用32位内存，而 `uint8` 表示无符号的8位整数。修改数据类型的一个主要目的是**优化内存使用**。例如，一个包含500万个元素的数组，如果默认使用 `int64`，将占用大量内存。通过将其元素类型改为 `uint8`（每个元素仅占1字节），可以显著减少内存开销。

![](img/df5957b190de0175508638f6da8fa344_47.png)

![](img/df5957b190de0175508638f6da8fa344_49.png)

本节课中我们一起学习了NumPy数组的四个核心属性：`shape`（形状）、`ndim`（维度）、`size`（大小）和 `dtype`（数据类型），并重点了解了如何查看和修改数组的数据类型以优化程序性能。