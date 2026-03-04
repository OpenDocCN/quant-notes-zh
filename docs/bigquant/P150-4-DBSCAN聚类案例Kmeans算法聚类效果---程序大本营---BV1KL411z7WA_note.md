# 机器学习聚类算法：P150：4-DBSCAN聚类案例与Kmeans算法效果对比 🧮

![](img/cf66db87641b5ec31425a88c6ae7665d_1.png)

![](img/cf66db87641b5ec31425a88c6ae7665d_3.png)

在本节课中，我们将学习如何使用Kmeans算法对一个特定的数据集进行聚类，并可视化其分类效果。我们将通过代码实践，观察Kmeans算法在处理非球形分布数据时的局限性，并学习如何自定义散点图的颜色映射。

![](img/cf66db87641b5ec31425a88c6ae7665d_5.png)

## 概述

我们将使用Python的`sklearn`库生成一个模拟数据集，并应用Kmeans算法进行聚类。通过`matplotlib`库，我们将结果可视化，并探讨如何通过`cmap`参数自定义数据点的颜色，以清晰地区分不同的类别。

## 数据准备与算法应用

![](img/cf66db87641b5ec31425a88c6ae7665d_7.png)

首先，我们创建了用于聚类的模拟数据。数据已经准备就绪。

接下来，我们使用Kmeans算法对数据进行聚类划分。我们之前学习了Kmeans算法，现在先使用它来演示。

以下是使用Kmeans进行聚类的代码步骤：

![](img/cf66db87641b5ec31425a88c6ae7665d_9.png)

```python
# 初始化Kmeans模型，设定聚类数量为3
kmeans = KMeans(n_clusters=3)

# 使用数据X训练模型
kmeans.fit(X)

![](img/cf66db87641b5ec31425a88c6ae7665d_11.png)

# 对数据进行预测，得到每个样本的类别标签
y_pred = kmeans.predict(X)
```

![](img/cf66db87641b5ec31425a88c6ae7665d_13.png)

## 结果可视化与颜色映射

![](img/cf66db87641b5ec31425a88c6ae7665d_15.png)

![](img/cf66db87641b5ec31425a88c6ae7665d_17.png)

训练完成后，我们使用`matplotlib`将聚类结果绘制成散点图。我们将数据点的横纵坐标传入，并通过参数`c=y_pred`来根据预测的类别为点着色。

![](img/cf66db87641b5ec31425a88c6ae7665d_19.png)

参数`c`代表颜色（color）。我们计算出的`y_pred`表示类别标签（例如0, 1, 2）。当我们在绘图时设置`c=y_pred`，意味着系统会根据每个数据点的类别自动分配颜色进行绘制。默认的颜色映射（colormap）是预定义的。

![](img/cf66db87641b5ec31425a88c6ae7665d_21.png)

如果我们想将所有点设置为单一颜色，例如黄色，可以传递字符串`‘y’`（代表yellow）。代码如下：

```python
plt.scatter(X[:, 0], X[:, 1], c=‘y’)
```

![](img/cf66db87641b5ec31425a88c6ae7665d_23.png)

执行后，所有数据点将变为黄色。

![](img/cf66db87641b5ec31425a88c6ae7665d_25.png)

然而，我们的目标是按类别区分颜色。传递`c=y_pred`（一个数值数组）后，绘图函数会自动为不同的类别值分配不同的颜色。我们可以通过`cmap`参数来指定使用哪种颜色映射。

![](img/cf66db87641b5ec31425a88c6ae7665d_27.png)

例如，设置`cmap=‘ocean’`会使用海洋色调的颜色映射。我们还可以调整图形尺寸以获得更好的视图：

![](img/cf66db87641b5ec31425a88c6ae7665d_29.png)

```python
plt.figure(figsize=(6, 6))
plt.scatter(X[:, 0], X[:, 1], c=y_pred, cmap=‘ocean’)
```

![](img/cf66db87641b5ec31425a88c6ae7665d_31.png)

![](img/cf66db87641b5ec31425a88c6ae7665d_33.png)

`matplotlib`提供了丰富的内置颜色映射。我们可以通过以下代码查看所有可用的选项：

![](img/cf66db87641b5ec31425a88c6ae7665d_35.png)

```python
print(plt.colormaps())
```

![](img/cf66db87641b5ec31425a88c6ae7665d_37.png)

从列表中选择一个，例如`‘autumn’`或`‘coolwarm’`，将其赋值给`cmap`参数即可应用。

![](img/cf66db87641b5ec31425a88c6ae7665d_39.png)

## 自定义颜色映射

![](img/cf66db87641b5ec31425a88c6ae7665d_41.png)

除了使用内置颜色映射，我们还可以完全自定义颜色。这需要从`matplotlib.colors`模块导入`ListedColormap`。

![](img/cf66db87641b5ec31425a88c6ae7665d_43.png)

以下是创建和使用自定义颜色映射的步骤：

1.  导入必要的模块。
2.  创建一个颜色列表，例如`[‘red‘, ‘green‘, ‘blue‘]`。
3.  使用这个列表实例化一个`ListedColormap`对象。
4.  在绘图时，将这个对象传递给`cmap`参数。

![](img/cf66db87641b5ec31425a88c6ae7665d_45.png)

![](img/cf66db87641b5ec31425a88c6ae7665d_47.png)

代码如下：

```python
from matplotlib.colors import ListedColormap

![](img/cf66db87641b5ec31425a88c6ae7665d_49.png)

# 创建自定义颜色列表
custom_cmap = ListedColormap([‘red‘, ‘green‘, ‘blue‘])

![](img/cf66db87641b5ec31425a88c6ae7665d_51.png)

# 使用自定义颜色映射绘图
plt.figure(figsize=(6, 6))
plt.scatter(X[:, 0], X[:, 1], c=y_pred, cmap=custom_cmap)
plt.show()
```

![](img/cf66db87641b5ec31425a88c6ae7665d_53.png)

执行后，三个类别将分别被显示为红色、绿色和蓝色。

![](img/cf66db87641b5ec31425a88c6ae7665d_55.png)

![](img/cf66db87641b5ec31425a88c6ae7665d_57.png)

## Kmeans算法效果分析

![](img/cf66db87641b5ec31425a88c6ae7665d_59.png)

现在，观察Kmeans算法在我们创建的数据集上的聚类效果。从可视化结果可以清晰地看出，Kmeans将数据划分成了三个部分。

![](img/cf66db87641b5ec31425a88c6ae7665d_61.png)

但是，这种划分结果理想吗？Kmeans算法倾向于将数据划分为球形或凸形的簇。对于我们所创建的、具有复杂形状（如环形或月牙形）分布的数据，Kmeans的划分效果并不好。它无法有效识别非球形的簇结构，这是该算法的一个局限性。

![](img/cf66db87641b5ec31425a88c6ae7665d_62.png)

![](img/cf66db87641b5ec31425a88c6ae7665d_64.png)

## 总结

![](img/cf66db87641b5ec31425a88c6ae7665d_66.png)

本节课中，我们一起学习了如何应用Kmeans算法进行聚类并可视化结果。我们详细介绍了如何通过`c`和`cmap`参数来控制散点图的颜色，包括使用内置颜色映射和创建自定义颜色映射。最后，我们通过实例观察到Kmeans算法在处理非球形分布数据时效果不佳，这引出了对更强大聚类算法（如下一节将介绍的DBSCAN）的需求。