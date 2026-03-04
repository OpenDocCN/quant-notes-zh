# 机器学习：P154：分层聚类瑞士卷数据效果

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_0.png)

在本节课中，我们将学习如何使用分层聚类算法处理瑞士卷数据，并探讨连接性约束如何帮助算法识别数据的流形结构，避免跨层聚类。

## 分层聚类基础应用

上一节我们介绍了瑞士卷数据的特点，本节中我们来看看如何使用分层聚类算法对其进行聚类。

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_2.png)

我们首先声明一个分层聚类模型，并设置聚类数量为6。`linkage`参数用于定义簇间的距离计算方式，这里我们选择`ward`方法，该方法旨在最小化合并后新簇的方差，即倾向于合并那些内部点距离较近的簇。

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_4.png)

```python
from sklearn.cluster import AgglomerativeClustering

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_6.png)

agg = AgglomerativeClustering(n_clusters=6, linkage='ward')
y_pred = agg.fit_predict(X)
```

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_8.png)

以下是绘制聚类结果的代码：

```python
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
ax.scatter(X[:, 0], X[:, 1], X[:, 2], c=y_pred, cmap='rainbow')
plt.show()
```

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_10.png)

执行代码后，我们发现聚类结果与K-Means类似，出现了颜色跨越瑞士卷不同“层”的情况。这表明，在标准的欧式距离下，分层聚类同样难以捕捉数据的流形结构。

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_12.png)

## 调整连接策略

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_14.png)

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_16.png)

我们尝试调整`linkage`参数，观察不同策略的效果。

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_18.png)

以下是几种常见的连接策略及其效果简述：
*   **`average`**：使用两个簇中所有点之间距离的平均值。执行后，聚类结果依然存在跨层现象。
*   **`complete`**：使用两个簇中距离最远的两个点之间的距离。结果同样不理想。
*   **`single`**：使用两个簇中距离最近的两个点之间的距离。执行后，几乎所有点都被归为同一类。

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_20.png)

经过测试，仅调整`linkage`参数无法解决跨层聚类的问题。

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_22.png)

## 引入连接性约束

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_24.png)

上面我们尝试了不同的连接策略，本节中我们来看看如何通过“连接性约束”来解决跨层问题。

连接性约束的作用是：在聚类过程中，只允许在空间上相邻的样本点被合并到同一个簇中。对于瑞士卷这种“非欧几里得几何”数据（即数据点分布在弯曲的流形上），如果没有此约束，算法会忽略数据本身的空间结构，仅依据欧式距离进行聚类，从而导致跨越流形不同“褶皱”（即层）的错误聚类。

我们通过`kneighbors_graph`函数来构建一个连接性约束矩阵。该函数会根据每个点的K个最近邻来定义“相邻”关系。

```python
from sklearn.neighbors import kneighbors_graph

# 创建连接性约束矩阵，指定每个点只与最近的10个邻居相连
connectivity = kneighbors_graph(X, n_neighbors=10)
```

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_26.png)

接下来，我们在声明分层聚类模型时，将构建好的连接性矩阵传递给`connectivity`参数。

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_28.png)

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_30.png)

```python
agg_constrained = AgglomerativeClustering(n_clusters=6,
                                          linkage='ward',
                                          connectivity=connectivity)
y_pred_constrained = agg_constrained.fit_predict(X)
```

再次绘制聚类结果，可以发现跨层现象基本消失。算法成功地将瑞士卷的每一层区分开来。

## 调整邻居数量

连接性约束的效果受`n_neighbors`参数影响。以下是调整该参数的效果：
*   **`n_neighbors=5`**：约束较强，聚类结果在层内区分更细致。
*   **`n_neighbors=30`**：约束过弱，每个点的“邻居”范围过大，可能再次包含其他层的点，导致重新出现跨层聚类。
*   **`n_neighbors=10`**：一个较为合适的值，能在保持层间分离的同时，得到合理的聚类效果。

通过调整这个参数，我们可以控制算法对数据局部结构的敏感程度，从而获得最佳的聚类效果。

![](img/8232ed27e2cd4fd3e99f1de3c55f04fc_32.png)

本节课中我们一起学习了如何应用分层聚类处理复杂空间数据。我们首先看到了标准分层聚类在瑞士卷数据上的局限性，然后引入了**连接性约束**这一关键工具。通过`kneighbors_graph`构建邻接关系，并传递给模型的`connectivity`参数，我们成功地引导算法关注数据的局部流形结构，从而避免了错误的跨层聚类，得到了更符合数据真实分布的聚类结果。这为解决具有复杂空间结构的数据聚类问题提供了有效思路。