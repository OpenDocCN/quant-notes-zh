# Python金融量化交易：10：指标相关情况分析 📊

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_1.png)

在本节课中，我们将学习如何分析两个金融指标之间的关系。我们将通过数据可视化和回归分析，直观地探索变量之间的相互影响，例如标普500指数（SPX）与波动率指数（VIX）之间的负相关性。

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_3.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_5.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_7.png)

## 概述

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_9.png)

回归分析的核心是探究变量之间的关系。我们将选取两个指标，通过绘制图表和计算，观察它们之间是否存在相互影响。

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_11.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_13.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_15.png)

## 数据准备

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_17.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_19.png)

首先，我们需要从数据集中选取两个感兴趣的指标进行分析。这里我们选择标普500指数（SPX）和波动率指数（VIX）。

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_21.png)

以下是数据准备的步骤：

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_23.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_25.png)

```python
# 从data2数据集中选取‘SPX’和‘VIX’两列数据
data = data2[['SPX', 'VIX']]
# 打印数据以确认
print(data.head())
```

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_27.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_29.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_31.png)

执行上述代码后，我们得到了只包含SPX和VIX两列的数据，索引正常，可以进行下一步分析。

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_33.png)

## 可视化分析：双轴折线图

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_35.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_37.png)

上一节我们准备好了数据，本节中我们来看看如何通过可视化初步观察两个指标的关系。最直接的方法是绘制图表。

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_39.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_41.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_43.png)

最简单的方法是直接在一个坐标系中绘制两条折线。然而，如果两个指标的量纲或数值范围差异很大，直接绘制可能难以观察趋势。因此，我们可以使用双Y轴的方法，将两个指标绘制在同一张图上，但使用不同的坐标轴刻度。

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_45.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_47.png)

以下是绘制双轴折线图的步骤：

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_49.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_51.png)

```python
import matplotlib.pyplot as plt

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_53.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_55.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_57.png)

# 创建图形和主坐标轴
fig, ax1 = plt.subplots(figsize=(10, 6))

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_59.png)

# 在主坐标轴（左侧Y轴）上绘制SPX数据
color = ‘tab:blue’
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘SPX’, color=color)
ax1.plot(data.index, data[‘SPX’], color=color)
ax1.tick_params(axis=‘y’, labelcolor=color)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_61.png)

# 创建副坐标轴（右侧Y轴）用于绘制VIX数据
ax2 = ax1.twinx()
color = ‘tab:green’
ax2.set_ylabel(‘VIX’, color=color)
ax2.plot(data.index, data[‘VIX’], color=color)
ax2.tick_params(axis=‘y’, labelcolor=color)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_63.png)

plt.title(‘SPX vs VIX Over Time’)
fig.tight_layout()
plt.show()
```

为了更清晰地观察短期内的关系，我们可以只选取一段时间的数据（例如2010年至2012年）进行绘图。通过观察图形，我们可以发现当SPX指数下降时，VIX指数往往上升，初步判断两者存在负相关关系。

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_65.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_67.png)

## 可视化分析：散点图与分布图

除了折线图，散点图矩阵是观察多变量关系及各自分布的强大工具。它能同时展示变量两两之间的散点关系以及对角线上的单变量分布。

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_69.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_71.png)

以下是使用Pandas绘制散点图矩阵的步骤：

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_73.png)

```python
from pandas.plotting import scatter_matrix

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_75.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_77.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_79.png)

# 计算指标的日收益率（对数差分），以观察变化率之间的关系
returns = np.log(data / data.shift(1)).dropna()

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_81.png)

# 绘制散点图矩阵
# diagonal=‘kde’ 表示在对角线绘制核密度估计图，更平滑
scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal=‘kde’, bins=50)
plt.suptitle(‘Scatter Matrix of SPX and VIX Returns’)
plt.show()
```

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_83.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_85.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_87.png)

在这个矩阵图中，对角线上的图展示了每个指标自身的分布情况（使用核密度估计）。非对角线上的散点图则清晰展示了两指标收益率之间的关系。从散点图中，我们可以更直观地看到数据点呈现从左上到右下的分布趋势，这进一步证实了SPX与VIX之间的负相关关系。

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_89.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_91.png)

## 总结

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_93.png)

![](img/4b4a8f628fc4c24f607bc6c9c4a121f4_95.png)

本节课中我们一起学习了如何分析两个金融指标之间的关系。我们首先通过**双轴折线图**在同一时间维度上对比了指标的走势，发现了此消彼长的现象。接着，我们利用**散点图矩阵**同时观察了指标自身的分布以及两两之间的相关性，通过散点图的趋势直观确认了负相关关系。这些可视化方法是金融数据分析中探索变量关系的有效工具。