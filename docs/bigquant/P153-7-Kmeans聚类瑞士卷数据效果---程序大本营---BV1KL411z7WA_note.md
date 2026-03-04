# 机器学习聚类算法：P153：7-Kmeans聚类瑞士卷数据效果 🍰

![](img/fad0694e7961fde9838880d74cce18d7_1.png)

![](img/fad0694e7961fde9838880d74cce18d7_3.png)

![](img/fad0694e7961fde9838880d74cce18d7_5.png)

在本节课中，我们将通过一个具体的例子，对比分层聚类与K-Means聚类在处理复杂数据（瑞士卷数据集）时的效果差异。我们将看到K-Means在处理具有特定流形结构的数据时存在的局限性。

上一节我们介绍了分层聚类的原理和参数，本节中我们来看看如何用代码实现并对比K-Means的效果。

## 创建瑞士卷数据集 📊

首先，我们需要导入必要的库并生成一个三维的瑞士卷数据集用于演示。

![](img/fad0694e7961fde9838880d74cce18d7_7.png)

![](img/fad0694e7961fde9838880d74cce18d7_9.png)

```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import AgglomerativeClustering
from sklearn.datasets import make_swiss_roll
from mpl_toolkits.mplot3d import Axes3D

![](img/fad0694e7961fde9838880d74cce18d7_11.png)

![](img/fad0694e7961fde9838880d74cce18d7_13.png)

# 生成瑞士卷数据
X, y = make_swiss_roll(n_samples=1500, noise=0.05)
print(np.unique(y))
print(len(y))

![](img/fad0694e7961fde9838880d74cce18d7_15.png)

# 创建3D视图展示原始数据
fig = plt.figure(figsize=(12, 9))
ax = fig.add_subplot(111, projection='3d')
ax.scatter(X[:, 0], X[:, 1], X[:, 2], c=y)
ax.view_init(10, -80)
plt.show()
```

![](img/fad0694e7961fde9838880d74cce18d7_17.png)

执行以上代码，我们会看到一个三维的瑞士卷形状数据点云。数据点`y`的标签是连续的数值，共有1500个不同的值，但在可视化中，相近的颜色代表数值接近的点。我们希望将数据沿着瑞士卷的螺旋方向进行分段聚类。

![](img/fad0694e7961fde9838880d74cce18d7_19.png)

![](img/fad0694e7961fde9838880d74cce18d7_21.png)

## 使用K-Means进行聚类 🔍

![](img/fad0694e7961fde9838880d74cce18d7_23.png)

![](img/fad0694e7961fde9838880d74cce18d7_25.png)

接下来，我们使用K-Means算法尝试对这个数据集进行聚类，并观察其效果。

```python
from sklearn.cluster import KMeans

# 使用K-Means聚类，假设分为6类
kmeans = KMeans(n_clusters=6)
y_kmeans = kmeans.fit_predict(X)

![](img/fad0694e7961fde9838880d74cce18d7_27.png)

# 可视化K-Means聚类结果
fig = plt.figure(figsize=(12, 9))
ax = fig.add_subplot(111, projection='3d')
ax.scatter(X[:, 0], X[:, 1], X[:, 2], c=y_kmeans)
ax.view_init(10, -80)
plt.show()
```

![](img/fad0694e7961fde9838880d74cce18d7_29.png)

![](img/fad0694e7961fde9838880d74cce18d7_31.png)

![](img/fad0694e7961fde9838880d74cce18d7_33.png)

观察K-Means的聚类结果，我们发现它并没有按照我们期望的“沿着瑞士卷螺旋方向分层”的方式进行划分。K-Means基于**距离**的聚类方式，导致它将空间上直线距离相近但属于不同螺旋层的点归为了同一类，出现了“跨层”现象。

![](img/fad0694e7961fde9838880d74cce18d7_35.png)

## 效果对比与分析 🤔

以下是K-Means聚类在此类数据上效果不佳的核心原因分析：

![](img/fad0694e7961fde9838880d74cce18d7_37.png)

![](img/fad0694e7961fde9838880d74cce18d7_39.png)

*   **算法原理限制**：K-Means的目标是最小化簇内样本到其质心的**欧氏距离平方和**。其公式表示为：
    `J = Σ_{i=1}^{k} Σ_{x∈C_i} ||x - μ_i||^2`
    其中`C_i`是第`i`个簇，`μ_i`是该簇的质心。对于瑞士卷这种在三维空间中弯曲、拉伸的数据（流形数据），欧氏距离并不能很好地反映数据点之间沿着流形结构的真实“邻近”关系。
*   **期望 vs 现实**：我们希望聚类能识别出数据内在的**流形结构**，即沿着瑞士卷表面将数据分成连续的几段。但K-Means只能识别出**凸形**的球形簇，因此它切割了瑞士卷，而不是沿着其表面进行划分。

![](img/fad0694e7961fde9838880d74cce18d7_41.png)

![](img/fad0694e7961fde9838880d74cce18d7_43.png)

本节课中我们一起学习了如何使用K-Means对复杂的瑞士卷数据集进行聚类，并分析了其效果不理想的原因。关键在于K-Means基于欧氏距离的聚类假设，无法有效处理具有非凸形状或流形结构的数据。这引出了我们对能够捕捉数据复杂结构（如分层聚类、DBSCAN、谱聚类等）的其他聚类算法的需求。