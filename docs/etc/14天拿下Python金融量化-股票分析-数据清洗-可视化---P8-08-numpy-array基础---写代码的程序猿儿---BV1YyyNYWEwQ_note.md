# Python金融量化：P8：NumPy数组基础 🧮

![](img/432df6c884bc0f387a848243cfafc90c_1.png)

在本节课中，我们将要学习Python金融量化分析中至关重要的基础工具包之一——NumPy。我们将重点了解NumPy的核心数据结构`ndarray`，并通过实例理解它为何能极大地简化批量数据的计算。

![](img/432df6c884bc0f387a848243cfafc90c_3.png)

---

![](img/432df6c884bc0f387a848243cfafc90c_5.png)

![](img/432df6c884bc0f387a848243cfafc90c_7.png)

## NumPy简介

![](img/432df6c884bc0f387a848243cfafc90c_9.png)

NumPy是一个高性能的科学计算和数据分析基础包。它是后续课程中将要学习的Pandas库的构建基础。NumPy最主要的功能是提供了一个名为`ndarray`的多维数组对象，它能够高效地处理批量数据运算。

## 为何使用NumPy？

上一节我们介绍了NumPy的基本概念，本节中我们通过两个例子来直观感受NumPy的强大之处。

![](img/432df6c884bc0f387a848243cfafc90c_11.png)

### 示例一：货币批量换算

![](img/432df6c884bc0f387a848243cfafc90c_13.png)

![](img/432df6c884bc0f387a848243cfafc90c_15.png)

![](img/432df6c884bc0f387a848243cfafc90c_17.png)

假设我们已知50家公司的市值（单位：万美元），需要将所有市值换算为人民币（假设汇率为6.8）。

![](img/432df6c884bc0f387a848243cfafc90c_19.png)

![](img/432df6c884bc0f387a848243cfafc90c_21.png)

**传统Python列表方法：**
```python
import random
# 生成50个100到200之间的随机数，模拟市值
a = [random.uniform(100, 200) for _ in range(50)]
# 使用列表推导式进行换算
b = [value * 6.8 for value in a]
```

![](img/432df6c884bc0f387a848243cfafc90c_23.png)

**NumPy数组方法：**
```python
import numpy as np
# 将列表转换为NumPy数组
a_np = np.array(a)
# 直接进行数组与标量的乘法运算
b_np = a_np * 6.8
```
使用NumPy，我们无需编写循环，一行代码即可完成整个数组的批量运算，代码简洁且执行效率更高。

![](img/432df6c884bc0f387a848243cfafc90c_25.png)

![](img/432df6c884bc0f387a848243cfafc90c_27.png)

### 示例二：计算购物车总金额

![](img/432df6c884bc0f387a848243cfafc90c_29.png)

![](img/432df6c884bc0f387a848243cfafc90c_31.png)

假设我们有两个列表，一个存储商品单价，另一个存储对应商品数量，需要计算总金额。

![](img/432df6c884bc0f387a848243cfafc90c_33.png)

**NumPy数组方法：**
```python
import numpy as np
# 生成单价和数量数组
price = np.random.uniform(10, 20, 50)  # 50个商品的单价
quantity = np.random.randint(1, 10, 50) # 50个商品的数量
# 计算每个商品的总价，然后求和
total_cost = (price * quantity).sum()
```
NumPy支持数组间的逐元素运算（如`price * quantity`），并提供了便捷的聚合函数（如`.sum()`），使得复杂计算变得异常简单。

通过以上例子，我们感受到了NumPy在批量数据处理上的便捷与高效。接下来，我们将深入探讨其核心对象——`ndarray`。

![](img/432df6c884bc0f387a848243cfafc90c_35.png)

![](img/432df6c884bc0f387a848243cfafc90c_37.png)

---

![](img/432df6c884bc0f387a848243cfafc90c_39.png)

![](img/432df6c884bc0f387a848243cfafc90c_41.png)

![](img/432df6c884bc0f387a848243cfafc90c_43.png)

## ndarray：NumPy的核心数组对象

![](img/432df6c884bc0f387a848243cfafc90c_45.png)

![](img/432df6c884bc0f387a848243cfafc90c_47.png)

`ndarray`是NumPy中最重要的对象，我们通常通过`np.array()`函数将Python列表转换为NumPy数组。

![](img/432df6c884bc0f387a848243cfafc90c_49.png)

### 创建数组
```python
import numpy as np
# 从列表创建一维数组
arr1 = np.array([1, 2, 3, 4, 5])
# 从范围创建数组
arr2 = np.array(range(10))
```

### 数组与列表的区别

![](img/432df6c884bc0f387a848243cfafc90c_51.png)

理解数组与Python列表的区别至关重要，以下是两个核心区别：

![](img/432df6c884bc0f387a848243cfafc90c_53.png)

1.  **元素类型必须一致**：Python列表可以容纳不同类型的元素（如整数、字符串混合）。而NumPy数组要求所有元素必须是相同的数据类型。
2.  **大小不可变**：Python列表可以动态地添加或删除元素（如使用`append`方法）。NumPy数组在创建后，其大小（即元素总数）是固定的，无法直接更改。这种设计是其高性能的原因之一。

![](img/432df6c884bc0f387a848243cfafc90c_55.png)

![](img/432df6c884bc0f387a848243cfafc90c_57.png)

---

![](img/432df6c884bc0f387a848243cfafc90c_59.png)

## 数组的常用属性

创建数组后，我们可以通过一系列属性来了解其信息。

以下是`ndarray`对象的一些常用属性：
*   **`dtype`**：返回数组中元素的数据类型，例如`int64`, `float32`。
*   **`size`**：返回数组中元素的总数。
*   **`shape`**：返回一个元组，表示数组的维度。例如，一个3行4列的二维数组，其`shape`为`(3, 4)`。
*   **`ndim`**：返回数组的维数（维度数量）。一维数组返回1，二维数组返回2。
*   **`T`**：返回数组的转置。对于二维数组，行和列将互换。

### 属性演示
```python
import numpy as np
# 创建一个二维数组
arr_2d = np.array([[1, 2, 3], [4, 5, 6]])
print(“数组内容：\n”, arr_2d)
print(“数据类型(dtype)：”, arr_2d.dtype)
print(“元素总数(size)：”, arr_2d.size)
print(“数组形状(shape)：”, arr_2d.shape)
print(“数组维数(ndim)：”, arr_2d.ndim)
print(“数组转置(T)：\n”, arr_2d.T)
```

### 理解多维数组
*   **一维数组**：可以看作一条线。
*   **二维数组**：可以看作一张表格（具有行和列），在金融数据中非常常见，例如多只股票多天的价格。
*   **三维数组**：可以想象成一个数据立方体或多页表格，例如多只股票、多个交易日、多种指标（开盘价、收盘价等）的数据。

![](img/432df6c884bc0f387a848243cfafc90c_61.png)

![](img/432df6c884bc0f387a848243cfafc90c_63.png)

---

## 总结

![](img/432df6c884bc0f387a848243cfafc90c_65.png)

![](img/432df6c884bc0f387a848243cfafc90c_67.png)

本节课中我们一起学习了NumPy库的基础知识。我们首先了解了NumPy在科学计算中的核心地位，然后通过实例对比，深刻体会到使用NumPy数组进行批量数据运算的简洁与高效。我们重点探讨了NumPy的核心——`ndarray`数组对象，明确了其与Python列表在**元素类型一致性**和**大小不可变性**上的关键区别。最后，我们掌握了数组的多个常用属性（`dtype`, `size`, `shape`, `ndim`, `T`），这些是后续进行数据操作和分析的基石。掌握NumPy数组是步入Python数据分析和金融量化领域的重要一步。