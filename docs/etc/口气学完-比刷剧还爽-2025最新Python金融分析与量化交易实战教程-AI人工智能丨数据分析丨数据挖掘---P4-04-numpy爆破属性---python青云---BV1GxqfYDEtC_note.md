# Python金融量化：P4：04 NumPy数组属性详解 🧮

![](img/115eb4c8199606fe1896f5a7b7f48cd6_1.png)

![](img/115eb4c8199606fe1896f5a7b7f48cd6_3.png)

在本节课中，我们将要学习NumPy数组的几个核心属性。上一节我们介绍了NumPy数组的一些常用方法，本节中我们来看看如何获取和修改数组的元数据信息，例如形状、维度和数据类型。

![](img/115eb4c8199606fe1896f5a7b7f48cd6_5.png)

首先，我们创建一个示例数组来演示这些属性的用法。

![](img/115eb4c8199606fe1896f5a7b7f48cd6_7.png)

![](img/115eb4c8199606fe1896f5a7b7f48cd6_9.png)

```python
import numpy as np

![](img/115eb4c8199606fe1896f5a7b7f48cd6_11.png)

![](img/115eb4c8199606fe1896f5a7b7f48cd6_13.png)

# 创建一个5行6列的二维数组，元素为0到100之间的随机整数
arr = np.random.randint(0, 100, size=(5, 6))
print(arr)
```

![](img/115eb4c8199606fe1896f5a7b7f48cd6_15.png)

现在，数组 `arr` 已经创建好了。接下来，我们将逐一介绍NumPy数组的几个常用属性。

![](img/115eb4c8199606fe1896f5a7b7f48cd6_17.png)

![](img/115eb4c8199606fe1896f5a7b7f48cd6_19.png)

以下是NumPy数组的四个核心属性及其说明：

![](img/115eb4c8199606fe1896f5a7b7f48cd6_21.png)

![](img/115eb4c8199606fe1896f5a7b7f48cd6_23.png)

*   **shape**：返回数组的形状，即每个维度上的大小。对于二维数组，返回一个元组 `(行数, 列数)`。
    ```python
    print(arr.shape)  # 输出例如：(5, 6)
    ```
*   **ndim**：返回数组的维度（轴）的数量。
    ```python
    print(arr.ndim)   # 输出：2
    ```
*   **size**：返回数组中元素的总个数。
    ```python
    print(arr.size)   # 输出：30 (5 * 6 = 30)
    ```
*   **dtype**：返回数组中元素的数据类型。
    ```python
    print(arr.dtype)  # 输出例如：int64
    ```

![](img/115eb4c8199606fe1896f5a7b7f48cd6_25.png)

了解了这些基本属性后，我们重点探讨一下 `dtype` 属性，因为它对内存管理和性能优化至关重要。

![](img/115eb4c8199606fe1896f5a7b7f48cd6_27.png)

### 深入理解与操作数据类型

![](img/115eb4c8199606fe1896f5a7b7f48cd6_29.png)

![](img/115eb4c8199606fe1896f5a7b7f48cd6_31.png)

`dtype` 属性定义了数组中每个元素的数据类型。NumPy提供了比Python原生类型更精细的控制，允许我们指定整数、浮点数的具体位数。

![](img/115eb4c8199606fe1896f5a7b7f48cd6_33.png)

首先，查看默认创建数组的数据类型。

![](img/115eb4c8199606fe1896f5a7b7f48cd6_35.png)

![](img/115eb4c8199606fe1896f5a7b7f48cd6_37.png)

```python
arr1 = np.array([1, 2, 3])
print(arr1.dtype)  # 输出：int64
```

![](img/115eb4c8199606fe1896f5a7b7f48cd6_39.png)

默认情况下，整数数组的元素类型是 `int64`（64位有符号整数）。我们可以通过两种方式指定或修改数据类型。

**方式一：在创建数组时通过 `dtype` 参数指定。**

![](img/115eb4c8199606fe1896f5a7b7f48cd6_41.png)

```python
arr2 = np.array([1, 2, 3], dtype='int32')
print(arr2.dtype)  # 输出：int32
```

**方式二：通过给 `dtype` 属性赋值来修改现有数组的数据类型。**

```python
arr1.dtype = 'uint8'  # 修改为8位无符号整数
print(arr1.dtype)     # 输出：uint8
```

![](img/115eb4c8199606fe1896f5a7b7f48cd6_43.png)

### 为什么需要关注数据类型？

![](img/115eb4c8199606fe1896f5a7b7f48cd6_45.png)

控制数据类型的主要目的是**优化内存使用**。例如，一个包含500万个整数的数组：
*   如果使用默认的 `int64` 类型，每个元素占8字节，总内存约为 `5,000,000 * 8字节 ≈ 38 MB`。
*   如果将其修改为 `uint8` 类型，每个元素仅占1字节，总内存骤减至约 `5,000,000 * 1字节 ≈ 4.8 MB`。

在金融量化分析中，处理海量历史行情数据时，合理选择数据类型可以显著降低内存开销，提升计算效率。

![](img/115eb4c8199606fe1896f5a7b7f48cd6_47.png)

![](img/115eb4c8199606fe1896f5a7b7f48cd6_49.png)

本节课中我们一起学习了NumPy数组的四个核心属性：`shape`（形状）、`ndim`（维度）、`size`（元素总数）和 `dtype`（数据类型）。我们重点掌握了如何查看和修改 `dtype`，并理解了通过选择合适的数据类型来优化程序内存使用的重要性。这些知识是高效进行后续数值计算和数据分析的基础。