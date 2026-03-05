# Python机器学习与量化交易：P11：NumPy数组基础

## 概述 📋
在本节课中，我们将要学习Python科学计算的核心库之一——NumPy。我们将重点了解NumPy的核心数据结构`ndarray`（多维数组），学习其创建方法、基本属性以及它为何能高效地进行批量数据运算。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_1.png)

---

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_3.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_5.png)

## 为什么要使用NumPy？🤔
上一节我们介绍了NumPy是科学计算的基础包。本节中我们来看看为什么需要它。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_7.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_9.png)

NumPy提供了一个名为`ndarray`的多维数组对象，它能高效地处理批量数据运算，无需使用循环。

以下是两个直观的例子：

**例子1：货币批量转换**
假设已知50家公司的市值（单位：美元），需要全部转换为人民币（汇率为6.8）。

```python
import random
# 模拟50家公司的市值（100万到200万美元）
a = [random.uniform(100, 200) for _ in range(50)]
print(a)
```
使用传统列表方法转换需要循环或列表推导式。而使用NumPy则非常简单：
```python
import numpy as np
a_np = np.array(a) # 将列表转换为NumPy数组
result = a_np * 6.8 # 数组直接与标量相乘
print(result)
```

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_11.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_13.png)

**例子2：计算购物车总金额**
已知商品价格列表和对应数量列表，计算总金额。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_15.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_17.png)

```python
# 模拟50件商品的价格和数量
prices = [random.uniform(10, 20) for _ in range(50)]
counts = [random.randint(1, 10) for _ in range(50)]

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_19.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_21.png)

prices_np = np.array(prices)
counts_np = np.array(counts)
# 数组对应元素相乘，然后求和
total = (prices_np * counts_np).sum()
print(total)
```
通过以上例子可以感受到，NumPy的数组运算语法简洁，并且执行效率远高于Python原生的循环。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_23.png)

---

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_25.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_27.png)

## NumPy核心：ndarray数组 🧱
上一节我们通过例子感受了NumPy的便捷。本节中我们来深入了解其核心对象`ndarray`。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_29.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_31.png)

`ndarray`是一个多维数组对象。使用`np.array()`函数创建，参数通常是一个列表。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_33.png)

```python
import numpy as np
arr1 = np.array([1, 2, 3, 4, 5]) # 一维数组
arr2 = np.array(range(10)) # 从可迭代对象创建
arr3 = np.array([[1,2,3], [4,5,6]]) # 二维数组
```

**数组与列表的主要区别：**
1.  **元素类型必须相同**：Python列表可以存储不同类型的数据（如字符串、数字混合）。NumPy数组要求所有元素必须是相同的数据类型。
2.  **大小固定**：Python列表可以动态`append`元素。NumPy数组在创建后大小固定，不能直接添加元素（但可以创建新数组）。

这种设计使得NumPy数组在内存中连续存储，这是其高效运算的基础。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_35.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_37.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_39.png)

---

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_41.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_43.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_45.png)

## 数组的常用属性 📊
了解了数组的创建，本节我们来看看如何查看数组的基本信息。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_47.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_49.png)

以下是`ndarray`对象的一些常用属性：

```python
import numpy as np
arr = np.array([[1, 2, 3], [4, 5, 6]]) # 创建一个2行3列的二维数组

# 1. dtype: 数组元素的数据类型
print(arr.dtype) # 例如：int64

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_51.png)

# 2. size: 数组元素的总个数
print(arr.size) # 输出：6

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_53.png)

# 3. shape: 数组的形状（各维度的长度），返回一个元组
print(arr.shape) # 输出：(2, 3) 表示2行3列

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_55.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_57.png)

# 4. ndim: 数组的维度（轴数）
print(arr.ndim) # 输出：2

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_59.png)

# 5. T: 数组的转置（行变列，列变行）
print(arr.T)
# 原始arr: [[1 2 3],
#           [4 5 6]]
# 转置后:   [[1 4],
#           [2 5],
#           [3 6]]
```

**关于数据类型（dtype）**
NumPy定义了丰富的数据类型，主要用于数值计算：
*   `bool_`: 布尔型
*   `int8/int16/int32/int64`: 有符号整数
*   `uint8/uint16/uint32/uint64`: 无符号整数
*   `float16/float32/float64`: 浮点数
*   `complex64/complex128`: 复数

例如，`int64`表示用64位二进制（8字节）存储一个整数，其数值范围约为$-2^{63}$到$2^{63}-1$。`float64`是双精度浮点数，精度更高。

---

## 总结 🎯
本节课中我们一起学习了NumPy库的基础知识。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_61.png)

我们首先通过实例了解了NumPy在**批量数据运算**上的巨大优势，它允许我们直接对整个数组进行数学操作，而无需编写低效的循环。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_63.png)

接着，我们深入探讨了NumPy的**核心数据结构`ndarray`（多维数组）**，掌握了使用`np.array()`创建数组的方法，并理解了其与Python列表在**元素类型一致性**和**大小固定**上的关键区别。

最后，我们学习了数组的多个**常用属性**，包括查看数据类型的`dtype`、查看元素总数的`size`、查看形状的`shape`、查看维度的`ndim`以及进行矩阵转置的`.T`属性。

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_65.png)

![](img/cb5a41c23f7d0b9fc525c2c6f9fff83e_67.png)

掌握这些基础是后续进行更复杂的数据处理、分析和机器学习建模的第一步。在接下来的课程中，我们将学习NumPy更强大的索引、切片和运算功能。