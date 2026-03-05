# 量化交易实战课程：P10：金融量化分析-numpy-array基础 🧮

在本节课中，我们将要学习NumPy库的基础知识，特别是其核心数据结构——`ndarray`（多维数组）。NumPy是进行高效科学计算和数据分析的基石，也是后续学习Pandas等高级工具的基础。我们将通过实例理解其优势，并掌握创建数组、查看数组属性的基本方法。

![](img/329981457b102ccc634faf3a3e7fae0e_1.png)

## 为什么要使用NumPy？ 🤔

![](img/329981457b102ccc634faf3a3e7fae0e_3.png)

![](img/329981457b102ccc634faf3a3e7fae0e_5.png)

上一节我们介绍了NumPy是数据分析的基础包。本节中我们来看看为什么需要它，通过两个例子直观感受其高效性。

![](img/329981457b102ccc634faf3a3e7fae0e_7.png)

![](img/329981457b102ccc634faf3a3e7fae0e_9.png)

### 示例一：货币批量换算

假设已知若干家跨国公司的市值（单位为美元），需要将所有市值换算为人民币。

如果不使用NumPy，传统方法可能需要循环遍历列表进行计算：

```python
# 传统方法：使用列表推导式
a = [random.uniform(100, 200) for _ in range(50)] # 模拟50家公司的市值（万美元）
exchange_rate = 6.8
b = [value * exchange_rate for value in a]
```

![](img/329981457b102ccc634faf3a3e7fae0e_11.png)

使用NumPy，可以极大地简化操作：

![](img/329981457b102ccc634faf3a3e7fae0e_13.png)

![](img/329981457b102ccc634faf3a3e7fae0e_15.png)

```python
import numpy as np
a_array = np.array(a) # 将列表转换为NumPy数组
result = a_array * exchange_rate # 直接进行数组与标量的乘法运算
```
**公式**：`result = array * scalar`
NumPy允许对整个数组执行批量运算，无需编写循环，代码简洁且执行效率高。

![](img/329981457b102ccc634faf3a3e7fae0e_17.png)

![](img/329981457b102ccc634faf3a3e7fae0e_19.png)

### 示例二：计算购物车总金额

![](img/329981457b102ccc634faf3a3e7fae0e_21.png)

![](img/329981457b102ccc634faf3a3e7fae0e_23.png)

已知购物车中每件商品的价格和对应件数，求总金额。

![](img/329981457b102ccc634faf3a3e7fae0e_25.png)

传统方法需要将两个列表的对应元素相乘再求和。使用NumPy则更为直接：

![](img/329981457b102ccc634faf3a3e7fae0e_27.png)

![](img/329981457b102ccc634faf3a3e7fae0e_29.png)

```python
# 价格列表和件数列表
prices = np.array([...])
quantities = np.array([...])
# 对应元素相乘得到各商品总价，再求和
total_cost = (prices * quantities).sum()
```
**公式**：`total_cost = Σ(price_i * quantity_i)`
NumPy的数组运算不仅代码简洁，在底层通过优化实现了更快的计算速度。

![](img/329981457b102ccc634faf3a3e7fae0e_31.png)

![](img/329981457b102ccc634faf3a3e7fae0e_33.png)

通过以上例子，我们感受到了NumPy在处理批量数据运算时的强大与便捷。接下来，我们将深入探讨其核心对象。

## NumPy的核心：ndarray数组对象 📦

上一节我们通过例子感受了NumPy的便捷，本节中我们来正式认识其核心——`ndarray`数组对象。

![](img/329981457b102ccc634faf3a3e7fae0e_35.png)

`ndarray`是一个多维数组对象，与Python原生的列表有显著区别。以下是创建数组的基本方法：

![](img/329981457b102ccc634faf3a3e7fae0e_37.png)

![](img/329981457b102ccc634faf3a3e7fae0e_39.png)

![](img/329981457b102ccc634faf3a3e7fae0e_41.png)

```python
import numpy as np
arr1 = np.array([1, 2, 3, 4, 5]) # 从列表创建一维数组
arr2 = np.array(range(10)) # 从可迭代对象创建
```

![](img/329981457b102ccc634faf3a3e7fae0e_43.png)

![](img/329981457b102ccc634faf3a3e7fae0e_45.png)

数组与列表的主要区别有两点：
1.  **元素类型必须相同**：数组中的所有元素必须是相同的数据类型（如全是整数或全是浮点数）。而Python列表可以混合存放不同类型的数据。
2.  **大小不可变**：数组一旦创建，其大小（即元素总数）就固定了，不能像列表那样随意追加(`append`)元素。这是其实现高效内存访问和计算的基础。

![](img/329981457b102ccc634faf3a3e7fae0e_47.png)

![](img/329981457b102ccc634faf3a3e7fae0e_49.png)

## 数组的常用属性 🔍

了解了数组的创建和特点后，我们来看看如何查看和了解一个数组的具体信息。以下是`ndarray`对象的一些常用属性。

以下是数组`arr`的一些关键属性介绍：
*   **`dtype`**：查看数组元素的数据类型。例如`int64`, `float32`。
*   **`size`**：查看数组的元素总个数。
*   **`shape`**：查看数组的维度形状，返回一个元组。例如`(2, 3)`表示2行3列。
*   **`ndim`**：查看数组的维数（轴的数量）。例如二维数组的`ndim`为2。
*   **`T`**：获取数组的转置（行变列，列变行）。

![](img/329981457b102ccc634faf3a3e7fae0e_51.png)

### 数据类型（dtype）

![](img/329981457b102ccc634faf3a3e7fae0e_53.png)

![](img/329981457b102ccc634faf3a3e7fae0e_55.png)

NumPy定义了丰富的数据类型，主要分为以下几类：
*   **布尔型**：`bool_`
*   **整型**：`int8`, `int16`, `int32`, `int64`
*   **无符号整型**：`uint8`, `uint16`, `uint32`, `uint64`
*   **浮点型**：`float16`, `float32`, `float64`
*   **复数型**：`complex64`, `complex128`

![](img/329981457b102ccc634faf3a3e7fae0e_57.png)

类型名中的数字（如64）代表该类型在内存中占用的**位数**。例如，`int64`表示用64位二进制（即8字节）来存储一个整数，其表示范围约为±9.22e18。数组会为所有元素统一分配连续的内存空间，并使用相同的类型，这是其高效的原因之一。

![](img/329981457b102ccc634faf3a3e7fae0e_59.png)

### 维度与形状（ndim, shape）

数组可以是多维的。最常用的是二维数组，可以将其理解为一张表格（有行和列）。

```python
# 创建一个二维数组（2行3列）
arr_2d = np.array([[1, 2, 3], [4, 5, 6]])
print(‘数组：\n‘, arr_2d)
print(‘维度(ndim):‘, arr_2d.ndim) # 输出：2
print(‘形状(shape):‘, arr_2d.shape) # 输出：(2, 3)
print(‘元素总数(size):‘, arr_2d.size) # 输出：6
```

对于三维数组，可以将其理解为一个由多张二维表格（页）堆叠起来的“立方体”或“书本”。

### 数组转置（T）

![](img/329981457b102ccc634faf3a3e7fae0e_61.png)

转置是线性代数中的常见操作，对于二维数组而言，转置就是交换行和列的位置。

![](img/329981457b102ccc634faf3a3e7fae0e_63.png)

```python
arr = np.array([[1, 2, 3], [4, 5, 6]])
print(‘原数组：\n‘, arr)
print(‘转置后：\n‘, arr.T)
```
输出结果为：
```
原数组：
 [[1 2 3]
 [4 5 6]]
转置后：
 [[1 4]
 [2 5]
 [3 6]]
```
**公式**：若原数组为 `A`， 其转置记为 `Aᵀ`， 满足 `Aᵀ[i, j] = A[j, i]`。

## 总结 📝

![](img/329981457b102ccc634faf3a3e7fae0e_65.png)

![](img/329981457b102ccc634faf3a3e7fae0e_67.png)

本节课中我们一起学习了NumPy库的入门知识。我们首先通过实例对比，理解了NumPy在**批量数据运算**上的高效与简洁。然后，我们深入探讨了NumPy的核心——`ndarray`数组对象，了解了其与Python列表在**元素类型一致性**和**大小不可变性**上的关键区别。最后，我们掌握了查看数组**数据类型(`dtype`)**、**维度形状(`shape`, `ndim`)**、**元素总数(`size`)**以及进行**数组转置(`T`)**等基本操作。这些是后续进行更复杂金融数据分析和计算的基础。