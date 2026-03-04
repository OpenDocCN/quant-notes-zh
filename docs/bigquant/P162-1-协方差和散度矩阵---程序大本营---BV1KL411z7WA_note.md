# PCA降维算法进阶：P162：1-协方差和散度矩阵 📊

![](img/11838706d629143ec5a7a39978b49d6c_1.png)

![](img/11838706d629143ec5a7a39978b49d6c_3.png)

在本节课中，我们将要学习PCA降维算法中两个核心的数学概念：**协方差**和**散度矩阵**。我们将通过代码演示和公式推导，清晰地理解它们的定义、计算方法以及两者之间的关系。

## 协方差矩阵

上一节我们介绍了均值和方差的基本概念，本节中我们来看看协方差。协方差用于衡量两个特征（变量）之间的线性关系。其计算公式如下：

**公式：** `cov(X, Y) = Σ[(xi - μx) * (yi - μy)] / (n - 1)`

其中，`xi` 和 `yi` 是样本点，`μx` 和 `μy` 是特征X和Y的均值，`n` 是样本数量。分母使用 `n-1` 时，计算的是**样本协方差**，这是一种无偏估计。

在代码中，我们可以使用NumPy的 `np.cov` 函数来计算协方差矩阵。

```python
import numpy as np

# 生成一个5行5列的随机数据矩阵
x = np.random.randint(0, 100, size=(5, 5))
print(“原始数据 x:\n”, x)

# 计算协方差矩阵，rowvar=False 表示将每一列视为一个特征（变量）
cov_matrix = np.cov(x, rowvar=False)
print(“协方差矩阵 (rowvar=False):\n”, cov_matrix)
```

执行上述代码，会得到一个5x5的协方差矩阵。这个矩阵对角线上的元素是每个特征自身的方差，非对角线上的元素是不同特征之间的协方差。

**方差是协方差的特例**，即当计算一个特征与其自身的协方差时，得到的结果就是该特征的方差。这类似于圆是椭圆的一种特殊形式。

为了加深理解，我们可以手动计算第一列的方差，并与协方差矩阵中的结果进行对比。

以下是手动计算样本方差的步骤：

```python
# 手动计算第一列（索引0）的样本方差
col_0 = x[:, 0]
# 方法1：使用np.var，并设置ddof=1（delta degrees of freedom）
var_np = np.var(col_0, ddof=1)
print(“NumPy计算的样本方差:”, var_np)

# 方法2：根据方差公式手动计算
mean_col_0 = np.mean(col_0)
n = len(col_0)
var_manual = np.sum((col_0 - mean_col_0) ** 2) / (n - 1)
print(“手动计算的样本方差:”, var_manual)
```

同样，我们可以手动计算第一列和第二列之间的协方差。

以下是手动计算协方差的代码：

```python
# 手动计算第一列和第二列之间的协方差
col_0 = x[:, 0]
col_1 = x[:, 1]
mean_col_0 = np.mean(col_0)
mean_col_1 = np.mean(col_1)
n = len(col_0)

cov_manual = np.sum((col_0 - mean_col_0) * (col_1 - mean_col_1)) / (n - 1)
print(“手动计算的协方差:”, cov_manual)

# 使用np.cov验证
cov_np = np.cov(col_0, col_1, ddof=1)[0, 1] # 取非对角线元素
print(“NumPy计算的协方差:”, cov_np)
```

通过对比，可以确认我们的理解和计算是正确的。

## 散度矩阵

理解了协方差之后，本节我们来看看另一个相关概念——散度矩阵。散度矩阵（Scatter Matrix）是计算协方差矩阵的基础，两者存在倍数关系。

散度矩阵 `S` 的定义公式为：

**公式：** `S = Σ (xk - m) * (xk - m)^T`

其中，`xk` 是一个样本向量（一行数据），`m` 是所有样本的均值向量，上标 `T` 表示转置。求和是对所有样本 `k` 进行的。

接下来，我们在代码中计算数据 `x` 的散度矩阵。

以下是计算散度矩阵的步骤：

```python
# 计算散度矩阵
# 1. 计算均值向量m（按行求平均，即每个特征的均值）
m = x.mean(axis=0) # axis=0 对每一列（特征）求平均
print(“均值向量 m:\n”, m)

# 2. 计算每个样本与均值向量的差值
b = x - m # 利用NumPy的广播机制
print(“差值矩阵 b:\n”, b)

# 3. 计算散度矩阵 S = b^T * b （注意维度）
# 这里b的每一行是一个样本，需要转置后相乘
s = np.dot(b.T, b)
print(“散度矩阵 S:\n”, s)
```

## 协方差矩阵与散度矩阵的关系

现在，我们已经分别计算出了协方差矩阵和散度矩阵。那么它们之间有什么关系呢？

**核心关系是：协方差矩阵 = 散度矩阵 / (n - 1)**

其中 `n` 是样本数量。让我们在代码中验证这个关系。

以下是验证两者关系的代码：

```python
# 验证关系：协方差矩阵 = 散度矩阵 / (n - 1)
n = x.shape[0] # 样本行数
cov_from_scatter = s / (n - 1)
print(“由散度矩阵除以(n-1)得到的矩阵:\n”, cov_from_scatter)
print(“直接计算的协方差矩阵:\n”, cov_matrix)
# 判断两个矩阵是否在允许误差内相等
print(“两者是否近似相等:”, np.allclose(cov_from_scatter, cov_matrix))
```

关于 `np.cov` 中 `rowvar` 参数的进一步说明：
*   `rowvar=True`（默认）：将每一行视为一个特征（变量），计算行与行之间的协方差。
*   `rowvar=False`：将每一列视为一个特征（变量），计算列与列之间的协方差。在数据科学中，通常每一列代表一个特征，所以更常用 `rowvar=False`。

如果使用 `rowvar=True` 计算协方差，那么在计算对应的散度矩阵时，需要对数据进行转置，并调整均值的计算轴。

以下是当 `rowvar=True` 时的计算示例：

```python
# 当 rowvar=True 时，计算散度矩阵的对应方式
# 计算行的协方差矩阵
cov_matrix_rows = np.cov(x, rowvar=True)
print(“行协方差矩阵 (rowvar=True):\n”, cov_matrix_rows)

# 计算对应的散度矩阵
# 此时，应将每一行视为一个样本，计算行方向的均值
m_rows = x.mean(axis=1) # axis=1 对每一行求平均
# 为了进行减法广播，需要将均值向量重塑为列向量
m_rows_reshaped = m_rows.reshape(-1, 1)
b_rows = x - m_rows_reshaped
# 散度矩阵 S = b * b^T
s_rows = np.dot(b_rows, b_rows.T)
print(“对应的散度矩阵:\n”, s_rows)
# 验证关系
cov_calculated_from_s_rows = s_rows / (x.shape[1] - 1) # 注意此时除以的是(特征数-1)
print(“由散度矩阵推导的协方差矩阵:\n”, cov_calculated_from_s_rows)
```

## 总结

本节课中我们一起学习了PCA降维中两个关键的数学工具：

1.  **协方差矩阵**：用于描述数据集中各特征之间的线性相关关系。对角线是方差，非对角线是协方差。可以使用 `np.cov()` 函数方便计算。
2.  **散度矩阵**：是计算协方差矩阵的中间步骤，体现了数据点围绕均值的散布情况。其计算公式为 `S = Σ (x - μ)(x - μ)^T`。
3.  **核心关系**：对于常见的按列表示特征的数据格式（`rowvar=False`），**协方差矩阵等于散度矩阵除以 `(n-1)`**，即 `Cov = S / (n-1)`。这揭示了二者本质上的联系。

![](img/11838706d629143ec5a7a39978b49d6c_5.png)

理解协方差和散度矩阵，是掌握PCA降维原理的重要基础，因为PCA的核心步骤之一就是计算数据的协方差矩阵并对其进行特征分解。