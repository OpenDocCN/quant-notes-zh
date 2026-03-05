# Python金融分析+量化交易：P14：12 金融量化分析-numpy-array索引和切片 📊

![](img/2fa15b462d2315fb03dd797f3990fd36_1.png)

在本节课中，我们将要学习NumPy数组（ndarray）的快捷运算、索引和切片操作。这些是进行高效数据处理和金融量化分析的基础。

![](img/2fa15b462d2315fb03dd797f3990fd36_3.png)

## 数组的快捷运算

![](img/2fa15b462d2315fb03dd797f3990fd36_5.png)

上一节我们介绍了NumPy数组的创建，本节中我们来看看数组能进行哪些快捷运算。NumPy数组支持与标量（单个数值）以及其他数组进行快速的逐元素运算。

![](img/2fa15b462d2315fb03dd797f3990fd36_7.png)

### 与标量运算

![](img/2fa15b462d2315fb03dd797f3990fd36_8.png)

![](img/2fa15b462d2315fb03dd797f3990fd36_10.png)

标量即单个数值。数组可以与标量进行加、减、乘、除等运算，运算会应用到数组的每一个元素上。

![](img/2fa15b462d2315fb03dd797f3990fd36_12.png)

```python
import numpy as np
A = np.arange(10)  # 创建数组 [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(A + 1)  # 每个元素加1
print(A * 3)  # 每个元素乘以3
print(3 / A)  # 用3除以每个元素（注意：除以0会产生inf警告）
```

![](img/2fa15b462d2315fb03dd797f3990fd36_14.png)

### 数组间运算

![](img/2fa15b462d2315fb03dd797f3990fd36_16.png)

![](img/2fa15b462d2315fb03dd797f3990fd36_17.png)

两个形状相同的数组可以进行逐元素的加减乘除以及比较运算。

![](img/2fa15b462d2315fb03dd797f3990fd36_19.png)

```python
B = np.arange(10, 20)  # 创建另一个数组 [10, 11, 12, ..., 19]
print(A + B)  # 对应位置相加
print(A > B)  # 比较对应位置，返回布尔值数组
```
这种运算方式比手动编写循环更高效、代码更简洁。

![](img/2fa15b462d2315fb03dd797f3990fd36_21.png)

![](img/2fa15b462d2315fb03dd797f3990fd36_23.png)

## 数组的索引

![](img/2fa15b462d2315fb03dd797f3990fd36_25.png)

![](img/2fa15b462d2315fb03dd797f3990fd36_27.png)

索引用于访问数组中的单个元素。其规则与Python列表类似。

### 一维数组索引

![](img/2fa15b462d2315fb03dd797f3990fd36_29.png)

通过中括号和位置下标来访问元素，下标从0开始。

![](img/2fa15b462d2315fb03dd797f3990fd36_31.png)

```python
print(A[0])  # 访问第一个元素
print(A[5])  # 访问第六个元素
```

![](img/2fa15b462d2315fb03dd797f3990fd36_33.png)

### 二维数组索引

对于二维数组，索引需要先指定行，再指定列。有两种等价的写法。

```python
# 创建一个3行5列的二维数组
arr_2d = np.arange(15).reshape(3, 5)
print(arr_2d[0][0])  # 方法一：先取行，再取列
print(arr_2d[0, 0])  # 方法二：使用逗号分隔（推荐，尤其在后续pandas中常用）
print(arr_2d[2, 2])  # 访问第三行第三列的元素
```

![](img/2fa15b462d2315fb03dd797f3990fd36_35.png)

## 数组的切片

![](img/2fa15b462d2315fb03dd797f3990fd36_37.png)

切片用于获取数组的一个子集。NumPy数组的切片与列表有相似之处，但也有一个关键区别。

### 一维数组切片

切片语法为 `[start:stop:step]`，遵循“前闭后开”原则。

```python
C = A[0:4]  # 切片，获取索引0到3的元素 [0, 1, 2, 3]
print(C)
```

### 切片与视图（关键区别）

以下是NumPy数组切片与列表切片的核心区别。NumPy的切片默认创建的是原数组的一个**视图（view）**，而非副本。这意味着修改切片会影响到原数组。

```python
# 对比实验
list_B = list(range(10))  # 创建一个普通列表
array_A = np.arange(10)   # 创建一个NumPy数组

slice_of_list = list_B[0:4]
slice_of_array = array_A[0:4]

# 修改切片的值
slice_of_list[0] = 20
slice_of_array[0] = 20

print(“原列表 list_B:”, list_B)  # 输出：[20, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(“原数组 array_A:”, array_A) # 输出：[20, 1, 2, 3, 4, 5, 6, 7, 8, 9]
# 两者都被修改了，但原因不同。列表切片是浅拷贝的一种体现，而NumPy切片是视图。
```
**原因**：NumPy这样设计是为了处理大规模数据时节省内存和时间。如果你需要一份独立的副本，必须显式使用 `copy()` 方法。

```python
independent_slice = array_A[0:4].copy()
independent_slice[0] = 100
print(“独立切片:”, independent_slice)
print(“原数组 array_A:”, array_A)  # 原数组未被修改
```

### 二维数组切片

二维数组的切片功能更强大，使用逗号分隔行和列的切片范围。语法为 `[行切片, 列切片]`。

```python
arr_2d = np.arange(15).reshape(3, 5)
# 切出前两行、前三列的子数组
sub_array = arr_2d[0:2, 0:3]
print(sub_array)
```
以下是具体示例：
*   切出元素 `0, 1, 5, 6`：`arr_2d[0:2, 0:2]`
*   切出元素 `7, 8, 9, 12, 13, 14`：`arr_2d[1:3, 2:5]` 或 `arr_2d[1:, 2:]`

**记住规则**：**逗号左边是行切片，右边是列切片。**

## 总结

![](img/2fa15b462d2315fb03dd797f3990fd36_39.png)

本节课中我们一起学习了NumPy数组的核心操作：
1.  **快捷运算**：数组支持与标量及同形数组进行高效的逐元素运算。
2.  **索引**：使用 `arr[index]` 或 `arr[row, col]` 访问特定元素。
3.  **切片**：使用 `arr[start:stop:step]` 或 `arr[row_slice, col_slice]` 获取子集。
4.  **视图与副本**：NumPy切片默认创建**视图**，修改视图会影响原数组；使用 `.copy()` 方法可创建独立**副本**。

![](img/2fa15b462d2315fb03dd797f3990fd36_41.png)

![](img/2fa15b462d2315fb03dd797f3990fd36_43.png)

掌握这些索引和切片技巧是后续进行数据筛选、清洗和转换的基础。下一节，我们将探讨NumPy中更高级的数组操作。