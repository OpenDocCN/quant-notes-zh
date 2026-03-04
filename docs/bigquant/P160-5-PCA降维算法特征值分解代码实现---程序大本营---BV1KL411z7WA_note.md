# PCA降维算法：P160：特征值分解代码实现 📊

![](img/389ecd790fbd87ab8df97926b305dae6_1.png)

在本节课中，我们将学习主成分分析（PCA）降维算法的核心原理，并通过特征值分解的方式，一步步用代码实现它。我们将从基础概念入手，逐步拆解PCA的计算过程，最终理解其背后的数学逻辑。

---

![](img/389ecd790fbd87ab8df97926b305dae6_3.png)

## 概述

PCA是一种常用的数据降维技术，其核心思想是通过线性变换，将原始数据映射到一个新的坐标系中，使得数据在新坐标系的第一维（第一主成分）上具有最大的方差，第二维上次之，以此类推。本节课我们将通过代码实现，深入理解PCA的每一步计算。

---

## PCA降维原理与步骤

上一节我们介绍了PCA的基本概念，本节中我们来看看其具体的数学实现步骤。PCA降维主要包含以下六个核心步骤：

![](img/389ecd790fbd87ab8df97926b305dae6_5.png)

1.  **去中心化**：将每个特征减去其平均值，使数据分布以原点为中心。
2.  **计算协方差矩阵**：衡量不同特征之间的相关性。
3.  **计算特征值与特征向量**：对协方差矩阵进行特征值分解。
4.  **筛选主成分**：根据特征值大小，选择最重要的k个特征向量。
5.  **矩阵乘法（数据转换）**：将原始数据投影到选定的特征向量构成的新空间。
6.  **标准化（可选）**：对降维后的数据进行标准化处理。

接下来，我们将逐一用代码实现这些步骤。

![](img/389ecd790fbd87ab8df97926b305dae6_7.png)

![](img/389ecd790fbd87ab8df97926b305dae6_9.png)

---

![](img/389ecd790fbd87ab8df97926b305dae6_11.png)

### 第一步：去中心化

去中心化是PCA的第一步，目的是让数据的均值为零，便于后续计算协方差。其操作是数据矩阵的每一列减去该列的均值。

以下是去中心化的代码实现：

```python
import numpy as np

# 假设 X 是原始数据矩阵，形状为 (n_samples, n_features)
# 1. 计算每个特征的均值
mean_vals = np.mean(X, axis=0)
# 2. 每个特征减去其均值
X_centered = X - mean_vals
```

执行后，`X_centered` 的每一列均值都接近于0。数据有了正负之分，分布更接近以原点为中心。

---

### 第二步：计算协方差矩阵

协方差矩阵描述了数据不同特征之间的线性相关程度。方差是衡量一个特征自身离散度的指标，而协方差是衡量两个特征一起变化的趋势。

方差公式为：
\[
\text{Var}(X) = \frac{1}{n-1} \sum_{i=1}^{n} (x_i - \bar{x})^2
\]

协方差公式为：
\[
\text{Cov}(X, Y) = \frac{1}{n-1} \sum_{i=1}^{n} (x_i - \bar{x})(y_i - \bar{y})
\]

在NumPy中，我们可以直接计算协方差矩阵：

```python
# 计算协方差矩阵，rowvar=False 表示每列代表一个特征
cov_matrix = np.cov(X_centered, rowvar=False, bias=True)
```
参数 `bias=True` 会影响分母是 `n` 还是 `n-1`，为了与某些库的实现保持一致，这里设为 `True`。

![](img/389ecd790fbd87ab8df97926b305dae6_13.png)

![](img/389ecd790fbd87ab8df97926b305dae6_15.png)

---

### 第三步：计算特征值与特征向量

协方差矩阵的特征值和特征向量是PCA的核心。特征值的大小代表了对应特征向量方向上数据方差的大小。特征值越大，该方向上的信息量（重要性）就越大。

我们可以使用线性代数库进行计算：

```python
# 计算协方差矩阵的特征值和特征向量
eigenvalues, eigenvectors = np.linalg.eig(cov_matrix)
```
`eigenvalues` 是特征值数组，`eigenvectors` 的每一列是对应的特征向量。

---

### 第四步：筛选主成分

并非所有的主成分都需要保留。我们通常根据累计贡献率来选择前k个主成分，例如保留95%的原始信息。

以下是筛选步骤：

1.  计算每个特征值的贡献率（重要性比例）。
2.  计算贡献率的累计和。
3.  找到累计贡献率首次超过目标阈值（如0.95）的位置。

```python
# 1. 计算贡献率
explained_variance_ratio = eigenvalues / np.sum(eigenvalues)
# 2. 计算累计贡献率
cumulative_variance_ratio = np.cumsum(explained_variance_ratio)
# 3. 找到满足条件的最小k值
target_variance = 0.95
# 找到第一个累计贡献率 >= 目标值的位置索引
n_components = np.argmax(cumulative_variance_ratio >= target_variance) + 1
# 4. 选取前k个最重要的特征向量
top_eigenvectors = eigenvectors[:, :n_components]
```

`np.argmax` 在这里用于找到第一个为 `True` 的索引，加1是因为切片操作 `[:k]` 包含前k个。

---

### 第五步：矩阵乘法（数据转换）

![](img/389ecd790fbd87ab8df97926b305dae6_17.png)

这一步是将中心化后的原始数据，投影到我们筛选出的主成分（特征向量）所张成的新低维空间中。这通过矩阵乘法实现。

```python
# 将数据转换到新的特征空间
X_pca = np.dot(X_centered, top_eigenvectors)
```
`X_pca` 就是降维后的新数据矩阵。这个矩阵乘法操作，在几何上就是将数据点映射到新的坐标轴上。

---

![](img/389ecd790fbd87ab8df97926b305dae6_19.png)

### 第六步：标准化（可选）

![](img/389ecd790fbd87ab8df97926b305dae6_21.png)

![](img/389ecd790fbd87ab8df97926b305dae6_23.png)

某些情况下，我们需要对降维后的数据进行标准化（例如Z-score标准化），使其均值为0，标准差为1。这对应于PCA中 `whiten=True` 参数的效果。

![](img/389ecd790fbd87ab8df97926b305dae6_25.png)

```python
# Z-score 标准化
X_pca_whitened = (X_pca - np.mean(X_pca, axis=0)) / np.std(X_pca, axis=0, ddof=1)
```
注意 `np.std` 中的参数 `ddof=1`，这表示计算样本标准差时分母使用 `n-1`，与 `np.cov(bias=False)` 的设定相匹配，确保结果的一致性。

---

![](img/389ecd790fbd87ab8df97926b305dae6_27.png)

## 总结

![](img/389ecd790fbd87ab8df97926b305dae6_29.png)

本节课中我们一起学习了PCA降维算法基于特征值分解的完整实现过程。我们从**去中心化**开始，计算了数据的**协方差矩阵**，然后对其进行**特征值分解**得到特征值和特征向量。接着，我们根据特征值的**贡献率**筛选出最重要的主成分，并通过**矩阵乘法**将原始数据转换到新的低维空间。最后，我们探讨了可选的**标准化**步骤。

![](img/389ecd790fbd87ab8df97926b305dae6_31.png)

通过这六个步骤的手动实现，我们揭开了PCA算法的“黑箱”，理解了其本质是通过线性代数变换，找到数据方差最大的方向并进行投影。这二三十行代码的核心逻辑，正是 `sklearn.decomposition.PCA` 类封装的功能。掌握这一实现，不仅有助于深入理解PCA，也能在需要时自定义降维过程，或在面试中清晰阐述其原理。