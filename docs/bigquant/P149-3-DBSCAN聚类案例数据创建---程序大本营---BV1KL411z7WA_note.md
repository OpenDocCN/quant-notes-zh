# 机器学习算法实战：P149：3-DBSCAN聚类案例数据创建 📊

![](img/0826f2ddf2a496892fe802109e98a166_1.png)

![](img/0826f2ddf2a496892fe802109e98a166_3.png)

在本节课中，我们将学习如何为DBSCAN聚类算法创建和准备一个示例数据集。我们将使用`sklearn`库中的工具生成模拟数据，并将它们合并成一个包含三类、共1500个点的数据集，为后续的聚类分析做好准备。

---

## 数据创建步骤

上一节我们介绍了DBSCAN算法的基本原理，本节中我们来看看如何用代码创建符合算法测试需求的数据。

### 导入必要库

![](img/0826f2ddf2a496892fe802109e98a166_5.png)

首先，我们需要导入用于生成数据和绘图的库。

![](img/0826f2ddf2a496892fe802109e98a166_7.png)

```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn import datasets
```

### 生成环形数据

![](img/0826f2ddf2a496892fe802109e98a166_9.png)

我们将使用`make_circles`方法生成第一组数据，它包含两个同心圆环。

![](img/0826f2ddf2a496892fe802109e98a166_11.png)

以下是生成环形数据的代码：

```python
# 生成环形数据
X, y = datasets.make_circles(n_samples=1000, noise=0.05, factor=0.5)

# 绘制数据
plt.figure(figsize=(5, 5))
plt.scatter(X[:, 0], X[:, 1], c=y)
plt.show()
```

**代码解释**：
*   `n_samples=1000`：生成1000个数据点。
*   `noise=0.05`：为数据点添加少量噪声，使分布更接近真实情况。
*   `factor=0.5`：控制内圈与外圈的比例。
*   生成的`y`是标签，这里包含两类（0和1）。

### 生成团状数据

接下来，我们使用`make_blobs`方法生成第二组数据，它是一个聚集在特定中心点的团状数据。

以下是生成团状数据的代码：

![](img/0826f2ddf2a496892fe802109e98a166_13.png)

![](img/0826f2ddf2a496892fe802109e98a166_15.png)

```python
# 生成团状数据
X1, y1 = datasets.make_blobs(n_samples=500, n_features=2, centers=[[1.5, 1.5]], cluster_std=0.2)

# 绘制数据
plt.figure(figsize=(5, 5))
plt.scatter(X1[:, 0], X1[:, 1], c=y1)
plt.show()
```

**代码解释**：
*   `n_samples=500`：生成500个数据点。
*   `n_features=2`：每个点有两个特征（横纵坐标）。
*   `centers=[[1.5, 1.5]]`：指定数据簇的中心点坐标为(1.5, 1.5)。参数需要是列表或数组形式。
*   `cluster_std=0.2`：控制数据点围绕中心的离散程度（标准差）。
*   生成的`y1`标签全是0，代表这是单独的一类。

![](img/0826f2ddf2a496892fe802109e98a166_17.png)

### 合并数据集

![](img/0826f2ddf2a496892fe802109e98a166_19.png)

现在，我们需要将生成的两组数据合并成一个完整的数据集，并确保类别标签正确。

![](img/0826f2ddf2a496892fe802109e98a166_21.png)

以下是合并数据集的步骤：

![](img/0826f2ddf2a496892fe802109e98a166_23.png)

![](img/0826f2ddf2a496892fe802109e98a166_25.png)

1.  **合并特征数据**：使用`np.concatenate`将`X`和`X1`在行方向（样本数）上拼接。
2.  **合并并调整标签**：将`y`和`y1`拼接，同时将`y1`的标签值统一加2，以避免与第一组数据的标签（0,1）冲突。

```python
# 合并特征数据X
X_combined = np.concatenate([X, X1], axis=0)
print("合并后X的形状：", X_combined.shape)  # 应输出 (1500, 2)

# 合并并调整标签y
y_combined = np.concatenate([y, y1 + 2])
print("合并后y的形状：", y_combined.shape)  # 应输出 (1500,)
print("y中的唯一值：", np.unique(y_combined))  # 应输出 [0 1 2]
```

### 可视化最终数据集

最后，我们将合并后的数据集可视化，检查数据分布和类别划分是否清晰。

```python
# 绘制最终合并的数据集
plt.figure(figsize=(6, 6))
plt.scatter(X_combined[:, 0], X_combined[:, 1], c=y_combined)
plt.title("合并后的数据集（共3类，1500个点）")
plt.show()
```

执行后，你将看到一幅包含三类数据的散点图：一个内圈、一个外圈和一个位于右上角的团状数据簇。

---

## 总结

![](img/0826f2ddf2a496892fe802109e98a166_27.png)

本节课中我们一起学习了如何为聚类案例创建合成数据集。我们首先使用`make_circles`生成了环形分布的数据，然后使用`make_blobs`生成了团状分布的数据。最后，我们通过`np.concatenate`将两组数据合并，并通过调整标签值确保了数据集中包含三个清晰的类别（标签0，1，2）。这个数据集将作为下一节我们应用DBSCAN聚类算法的输入数据。