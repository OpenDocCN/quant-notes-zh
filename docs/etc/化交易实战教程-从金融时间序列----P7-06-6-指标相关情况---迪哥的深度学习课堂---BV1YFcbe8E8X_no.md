# Python金融分析与量化交易实战：P7：06-6-指标相关情况分析 📈

在本节课中，我们将要学习如何分析两个金融指标之间的相关性。我们将通过数据可视化（如折线图和散点图矩阵）来直观地观察变量间的关系，并最终通过回归分析来量化这种关系。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_1.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_3.png)

## 第一步：数据准备与初步观察

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_5.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_7.png)

上一节我们介绍了数据处理的基础，本节中我们来看看如何选取并观察两个特定指标。我们选择标普500指数（SPX）和恐慌指数（VIX）作为分析对象。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_9.png)

首先，我们需要从数据集中提取这两列数据。假设我们有一个名为 `data2` 的DataFrame，其中包含了我们所需的数据。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_11.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_13.png)

```python
# 从data2中选取SPX和VIX两列数据
data = data2[['SPX', 'VIX']]
print(data.head())
```

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_15.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_17.png)

执行上述代码后，我们便得到了只包含SPX和VIX两列的新DataFrame `data`。为了专注于分析这两个指标的关系，我们暂时忽略其他数据。

## 第二步：通过折线图观察关系

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_19.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_21.png)

获取数据后，最直观的方法是绘制折线图来观察它们的走势。以下是绘制折线图的步骤。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_23.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_25.png)

首先，我们尝试将两个指标绘制在同一张图中。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_27.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_29.png)

```python
data.plot()
```

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_31.png)

然而，由于两个指标的数值范围差异较大，直接绘制在一张图上难以观察趋势。因此，我们需要改进绘图方法。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_33.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_35.png)

一种有效的方法是使用双Y轴，将两个指标绘制在同一张图的不同坐标轴上，以便更清晰地观察其联动关系。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_37.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_39.png)

```python
# 使用双Y轴绘图
ax = data['SPX'].plot(legend=True, figsize=(10,6))
data['VIX'].plot(ax=ax, secondary_y=True, legend=True)
```

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_41.png)

为了更清晰地展示短期内的关系，我们可以只选取特定时间段的数据进行分析，例如2010年至2012年。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_43.png)

```python
# 选取2010年至2012年的数据
data_subset = data['2010-01-01':'2012-12-31']
ax = data_subset['SPX'].plot(legend=True, figsize=(10,6))
data_subset['VIX'].plot(ax=ax, secondary_y=True, legend=True)
```

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_45.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_47.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_49.png)

通过观察图形，我们可以初步发现：当SPX指数下降时，VIX指数往往上升，这表明两者可能存在负相关关系。绘图过程通常是迭代的，从基本图形开始，逐步调整参数以获得更清晰的可视化效果。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_51.png)

## 第三步：通过散点图矩阵深入分析

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_53.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_55.png)

除了折线图，散点图矩阵是观察多变量关系及其各自分布的强大工具。在进行分析之前，我们通常需要计算指标的日收益率。

以下是计算日收益率并绘制散点图矩阵的步骤。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_57.png)

首先，我们计算SPX和VIX的日收益率。

```python
import numpy as np
# 计算日收益率
returns = np.log(data / data.shift(1))
print(returns.head())
```

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_59.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_61.png)

接下来，我们使用Pandas的 `scatter_matrix` 函数来绘制散点图矩阵。

以下是绘制散点图矩阵的代码示例，其中对角线显示直方图。

```python
from pandas.plotting import scatter_matrix
scatter_matrix(returns,
               alpha=0.5,        # 设置点的透明度
               figsize=(10, 10),
               diagonal='hist',  # 对角线显示直方图
               hist_kwds={'bins': 50}) # 直方图参数
```

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_63.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_65.png)

我们也可以将对角线的图形改为核密度估计图，它能提供更平滑的分布曲线。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_67.png)

```python
scatter_matrix(returns,
               alpha=0.5,
               figsize=(10, 10),
               diagonal='kde')   # 对角线显示核密度估计图
```

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_69.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_71.png)

核密度估计图与直方图反映的趋势是类似的，但它通过平滑处理提供了更连续的分布视图。通过散点图矩阵，我们可以更清晰地确认SPX收益率与VIX收益率之间存在负相关关系。选择合适的图形有助于更有效地传达分析结果。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_73.png)

## 第四步：进行回归分析

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_75.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_77.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_79.png)

散点图展示了关系，但要量化这种关系，我们需要进行回归分析。回归分析可以帮助我们拟合出一条最能代表数据点趋势的直线，并得到回归方程和相关系数。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_81.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_83.png)

虽然本教程未展示完整的回归代码，但其核心思想是：以SPX收益率为自变量（X），VIX收益率为因变量（Y），使用线性回归模型（如 `statsmodels` 或 `scikit-learn` 库）进行拟合。拟合出的直线方程和R平方值能够量化两者的负相关程度，为量化交易策略提供依据。

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_85.png)

---

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_87.png)

![](img/77fdca7b2fcd7be747d10a5c61b89ecd_89.png)

本节课中我们一起学习了如何分析金融指标间的相关性。我们从数据选取开始，通过绘制**双Y轴折线图**观察指标走势，利用**散点图矩阵**深入分析分布与两两关系，并引出了**回归分析**的概念以量化相关性。这些方法是金融数据分析和量化策略开发的基础。