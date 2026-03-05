# Python金融量化分析：P11：NumPy数组基础

## 概述

在本节课中，我们将要学习金融量化分析的核心工具之一——NumPy。我们将从NumPy的引入原因讲起，通过具体例子感受其高效性，然后深入讲解其核心数据结构`ndarray`（多维数组）的创建、常用属性及基本操作。掌握NumPy是后续学习Pandas等高级数据分析库的基础。

![](img/863d5f476383cd0d048cc6cefabe9c1d_1.png)

---

## 为什么要使用NumPy？🚀

![](img/863d5f476383cd0d048cc6cefabe9c1d_3.png)

NumPy是一个高性能的科学计算和数据分析基础包。它是后续要讲的Pandas库的基础，因为Pandas是在NumPy的基础上构建的。

![](img/863d5f476383cd0d048cc6cefabe9c1d_5.png)

NumPy最主要的功能是提供了一个名为`ndarray`的多维数组数据结构，以及针对数组进行高效运算的工具。使用NumPy后，在对批量数据进行运算时可以避免使用循环，从而极大地提升效率和代码简洁性。除此之外，NumPy还提供了线性代数、傅里叶变换等功能。

![](img/863d5f476383cd0d048cc6cefabe9c1d_7.png)

![](img/863d5f476383cd0d048cc6cefabe9c1d_9.png)

NumPy的安装非常简单，使用`pip install numpy`命令即可。

## 引入NumPy

官方推荐的引入方式是：
```python
import numpy as np
```
这是一种约定俗成的缩写，后续在代码中看到`np`，指的就是NumPy。

## NumPy高效性示例

为了直观感受NumPy的高效，我们来看两个例子。

### 示例一：货币批量换算

假设已知若干家跨国公司的市值（单位为美元），需要将所有市值换算为人民币。

![](img/863d5f476383cd0d048cc6cefabe9c1d_11.png)

**传统Python列表方法：**
```python
import random
# 模拟生成50家公司的市值（单位：万美元）
a = [random.uniform(100, 200) for _ in range(50)]
# 将美元换算为人民币（假设汇率为6.8）
b = []
for value in a:
    b.append(value * 6.8)
# 或者使用列表推导式
b = [value * 6.8 for value in a]
```

![](img/863d5f476383cd0d048cc6cefabe9c1d_13.png)

**使用NumPy的方法：**
```python
import numpy as np
# 将列表转换为NumPy数组
a_np = np.array(a)
# 直接进行数组与标量的乘法运算
b_np = a_np * 6.8
```
使用NumPy，一行代码`a_np * 6.8`就完成了整个列表的批量运算，无需循环，代码简洁且执行速度更快。

![](img/863d5f476383cd0d048cc6cefabe9c1d_15.png)

![](img/863d5f476383cd0d048cc6cefabe9c1d_17.png)

### 示例二：计算购物车总金额

![](img/863d5f476383cd0d048cc6cefabe9c1d_19.png)

已知购物车中每件商品的价格和对应件数，要求总金额。

![](img/863d5f476383cd0d048cc6cefabe9c1d_21.png)

**使用NumPy的方法：**
```python
import numpy as np
import random
# 生成50件商品的价格（10-20元）和件数（1-10件）
prices = np.array([random.uniform(10, 20) for _ in range(50)])
counts = np.array([random.randint(1, 10) for _ in range(50)])
# 计算每件商品的总价
item_totals = prices * counts
# 计算购物车总金额
total_cost = item_totals.sum()
```
NumPy的数组支持对应元素直接相乘（`prices * counts`），然后使用`.sum()`方法快速求和，整个过程高效直观。

![](img/863d5f476383cd0d048cc6cefabe9c1d_23.png)

通过以上例子，我们直观感受到了NumPy在处理批量数据运算时的便捷与高效。接下来，我们来深入了解其核心数据结构。

---

![](img/863d5f476383cd0d048cc6cefabe9c1d_25.png)

## NumPy核心：ndarray数组对象🧱

![](img/863d5f476383cd0d048cc6cefabe9c1d_27.png)

NumPy的核心是提供了一个名为`ndarray`（N-dimensional array，N维数组）的对象。我们通常使用`np.array()`函数来创建它。

![](img/863d5f476383cd0d048cc6cefabe9c1d_29.png)

![](img/863d5f476383cd0d048cc6cefabe9c1d_31.png)

### 创建数组

![](img/863d5f476383cd0d048cc6cefabe9c1d_33.png)

创建数组最基本的方法是将一个列表（或任何可迭代对象）传递给`np.array()`函数。
```python
import numpy as np
# 从列表创建一维数组
arr1 = np.array([1, 2, 3, 4, 5])
# 从range对象创建
arr2 = np.array(range(10))
```

### 数组与列表的区别

数组与Python原生的列表有两大关键区别：
1.  **元素类型必须一致**：列表可以容纳不同类型的元素（如字符串、数字混合）。而数组要求所有元素必须是相同的数据类型（通常是数字）。
2.  **大小不可动态改变**：列表可以随时使用`.append()`方法添加元素。数组在创建时就固定了大小，不能像列表一样随意追加元素。这种设计与其底层内存结构有关，也是其高效运算的原因之一。

---

## 数组的常用属性🔍

![](img/863d5f476383cd0d048cc6cefabe9c1d_35.png)

上一节我们介绍了如何创建数组以及数组与列表的区别。本节中，我们来看看如何查看和操作数组的一些基本属性。

![](img/863d5f476383cd0d048cc6cefabe9c1d_37.png)

![](img/863d5f476383cd0d048cc6cefabe9c1d_39.png)

以下是一些`ndarray`对象常用的属性：

![](img/863d5f476383cd0d048cc6cefabe9c1d_41.png)

![](img/863d5f476383cd0d048cc6cefabe9c1d_43.png)

```python
import numpy as np
# 创建一个二维数组作为示例
arr = np.array([[1, 2, 3], [4, 5, 6]])
```

![](img/863d5f476383cd0d048cc6cefabe9c1d_45.png)

### 数据类型 (dtype)

![](img/863d5f476383cd0d048cc6cefabe9c1d_47.png)

`dtype`属性表示数组中元素的数据类型。NumPy有自己的一套数据类型体系，主要是为了高效存储和计算。

![](img/863d5f476383cd0d048cc6cefabe9c1d_49.png)

```python
print(arr.dtype)  # 输出：int64
```

以下是NumPy中常见的数据类型分类：
*   **布尔型**：`bool_`
*   **整型**：`int8`, `int16`, `int32`, `int64`
*   **无符号整型**：`uint8`, `uint16`, `uint32`, `uint64`
*   **浮点型**：`float16`, `float32`, `float64`
*   **复数型**：`complex64`, `complex128`

类型名后的数字（如`int64`中的64）代表该类型在内存中占用的**位数**。位数越多，能表示的数值范围越大或精度越高。例如，`int64`表示64位（8字节）的有符号整数。

### 元素总数 (size)

![](img/863d5f476383cd0d048cc6cefabe9c1d_51.png)

`size`属性返回数组中元素的总个数。

```python
print(arr.size)  # 输出：6 (因为数组有2行3列，共6个元素)
```

![](img/863d5f476383cd0d048cc6cefabe9c1d_53.png)

### 形状 (shape)

![](img/863d5f476383cd0d048cc6cefabe9c1d_55.png)

`shape`属性返回一个元组，表示数组在每个维度上的大小（长度）。对于二维数组，就是（行数， 列数）。

![](img/863d5f476383cd0d048cc6cefabe9c1d_57.png)

```python
print(arr.shape)  # 输出：(2, 3)
```

![](img/863d5f476383cd0d048cc6cefabe9c1d_59.png)

### 维数 (ndim)

`ndim`属性返回数组的维数（轴数）。

```python
print(arr.ndim)  # 输出：2
```

### 转置 (T)

`.T`属性（或`np.transpose()`函数）返回数组的转置。对于二维数组，转置操作就是行变列，列变行。

```python
print(arr)
# 输出：
# [[1 2 3]
#  [4 5 6]]

print(arr.T)
# 输出：
# [[1 4]
#  [2 5]
#  [3 6]]
```

---

## 总结

![](img/863d5f476383cd0d048cc6cefabe9c1d_61.png)

本节课中我们一起学习了NumPy的基础知识。

![](img/863d5f476383cd0d048cc6cefabe9c1d_63.png)

我们首先了解了**为什么需要NumPy**：它提供了高效的`ndarray`数组结构和批量运算能力，是金融量化分析和科学计算的基石。

接着，我们通过两个生动的例子（货币换算和购物车计算）**直观感受了NumPy的便捷与高效**，它让我们摆脱了繁琐的循环，用更简洁的代码完成复杂计算。

然后，我们深入探讨了NumPy的**核心对象`ndarray`**，学会了如何使用`np.array()`创建数组，并理解了**数组与Python列表在元素类型和大小可变性上的关键区别**。

最后，我们掌握了数组的一系列**常用属性**，包括查看数据类型的`dtype`、查看元素总数的`size`、查看形状的`shape`、查看维数的`ndim`以及进行矩阵转置的`.T`属性。

![](img/863d5f476383cd0d048cc6cefabe9c1d_65.png)

![](img/863d5f476383cd0d048cc6cefabe9c1d_67.png)

掌握这些基础是后续进行更复杂金融数据操作和分析的第一步。在接下来的课程中，我们将学习如何使用NumPy进行更丰富的数组操作和数学运算。