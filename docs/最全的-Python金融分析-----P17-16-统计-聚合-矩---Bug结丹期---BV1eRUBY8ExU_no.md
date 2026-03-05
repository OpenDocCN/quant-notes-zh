# Python金融分析+量化交易：P17：16 统计&聚合&矩阵操作

在本节课中，我们将要学习NumPy模块中关于数组形状变换、拼接、聚合统计以及矩阵运算的核心操作。这些内容是进行高效数值计算和数据分析的基础。

## 数组形状变换

上一节我们介绍了NumPy数组的基本操作，本节中我们来看看如何改变数组的形状。数组的`shape`属性描述了其维度结构，例如一个五行六列的二维数组。

我们可以使用`reshape`方法改变数组的形状。变形操作要求新形状的容器能容纳原数组的所有元素，数量必须一致。

以下是变形操作的示例：

```python
import numpy as np

# 原始二维数组，5行6列，共30个元素
arr = np.arange(30).reshape(5, 6)
print('原始数组形状：', arr.shape)

# 将二维数组变形为一维数组
arr_1d = arr.reshape(30)
print('一维数组：', arr_1d)

# 将一维数组变形为多维数组，例如2行15列
arr_2d_new = arr_1d.reshape(2, 15)
print('新二维数组形状：', arr_2d_new.shape)
```

## 数组拼接操作

接下来，我们学习如何将多个数组进行拼接。拼接操作可以将数组在横向（行方向）或纵向（列方向）上连接起来。

我们使用`np.concatenate`函数进行拼接，并通过`axis`参数指定拼接方向。`axis=0`表示纵向拼接（按列），`axis=1`表示横向拼接（按行）。需要注意的是，拼接的数组必须具有相同的维度。

以下是拼接操作的示例：

```python
# 创建两个相同的二维数组
arr1 = np.arange(30).reshape(5, 6)
arr2 = np.arange(30, 60).reshape(5, 6)

# 纵向拼接（按列）
v_stack = np.concatenate((arr1, arr2), axis=0)
print('纵向拼接后形状：', v_stack.shape)

# 横向拼接（按行）
h_stack = np.concatenate((arr1, arr2), axis=1)
print('横向拼接后形状：', h_stack.shape)
```

> **思考**：如果两个数组维度相同，但行数或列数不同，能否进行拼接？结果会如何？请自行尝试。

## 聚合统计操作

现在，我们来看看NumPy中常用的聚合统计操作。这些操作可以对数组的整体或沿某个轴向进行数学计算。

以下是常用的聚合函数：
*   `sum()`: 计算所有元素或指定轴向的和。
*   `max()`: 找出所有元素或指定轴向的最大值。
*   `min()`: 找出所有元素或指定轴向的最小值。
*   `mean()`: 计算所有元素或指定轴向的平均值。

```python
arr = np.arange(30).reshape(5, 6)

# 计算所有元素的和
total_sum = arr.sum()
print('所有元素和：', total_sum)

# 计算每一列的和
col_sum = arr.sum(axis=0)
print('每列的和：', col_sum)

# 找出每一行的最大值
row_max = arr.max(axis=1)
print('每行的最大值：', row_max)
```

## 常用数学与统计函数

除了聚合操作，NumPy还提供了丰富的数学函数和统计函数。

以下是部分常用函数：
*   `np.sin()`, `np.cos()`, `np.tan()`: 计算三角函数。
*   `np.around()`: 对数组元素进行四舍五入。
*   `np.std()`: 计算标准差，衡量数据的离散程度。
*   `np.var()`: 计算方差，标准差的平方。

标准差的计算公式为：
**σ = √[ Σ(xi - μ)² / N ]**
其中，σ是标准差，xi是每个数据点，μ是数据的平均值，N是数据点的数量。

```python
arr = np.array([1, 2, 3, 4, 5, 100]) # 一组数据

# 计算正弦值
sin_values = np.sin(arr)
print('正弦值：', sin_values)

# 四舍五入
rounded = np.around(3.14159, decimals=2)
print('四舍五入：', rounded)

# 计算标准差和方差
data_std = np.std(arr)
data_var = np.var(arr)
print('标准差：', data_std)
print('方差：', data_var)
```

## 矩阵运算

![](img/99814718b88cbcabc173ee00b9c9d72e_1.png)

最后，我们学习NumPy中的矩阵运算。在线性代数和机器学习中，矩阵乘法是核心操作。

首先，我们可以创建特殊的矩阵，如单位矩阵，并使用`.T`属性进行转置操作（行变列，列变行）。

```python
# 创建3x3的单位矩阵
identity_matrix = np.eye(3)
print('单位矩阵：\n', identity_matrix)

![](img/99814718b88cbcabc173ee00b9c9d72e_3.png)

# 矩阵转置
arr = np.array([[1, 2], [3, 4]])
arr_transpose = arr.T
print('原矩阵：\n', arr)
print('转置矩阵：\n', arr_transpose)
```

矩阵乘法的规则是：第一个矩阵的行与第二个矩阵的列对应元素相乘后求和，得到结果矩阵中对应位置的元素。

我们使用`np.dot()`函数或`@`运算符进行矩阵乘法。

```python
# 定义两个矩阵
A = np.array([[2, 1],
              [4, 3]])
B = np.array([[1, 2],
              [1, 0]])

![](img/99814718b88cbcabc173ee00b9c9d72e_5.png)

# 矩阵乘法
C = np.dot(A, B)
# 等价于 C = A @ B
print('矩阵乘法结果：\n', C)
```
结果`C`的计算过程为：
*   C[0,0] = 2*1 + 1*1 = 3
*   C[0,1] = 2*2 + 1*0 = 4
*   C[1,0] = 4*1 + 3*1 = 7
*   C[1,1] = 4*2 + 3*0 = 8

![](img/99814718b88cbcabc173ee00b9c9d72e_7.png)

---

本节课中我们一起学习了NumPy模块的几个高级主题：
1.  **数组变形**：使用`reshape`方法改变数组维度，需确保元素总数不变。
2.  **数组拼接**：使用`np.concatenate`函数沿指定轴拼接多个数组。
3.  **聚合统计**：掌握`sum`、`max`、`min`、`mean`等函数对数组进行快速计算。
4.  **数学与统计函数**：了解`sin`、`around`、`std`、`var`等常用函数。
5.  **矩阵运算**：理解矩阵转置（`.T`）和矩阵乘法（`np.dot`）的原理与应用。

![](img/99814718b88cbcabc173ee00b9c9d72e_9.png)

这些操作是进行科学计算、数据分析和机器学习模型构建的基石，务必熟练掌握。