# 量化交易实战：P10：指标相关性分析 📊

![](img/c50cadefefc78cbf207efebc21d70495_1.png)

在本节课中，我们将学习如何使用Python进行金融数据的回归分析，以探究不同指标（如标普500指数与VIX恐慌指数）之间的相互关系。我们将通过数据可视化（折线图、散点图）和统计方法（核密度估计、回归线）来直观地理解变量间的关联性。

---

![](img/c50cadefefc78cbf207efebc21d70495_3.png)

![](img/c50cadefefc78cbf207efebc21d70495_5.png)

## 第一步：数据准备与提取

![](img/c50cadefefc78cbf207efebc21d70495_7.png)

首先，我们需要从数据集中提取出要分析的两个指标。这里我们选择标普500指数（SPX）和VIX恐慌指数（VIX）。

![](img/c50cadefefc78cbf207efebc21d70495_9.png)

![](img/c50cadefefc78cbf207efebc21d70495_11.png)

```python
# 假设data2是包含所有数据的DataFrame
data = data2[['SPX', 'VIX']]
print(data.head())
```

![](img/c50cadefefc78cbf207efebc21d70495_13.png)

![](img/c50cadefefc78cbf207efebc21d70495_15.png)

执行以上代码后，我们将得到一个仅包含`SPX`和`VIX`两列数据的新DataFrame。

![](img/c50cadefefc78cbf207efebc21d70495_17.png)

---

![](img/c50cadefefc78cbf207efebc21d70495_19.png)

![](img/c50cadefefc78cbf207efebc21d70495_21.png)

## 第二步：初步可视化观察

为了直观感受两个指标随时间的变化趋势，我们首先尝试将它们绘制在同一张图中。但由于两个指标的数值尺度差异巨大，直接绘制会导致一个指标的变化几乎无法观察。

![](img/c50cadefefc78cbf207efebc21d70495_23.png)

![](img/c50cadefefc78cbf207efebc21d70495_25.png)

上一节我们提取了数据，本节中我们来看看如何通过可视化初步观察它们的关系。

![](img/c50cadefefc78cbf207efebc21d70495_27.png)

以下是绘制双Y轴折线图的步骤：

![](img/c50cadefefc78cbf207efebc21d70495_29.png)

![](img/c50cadefefc78cbf207efebc21d70495_31.png)

1.  使用`plot`方法并指定`secondary_y`参数，为第二个指标创建右侧Y轴。
2.  为了更清晰地观察短期关系，我们可以截取一段时间（例如2010年至2012年）的数据进行分析。
3.  通过调整图形尺寸（`figsize`）来获得更好的视觉效果。

![](img/c50cadefefc78cbf207efebc21d70495_33.png)

```python
# 选取2010年至2012年的数据
data_subset = data['2010-01-01':'2012-12-31']

![](img/c50cadefefc78cbf207efebc21d70495_35.png)

# 绘制双Y轴折线图
ax = data_subset['SPX'].plot(figsize=(12, 6), legend=True, color='blue', label='SPX')
ax2 = data_subset['VIX'].plot(secondary_y=True, legend=True, color='green', label='VIX', ax=ax)

![](img/c50cadefefc78cbf207efebc21d70495_37.png)

![](img/c50cadefefc78cbf207efebc21d70495_39.png)

# 添加图例
ax.legend(loc='upper left')
ax2.legend(loc='upper right')
plt.show()
```

![](img/c50cadefefc78cbf207efebc21d70495_41.png)

![](img/c50cadefefc78cbf207efebc21d70495_43.png)

通过观察图形，我们可以初步发现：当蓝色线（SPX）下降时，绿色线（VIX）往往上升，这表明两者可能存在负相关关系。可视化是一个迭代过程，通常从基础图形开始，根据观察结果逐步调整参数以获得更清晰的洞察。

![](img/c50cadefefc78cbf207efebc21d70495_45.png)

![](img/c50cadefefc78cbf207efebc21d70495_47.png)

---

## 第三步：散点图与分布分析

![](img/c50cadefefc78cbf207efebc21d70495_49.png)

虽然折线图能展示趋势，但散点图能更直接地揭示两个变量间的相关性。此外，我们还可以同时观察每个变量的自身分布。

![](img/c50cadefefc78cbf207efebc21d70495_51.png)

![](img/c50cadefefc78cbf207efebc21d70495_53.png)

上一节我们通过折线图观察了时序关系，本节中我们来看看如何利用散点图矩阵进行更全面的分析。

![](img/c50cadefefc78cbf207efebc21d70495_55.png)

![](img/c50cadefefc78cbf207efebc21d70495_57.png)

Pandas提供了`scatter_matrix`函数来快速绘制散点图矩阵。以下是关键参数说明：

![](img/c50cadefefc78cbf207efebc21d70495_59.png)

*   `alpha`: 设置点的透明度，用于处理数据点重叠的情况。
*   `diagonal`: 指定对角线上的图形类型，可选择直方图（`'hist'`）或核密度估计图（`'kde'`）。
*   `range_padding`: 调整图形之间的间距。
*   `figsize`: 设置整个图形的大小。

![](img/c50cadefefc78cbf207efebc21d70495_61.png)

```python
from pandas.plotting import scatter_matrix

![](img/c50cadefefc78cbf207efebc21d70495_63.png)

# 计算每日收益率（或变化率），以便分析变化量之间的关系
returns = np.log(data / data.shift(1))
returns = returns.dropna() # 删除第一行因shift产生的NaN值

# 绘制散点图矩阵，对角线显示核密度估计图
scatter_matrix(returns, alpha=0.2, figsize=(10, 10), diagonal='kde', range_padding=0.5)
plt.show()
```

生成的图形是一个2x2的矩阵：
*   **对角线**：显示每个变量（SPX收益率、VIX收益率）的核密度估计（KDE）图，它比直方图更平滑地展示了数据的分布形态。
*   **非对角线**：显示两个变量两两之间的散点图。从SPX与VIX的散点图中，点云呈现从左上到右下的分布，这直观地证实了它们之间的**负相关关系**。

![](img/c50cadefefc78cbf207efebc21d70495_65.png)

---

![](img/c50cadefefc78cbf207efebc21d70495_67.png)

## 第四步：添加回归线进行量化分析

散点图展示了关系模式，而回归线可以量化这种关系。我们可以在散点图上添加一条线性回归线，使得关系更加一目了然。

![](img/c50cadefefc78cbf207efebc21d70495_69.png)

上一节我们通过散点图矩阵观察了变量关系与分布，本节我们通过添加回归线来量化这种关系。

![](img/c50cadefefc78cbf207efebc21d70495_71.png)

以下是使用Seaborn库绘制带回归线的散点图步骤：

![](img/c50cadefefc78cbf207efebc21d70495_73.png)

1.  导入Seaborn库。
2.  使用`lmplot`或`regplot`函数，指定自变量（x）和因变量（y）。
3.  图形会自动拟合一条回归直线，并显示其置信区间。

![](img/c50cadefefc78cbf207efebc21d70495_75.png)

![](img/c50cadefefc78cbf207efebc21d70495_77.png)

```python
import seaborn as sns

![](img/c50cadefefc78cbf207efebc21d70495_79.png)

# 使用Seaborn绘制带回归线的散点图
sns.lmplot(x='SPX', y='VIX', data=returns, height=6, aspect=1.5)
plt.title('SPX vs VIX Returns with Regression Line')
plt.show()
```

![](img/c50cadefefc78cbf207efebc21d70495_81.png)

![](img/c50cadefefc78cbf207efebc21d70495_83.png)

这条回归线的斜率（系数）和截距就是线性回归方程 `y = ax + b` 的参数。斜率直接表明了关系的强度和方向（正或负）。通过观察这条线，我们可以快速、定量地理解一个指标变动时，另一个指标平均会如何变化。

![](img/c50cadefefc78cbf207efebc21d70495_85.png)

![](img/c50cadefefc78cbf207efebc21d70495_87.png)

---

![](img/c50cadefefc78cbf207efebc21d70495_89.png)

![](img/c50cadefefc78cbf207efebc21d70495_91.png)

## 总结

本节课中我们一起学习了金融指标相关性分析的完整流程：
1.  **数据准备**：从数据集中提取目标指标。
2.  **时序观察**：使用**双Y轴折线图**初步观察指标随时间变化的趋势关系。
3.  **关系与分布分析**：利用**散点图矩阵**同时观察变量间的散点关系以及各自的分布（通过直方图或**核密度估计图**）。
4.  **量化分析**：在散点图上添加**回归线**，直观地展示并量化变量间的线性关系。

![](img/c50cadefefc78cbf207efebc21d70495_93.png)

![](img/c50cadefefc78cbf207efebc21d70495_95.png)

通过这些步骤，我们可以从定性到定量，逐步深入地理解如SPX与VIX之间的负相关关系，这是量化交易中构建策略和风险控制的重要基础。