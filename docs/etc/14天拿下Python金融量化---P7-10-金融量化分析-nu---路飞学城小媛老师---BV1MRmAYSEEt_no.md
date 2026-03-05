# Python金融量化：P7：NumPy数组基础

## 概述
在本节课中，我们将要学习金融量化分析中的一个核心工具包——NumPy。我们将了解NumPy是什么，它为什么重要，并重点学习其核心数据结构——数组（ndarray）的基础知识，包括如何创建数组以及数组的常用属性。

![](img/912c761b212038ddbeae322f62fe80ac_1.png)

NumPy是Python中进行高性能科学计算和数据分析的基础包。它是后续我们将要学习的Pandas等高级数据分析库的基石。NumPy最主要的功能是提供了一个高效的多维数组对象，使得我们可以对批量数据进行快速的数学运算，而无需编写繁琐的循环。

![](img/912c761b212038ddbeae322f62fe80ac_3.png)

![](img/912c761b212038ddbeae322f62fe80ac_5.png)

## 为什么要使用NumPy？

![](img/912c761b212038ddbeae322f62fe80ac_7.png)

![](img/912c761b212038ddbeae322f62fe80ac_9.png)

上一节我们概述了NumPy的重要性，本节中我们通过两个具体的例子来直观感受NumPy带来的便利。

### 示例一：货币批量换算
假设我们已知50家跨国公司的市值（以美元为单位），现在需要将所有市值换算为人民币（假设汇率为1美元=6.8人民币）。

如果不使用NumPy，我们可能需要使用循环或列表推导式来完成：

```python
import random
# 生成50个模拟市值数据（单位：百万美元）
a = [random.uniform(100, 200) for _ in range(50)]
# 使用列表推导式进行换算
b = [value * 6.8 for value in a]
```

![](img/912c761b212038ddbeae322f62fe80ac_11.png)

而使用NumPy，操作将变得异常简洁和高效：

![](img/912c761b212038ddbeae322f62fe80ac_13.png)

![](img/912c761b212038ddbeae322f62fe80ac_15.png)

```python
import numpy as np
# 将列表转换为NumPy数组
a_np = np.array(a)
# 直接进行数组与标量的乘法运算
result = a_np * 6.8
```

![](img/912c761b212038ddbeae322f62fe80ac_17.png)

![](img/912c761b212038ddbeae322f62fe80ac_19.png)

### 示例二：计算购物车总金额
假设我们有一个商品价格列表和一个对应的商品数量列表，需要计算总金额。

![](img/912c761b212038ddbeae322f62fe80ac_21.png)

传统方法可能需要遍历列表并累加。使用NumPy，我们可以直接对两个数组进行逐元素相乘，然后求和：

![](img/912c761b212038ddbeae322f62fe80ac_23.png)

```python
import numpy as np
# 模拟价格和数量
prices = np.array([10.5, 20.0, 15.0])
quantities = np.array([2, 1, 3])
# 计算总金额
total_cost = (prices * quantities).sum()
```

![](img/912c761b212038ddbeae322f62fe80ac_25.png)

![](img/912c761b212038ddbeae322f62fe80ac_27.png)

通过以上例子可以看到，NumPy的数组运算不仅代码简洁，而且执行效率远高于普通的Python循环，在处理大规模数据时优势尤为明显。

![](img/912c761b212038ddbeae322f62fe80ac_29.png)

![](img/912c761b212038ddbeae322f62fe80ac_31.png)

## NumPy核心：ndarray对象

![](img/912c761b212038ddbeae322f62fe80ac_33.png)

上一节我们通过例子感受了NumPy的便捷，本节中我们来深入了解一下其核心——`ndarray`对象。

`ndarray`（N-dimensional array，N维数组）是NumPy中最重要的对象。我们可以使用`np.array()`函数来创建它。

```python
import numpy as np
# 从列表创建一维数组
arr1 = np.array([1, 2, 3, 4, 5])
# 从range对象创建
arr2 = np.array(range(10))
```

![](img/912c761b212038ddbeae322f62fe80ac_35.png)

![](img/912c761b212038ddbeae322f62fe80ac_37.png)

![](img/912c761b212038ddbeae322f62fe80ac_39.png)

### 数组与列表的区别
理解数组与Python原生列表的区别至关重要，主要有两点：

![](img/912c761b212038ddbeae322f62fe80ac_41.png)

![](img/912c761b212038ddbeae322f62fe80ac_43.png)

1.  **元素类型必须相同**：Python列表可以容纳不同类型的元素（如整数、字符串混合）。而NumPy数组要求所有元素必须是相同的数据类型（如全是整数或全是浮点数）。
2.  **大小固定**：Python列表可以动态地添加或删除元素。NumPy数组在创建后大小是固定的，不能像列表那样直接`append`新元素。这种设计与其底层高效的内存布局和计算方式有关。

![](img/912c761b212038ddbeae322f62fe80ac_45.png)

![](img/912c761b212038ddbeae322f62fe80ac_47.png)

## 数组的常用属性

![](img/912c761b212038ddbeae322f62fe80ac_49.png)

创建数组后，我们可以通过一系列属性来了解它的信息。以下是几个最常用的属性：

### dtype：数据类型
`dtype`属性显示了数组中元素的数据类型。NumPy提供了丰富的数据类型以优化存储和计算。

```python
arr = np.array([1, 2, 3])
print(arr.dtype)  # 输出：int64 (64位整数)
```

![](img/912c761b212038ddbeae322f62fe80ac_51.png)

以下是NumPy中常见的数据类型分类：
*   **布尔型**：`bool_`
*   **有符号整型**：`int8`, `int16`, `int32`, `int64`
*   **无符号整型**：`uint8`, `uint16`, `uint32`, `uint64`
*   **浮点型**：`float16`, `float32`, `float64`
*   **复数型**：`complex64`, `complex128`

![](img/912c761b212038ddbeae322f62fe80ac_53.png)

![](img/912c761b212038ddbeae322f62fe80ac_55.png)

> **说明**：类型名后的数字（如`int64`中的64）代表该类型在内存中占用的**位数**。位数越多，能表示的数值范围越大或精度越高。例如，`int64`可以表示大约从$-9.22 \times 10^{18}$到$9.22 \times 10^{18}$的整数。

![](img/912c761b212038ddbeae322f62fe80ac_57.png)

### shape：数组形状
`shape`属性返回一个元组，表示数组在每个维度上的大小（长度）。

![](img/912c761b212038ddbeae322f62fe80ac_59.png)

```python
# 二维数组（矩阵）
arr_2d = np.array([[1, 2, 3], [4, 5, 6]])
print(arr_2d.shape)  # 输出：(2, 3) 表示2行3列

# 三维数组（可以想象成一个数据立方体或多页的表格）
arr_3d = np.array([[[1,2], [3,4]], [[5,6], [7,8]]])
print(arr_3d.shape)  # 输出：(2, 2, 2) 可以理解为2页，每页2行2列
```

### size：元素总数
`size`属性返回数组中所有元素的总数量。

```python
arr_2d = np.array([[1, 2, 3], [4, 5, 6]])
print(arr_2d.size)  # 输出：6 (2行 * 3列 = 6个元素)
```

### ndim：数组维度
`ndim`属性返回数组的维数（轴的数量）。

![](img/912c761b212038ddbeae322f62fe80ac_61.png)

```python
arr_1d = np.array([1, 2, 3])
arr_2d = np.array([[1, 2], [3, 4]])
print(arr_1d.ndim)  # 输出：1 (一维)
print(arr_2d.ndim)  # 输出：2 (二维)
```

![](img/912c761b212038ddbeae322f62fe80ac_63.png)

### T：数组转置
对于二维数组，`.T`属性可以获取其转置。转置操作将数组的行和列互换。

```python
arr = np.array([[1, 2, 3], [4, 5, 6]])
print("原数组：\n", arr)
print("转置后：\n", arr.T)
```
输出：
```
原数组：
 [[1 2 3]
 [4 5 6]]
转置后：
 [[1 4]
 [2 5]
 [3 6]]
```

![](img/912c761b212038ddbeae322f62fe80ac_65.png)

![](img/912c761b212038ddbeae322f62fe80ac_67.png)

## 总结
本节课中我们一起学习了NumPy的基础知识。我们首先了解了NumPy在金融量化分析中的核心地位，它提供了高效的数组运算能力。然后，我们通过实例对比了使用NumPy和传统方法的差异，直观感受到了其简洁与高效。接着，我们深入探讨了NumPy的核心——`ndarray`数组对象，并学习了如何创建数组，以及数组与Python列表的关键区别。最后，我们掌握了一系列数组的常用属性，包括`dtype`（数据类型）、`shape`（形状）、`size`（元素总数）、`ndim`（维度）和`.T`（转置），这些是后续进行更复杂数据操作的基础。在接下来的课程中，我们将基于这些知识，学习更多关于数组的创建、索引和运算方法。