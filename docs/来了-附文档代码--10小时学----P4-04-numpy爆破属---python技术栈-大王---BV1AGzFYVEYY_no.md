# Python金融量化：P4：04 NumPy数组属性详解 🧮

![](img/1dcd53f072439c8d5b2bd1bf30706f10_1.png)

在本节课中，我们将要学习NumPy数组的几个核心属性。上一节我们介绍了NumPy数组的创建和基本方法，本节中我们来看看如何获取和修改数组的元信息，例如形状、维度和数据类型。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_3.png)

## 创建示例数组

![](img/1dcd53f072439c8d5b2bd1bf30706f10_5.png)

首先，我们创建一个示例数组，以便后续演示属性的使用。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_7.png)

```python
import numpy as np

![](img/1dcd53f072439c8d5b2bd1bf30706f10_9.png)

# 创建一个5行6列的二维数组，元素为0到100之间的随机整数
arr = np.random.randint(0, 100, size=(5, 6))
print(arr)
```

![](img/1dcd53f072439c8d5b2bd1bf30706f10_11.png)

## 常用数组属性

![](img/1dcd53f072439c8d5b2bd1bf30706f10_13.png)

以下是NumPy数组的几个常用属性，它们可以帮助我们快速了解数组的结构和特征。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_15.png)

### 1. shape属性

![](img/1dcd53f072439c8d5b2bd1bf30706f10_17.png)

`shape`属性返回数组的形状，即每个维度的大小。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_19.png)

```python
print(arr.shape)  # 输出: (5, 6)
```
这表示数组`arr`是一个5行6列的二维数组。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_21.png)

### 2. ndim属性

![](img/1dcd53f072439c8d5b2bd1bf30706f10_23.png)

`ndim`属性返回数组的维度（阶数）。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_25.png)

```python
print(arr.ndim)   # 输出: 2
```
这表示数组`arr`是一个二维数组。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_27.png)

### 3. size属性

![](img/1dcd53f072439c8d5b2bd1bf30706f10_29.png)

`size`属性返回数组中元素的总个数。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_31.png)

```python
print(arr.size)   # 输出: 30
```
对于一个5行6列的数组，元素总数为 `5 * 6 = 30`。

### 4. dtype属性

![](img/1dcd53f072439c8d5b2bd1bf30706f10_33.png)

![](img/1dcd53f072439c8d5b2bd1bf30706f10_35.png)

`dtype`属性返回数组中元素的数据类型。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_37.png)

```python
print(arr.dtype)  # 输出: int64 (或其他整数类型)
```
这表示数组`arr`中的元素是64位整数。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_39.png)

## 理解与修改数据类型

上一节我们介绍了如何查看数据类型，本节中我们来看看如何指定和修改它。理解数据类型对于控制内存使用和计算精度至关重要。

### 创建时指定数据类型

在创建数组时，可以通过`dtype`参数直接指定元素的数据类型。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_41.png)

```python
# 创建一个数组，并指定元素类型为int32
arr_int32 = np.array([1, 2, 3], dtype='int32')
print(arr_int32.dtype)  # 输出: int32
```

### 修改现有数组的数据类型

可以通过为`dtype`属性赋值来修改现有数组的数据类型。NumPy会创建一个具有新数据类型的新数组（视图或副本）。

```python
# 修改数组的数据类型为无符号8位整数
arr_uint8 = arr.astype('uint8')
print(arr_uint8.dtype)  # 输出: uint8
```
**注意**：直接赋值`arr.dtype = ‘uint8’`在某些情况下可能导致数据被重新解释，通常更安全的做法是使用`.astype()`方法。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_43.png)

### 数据类型的重要性

![](img/1dcd53f072439c8d5b2bd1bf30706f10_45.png)

选择合适的数据类型可以显著影响程序性能。例如，一个包含500万个元素的数组：
*   默认`int64`类型：每个元素占8字节，总内存约 `5,000,000 * 8 bytes ≈ 38 MB`。
*   使用`uint8`类型：每个元素占1字节，总内存约 `5,000,000 * 1 bytes ≈ 4.8 MB`。

在数据量巨大时，合理选择数据类型可以大大减少内存占用。

## 数组类型 vs 元素类型

![](img/1dcd53f072439c8d5b2bd1bf30706f10_47.png)

需要区分数组本身的类型和数组元素的类型：
*   **数组类型**：使用`type()`函数查看，NumPy数组的类型是`numpy.ndarray`。
    ```python
    print(type(arr))  # 输出: <class 'numpy.ndarray'>
    ```
*   **元素类型**：使用`.dtype`属性查看，如上文所示。

![](img/1dcd53f072439c8d5b2bd1bf30706f10_49.png)

本节课中我们一起学习了NumPy数组的核心属性：`shape`（形状）、`ndim`（维度）、`size`（元素总数）和`dtype`（元素数据类型）。我们不仅学会了如何查看这些属性，还掌握了在创建时指定以及后续修改数据类型的方法。理解并合理运用这些属性，是进行高效数组操作和内存管理的基础。