# Python三剑客：P7：01 numpy剑客修炼_06 统计&聚合&矩阵操作 📊

在本节课中，我们将要学习NumPy模块中关于数组形状变换、拼接、聚合统计以及矩阵运算的核心操作。这些功能是进行数据分析和科学计算的基础。

## 数组形状变换

上一节我们介绍了数组的索引与切片，本节中我们来看看如何改变数组的形状。数组的`shape`属性描述了其维度结构，例如一个五行六列的二维数组。我们可以使用`reshape`方法改变数组的形状，但新形状必须能容纳原数组的所有元素。

**代码示例：**
```python
import numpy as np
# 原始二维数组
arr = np.arange(30).reshape(5, 6)
print("原始形状：", arr.shape)  # 输出：(5, 6)

# 将二维数组变形为一维数组
arr_1d = arr.reshape(30)
print("一维形状：", arr_1d.shape)  # 输出：(30,)

# 将一维数组变形为多维数组，例如两行十五列
arr_2d_new = arr_1d.reshape(2, 15)
print("新二维形状：", arr_2d_new.shape)  # 输出：(2, 15)
```
变形操作要求新形状的元素总数必须与原数组一致，否则会报错。在基础数据分析中，变形操作不常用，但需要了解其存在。

## 数组拼接操作

接下来，我们学习如何将多个数组拼接在一起。拼接操作可以将数组在横向（按行）或纵向（按列）进行连接，使用`np.concatenate`函数实现。

**代码示例：**
```python
# 创建两个相同的二维数组
arr1 = np.arange(30).reshape(5, 6)
arr2 = np.arange(30, 60).reshape(5, 6)

# 纵向拼接（按列，axis=0）
v_stack = np.concatenate((arr1, arr2), axis=0)
print("纵向拼接后形状：", v_stack.shape)  # 输出：(10, 6)

# 横向拼接（按行，axis=1）
h_stack = np.concatenate((arr1, arr2), axis=1)
print("横向拼接后形状：", h_stack.shape)  # 输出：(5, 12)
```
以下是关于拼接的几点说明：
*   `axis`参数决定拼接方向：`axis=0`表示纵向（沿行方向），`axis=1`表示横向（沿列方向）。
*   只能对**维度相同**的数组进行拼接。
*   如果尝试拼接维度相同但行或列数不一致的数组，操作会失败。例如，一个五行三列的数组与一个五行四列的数组无法在`axis=1`（横向）上拼接。

拼接操作的一个典型应用是图像处理，例如将多张图片拼接成九宫格。

## 聚合统计操作

从聚合操作开始，我们将学习NumPy中封装好的数值运算方法。聚合函数可以对整个数组或沿特定轴（行或列）进行计算。

**代码示例：**
```python
arr = np.arange(30).reshape(5, 6)

# 计算所有元素的和、最大值、最小值、平均值
total_sum = arr.sum()
max_value = arr.max()
min_value = arr.min()
mean_value = arr.mean()
print(f"总和：{total_sum}, 最大值：{max_value}, 最小值：{min_value}, 平均值：{mean_value}")

# 计算每一列的和（axis=0）
col_sum = arr.sum(axis=0)
print("每列的和：", col_sum)

# 计算每一行的最大值（axis=1）
row_max = arr.max(axis=1)
print("每行的最大值：", row_max)
```
常用的聚合函数包括`sum()`, `max()`, `min()`, `mean()`。通过指定`axis`参数，可以灵活地按行或列进行统计。

## 常用数学与统计函数

NumPy提供了丰富的数学函数，可以对数组中的每个元素进行运算。

**代码示例：**
```python
arr = np.arange(30).reshape(5, 6)

# 三角函数：计算每个元素的正弦值
sin_arr = np.sin(arr)

# 四舍五入：np.around
rounded = np.around(3.14159, decimals=2)  # 保留两位小数
print("四舍五入结果：", rounded)  # 输出：3.14

# 极差：计算最大值与最小值的差
range_val = np.ptp(arr, axis=0)  # 计算每列的极差
print("每列的极差：", range_val)
```

在统计函数中，**方差**和**标准差**尤为重要，它们用于衡量一组数据的离散程度。
*   **方差**：每个数据与平均值之差的平方的平均数。
*   **标准差**：方差的平方根，单位与原数据一致。

**公式：**
*   方差公式：$ \sigma^2 = \frac{1}{N} \sum_{i=1}^{N} (x_i - \mu)^2 $
*   标准差公式：$ \sigma = \sqrt{\frac{1}{N} \sum_{i=1}^{N} (x_i - \mu)^2} $

**代码示例：**
```python
data = np.array([85, 90, 78, 92, 88])

![](img/20bd179ec2265f28dccfda244ee73335_1.png)

# 计算方差
variance = np.var(data)
print("方差：", variance)

# 计算标准差
std_dev = np.std(data)
print("标准差：", std_dev)
```
标准差越大，说明数据波动越大，分布越分散。

![](img/20bd179ec2265f28dccfda244ee73335_3.png)

## 矩阵运算

最后，我们学习矩阵运算，这在机器学习和线性代数中应用广泛。矩阵可以看作是一种特殊的二维数组。

**代码示例：**
```python
# 创建矩阵（二维数组）
A = np.array([[2, 1], [4, 3]])
B = np.array([[1, 2], [1, 0]])

# 矩阵转置：行变列，列变行
A_T = A.T
print("A的转置：\n", A_T)

![](img/20bd179ec2265f28dccfda244ee73335_5.png)

# 矩阵乘法：使用 np.dot 或 @ 运算符
# 结果 C[i][j] = sum(A[i][:] * B[:][j])
C = np.dot(A, B)
# 等价于 C = A @ B
print("矩阵乘法结果 A x B：\n", C)
```
矩阵乘法`C = A x B`的规则是：结果矩阵`C`中第`i`行第`j`列的元素，等于矩阵`A`的第`i`行与矩阵`B`的第`j`列对应元素乘积之和。

![](img/20bd179ec2265f28dccfda244ee73335_7.png)

---

![](img/20bd179ec2265f28dccfda244ee73335_9.png)

本节课中我们一起学习了NumPy模块的几个高级主题：通过`reshape`改变数组形状；使用`concatenate`进行数组拼接；利用`sum`、`max`、`var`、`std`等函数进行聚合与统计计算；最后掌握了矩阵的转置与乘法运算。理解这些操作是运用NumPy进行高效数据分析的关键。