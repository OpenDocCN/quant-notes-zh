# 机器学习降维算法：P169-2：LLE算法代码实践 🧩

在本节课中，我们将学习如何使用局部线性嵌入算法对具有流行结构的数据进行降维，并与主成分分析算法的降维效果进行对比。

![](img/31b691a239b6a2e2921016755cf1b209_1.png)

![](img/31b691a239b6a2e2921016755cf1b209_3.png)

---

![](img/31b691a239b6a2e2921016755cf1b209_5.png)

## 导入必要的库 📦

首先，我们需要导入实现算法和可视化所需的库。

![](img/31b691a239b6a2e2921016755cf1b209_7.png)

```python
from sklearn.manifold import LocallyLinearEmbedding
import numpy as np
from sklearn import datasets
import matplotlib.pyplot as plt
from sklearn.decomposition import PCA
from mpl_toolkits.mplot3d import Axes3D
```

上一节我们介绍了LLE算法的基本概念，本节中我们来看看如何在代码中应用它。

---

## 创建数据：瑞士卷数据集 🍰

我们将使用`sklearn`生成一个具有流行结构的“瑞士卷”数据集，以便观察不同降维算法的效果。

```python
# 生成瑞士卷数据
n_samples = 1500
X, y = datasets.make_swiss_roll(n_samples=n_samples, noise=0.05, random_state=1024)

# 可视化原始三维数据
fig = plt.figure(figsize=(12, 9))
ax = fig.add_subplot(projection='3d')
ax.scatter(X[:, 0], X[:, 1], X[:, 2], c=y)
ax.view_init(7, -80)
plt.show()
```

执行以上代码后，我们将看到一个三维的瑞士卷形状数据点图，不同颜色代表数据的不同类别或位置。

---

## 使用PCA进行降维 🔍

在应用LLE之前，我们先使用之前学过的PCA算法对数据进行降维，以便后续对比。

```python
# 使用PCA降维至2维
pca = PCA(n_components=2)
X_pca = pca.fit_transform(X)

# 查看降维后数据的形状
print("PCA降维后数据形状:", X_pca.shape)

# 可视化PCA降维结果
plt.scatter(X_pca[:, 0], X_pca[:, 1], c=y)
plt.title("PCA降维结果")
plt.show()
```

PCA通过线性投影进行降维。从结果图中我们可以看到，三维的螺旋结构被投影到二维平面上，但数据点在某些区域出现了堆叠，未能完全展开数据的内在结构。

---

## 使用LLE进行降维 🧠

接下来，我们使用本节课的核心——局部线性嵌入算法对同一数据集进行降维。

以下是LLE算法的关键步骤在代码中的体现：
1.  为每个数据点寻找**k个最近邻**。
2.  基于这些邻居**重建该点的线性关系**（计算权重矩阵W）。
3.  在低维空间中**保持这些重建权重不变**，求解新的数据点坐标。

```python
# 使用LLE降维至2维
lle = LocallyLinearEmbedding(n_neighbors=10, n_components=2)
X_lle = lle.fit_transform(X)

# 查看降维后数据的形状
print("LLE降维后数据形状:", X_lle.shape)

# 可视化LLE降维结果
plt.scatter(X_lle[:, 0], X_lle[:, 1], c=y)
plt.title("LLE降维结果")
plt.show()
```

参数说明：
*   `n_neighbors=10`：为每个点寻找10个最近邻。
*   `n_components=2`：将数据降至二维。

---

## 算法效果对比与分析 📊

通过对比PCA和LLE的降维结果，我们可以得出以下结论：

以下是两种算法的主要区别：
*   **PCA（主成分分析）**：作为一种**线性**降维方法，它寻找数据方差最大的方向进行投影。对于瑞士卷这种**非线性流行结构**，PCA难以捕捉其内在的几何关系，导致降维后数据依然缠绕，不同部分（如紫色与黄色区域）在二维平面上距离很近。
*   **LLE（局部线性嵌入）**：作为一种**非线性**降维方法，它专注于保持每个数据点与其邻居之间的局部线性关系。因此，它能将瑞士卷“展开”成一个二维平面，使得在原始三维空间中距离较远的点（如紫色与黄色区域），在二维结果中也保持了较远的距离，更好地揭示了数据的本质结构。

简而言之，**LLE通过保持局部结构实现了对非线性数据的有效展开**。

---

![](img/31b691a239b6a2e2921016755cf1b209_9.png)

本节课中我们一起学习了如何使用代码实现局部线性嵌入算法，并将其应用于瑞士卷数据集。通过对比PCA的降维结果，我们直观地理解了LLE算法在处理非线性流行数据时的优势——它能够有效地展开数据，保持其内在的局部几何结构，从而获得更易于分析的低维表示。