# 数据分析实战：P5：04 NumPy数组属性详解 🔍

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_1.png)

在本节课中，我们将学习NumPy数组的几个核心属性。这些属性可以帮助我们快速了解数组的形状、维度、大小和数据类型，是进行数据操作和分析的基础。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_3.png)

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_5.png)

上一节我们介绍了NumPy数组的创建和一些常用方法，本节中我们来看看数组本身有哪些重要的属性可以为我们所用。

## 创建示例数组

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_7.png)

首先，我们创建一个示例的二维数组，以便后续演示属性。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_9.png)

```python
import numpy as np
arr = np.random.randint(0, 100, size=(5, 6))
```
这段代码创建了一个名为 `arr` 的数组，它是一个5行6列的二维数组，其中的元素是0到100之间（不含100）的随机整数。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_11.png)

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_13.png)

## 核心属性介绍

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_15.png)

以下是NumPy数组最常用的几个属性，它们能让我们快速掌握数组的基本信息。

### 1. 形状 (shape)

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_17.png)

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_19.png)

`shape` 属性返回一个元组，表示数组在每个维度上的大小。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_21.png)

```python
arr.shape
```
对于我们的示例数组 `arr`，`arr.shape` 将返回 `(5, 6)`，表示这是一个5行6列的数组。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_23.png)

### 2. 维度 (ndim)

`ndim` 属性返回一个整数，表示数组的维数。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_25.png)

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_27.png)

```python
arr.ndim
```
对于二维数组 `arr`，`arr.ndim` 将返回 `2`。

### 3. 元素总数 (size)

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_29.png)

`size` 属性返回一个整数，表示数组中元素的总个数。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_31.png)

```python
arr.size
```
对于5行6列的数组 `arr`，`arr.size` 将返回 `30`（即 5 * 6）。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_33.png)

### 4. 数据类型 (dtype)

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_35.png)

`dtype` 属性返回数组元素的数据类型。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_37.png)

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_39.png)

```python
arr.dtype
```
对于由随机整数构成的数组 `arr`，`arr.dtype` 通常会返回 `dtype(‘int64’)`，表示元素是64位整数。

**注意**：`dtype` 表示的是数组中**元素**的数据类型，而 `type(arr)` 返回的是数组**本身**的数据类型 `numpy.ndarray`。

## 深入理解数据类型 (dtype)

数据类型是NumPy中一个非常重要的概念，它决定了数组中每个元素所占用的内存大小和解释方式。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_41.png)

### 指定创建时的数据类型

在创建数组时，我们可以通过 `dtype` 参数直接指定元素的数据类型。

```python
arr_int32 = np.array([1, 2, 3], dtype=‘int32’)
print(arr_int32.dtype) # 输出：int32
```

### 修改已有数组的数据类型

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_43.png)

我们也可以直接为数组的 `dtype` 属性赋值，来修改其数据类型。NumPy会自动进行类型转换。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_45.png)

```python
arr = np.array([1, 2, 3])
arr.dtype = ‘uint8’ # 修改为无符号8位整数
print(arr.dtype) # 输出：uint8
```

### 为什么需要关注数据类型？

控制数据类型的主要目的是**优化内存使用**和**计算性能**。
*   默认的 `int64` 类型每个元素占用8字节（64位）内存。
*   如果数组有500万个元素，则需占用约 **40MB** 内存（500万 * 8字节）。
*   若将其改为 `uint8`（无符号8位整数），每个元素仅占用1字节，总内存占用降至约 **5MB**。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_47.png)

在处理大规模数据集时，合理选择数据类型可以显著减少内存开销，提升程序运行效率。

![](img/8bc1d79fb422e7f05dbc9b73a63d0ceb_49.png)

本节课中我们一起学习了NumPy数组的四个核心属性：**形状(shape)**、**维度(ndim)**、**元素总数(size)** 和 **数据类型(dtype)**。理解这些属性是进行高效数组操作和数据分析的第一步。特别是 `dtype` 属性，通过合理设置它，我们可以有效管理内存资源，为处理大型数据集做好准备。