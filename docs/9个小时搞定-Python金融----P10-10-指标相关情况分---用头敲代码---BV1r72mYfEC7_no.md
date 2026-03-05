# Python金融量化分析：P10：指标相关情况分析 📊

![](img/d3d235a1a75bfa178363fffefeea287d_1.png)

在本节课中，我们将学习如何分析两个金融指标之间的关系，特别是通过可视化和回归分析来探究标普500指数（SPX）与恐慌指数（VIX）之间的相关性。

![](img/d3d235a1a75bfa178363fffefeea287d_3.png)

![](img/d3d235a1a75bfa178363fffefeea287d_5.png)

![](img/d3d235a1a75bfa178363fffefeea287d_7.png)

## 概述

![](img/d3d235a1a75bfa178363fffefeea287d_9.png)

上一节我们介绍了数据处理与增长率计算。本节中，我们来看看如何分析两个变量之间的相关关系。我们将通过画图直观观察，并使用散点图矩阵和回归分析来量化这种关系。

![](img/d3d235a1a75bfa178363fffefeea287d_11.png)

![](img/d3d235a1a75bfa178363fffefeea287d_13.png)

## 第一步：选择并提取数据

![](img/d3d235a1a75bfa178363fffefeea287d_15.png)

![](img/d3d235a1a75bfa178363fffefeea287d_17.png)

![](img/d3d235a1a75bfa178363fffefeea287d_19.png)

首先，我们需要从数据集中选择两个感兴趣的指标进行分析。以下是具体步骤：

1.  从名为 `data2` 的数据集中，提取 `SPX` 和 `VIX` 这两列数据。
2.  确保列名与数据集中的原始名称（大写）一致。

![](img/d3d235a1a75bfa178363fffefeea287d_21.png)

![](img/d3d235a1a75bfa178363fffefeea287d_23.png)

![](img/d3d235a1a75bfa178363fffefeea287d_25.png)

```python
# 选择 SPX 和 VIX 两列数据
data = data2[['SPX', 'VIX']]
```

![](img/d3d235a1a75bfa178363fffefeea287d_27.png)

执行以上代码后，我们便得到了一个只包含 `SPX` 和 `VIX` 两列的新DataFrame，用于后续分析。

![](img/d3d235a1a75bfa178363fffefeea287d_29.png)

![](img/d3d235a1a75bfa178363fffefeea287d_31.png)

![](img/d3d235a1a75bfa178363fffefeea287d_33.png)

## 第二步：绘制双轴折线图进行初步观察

![](img/d3d235a1a75bfa178363fffefeea287d_35.png)

为了直观地观察两个指标随时间的变化趋势及其相互关系，最直接的方法是绘制折线图。但由于两个指标的数值范围可能差异很大，将它们画在同一坐标系下会难以观察。因此，我们采用双Y轴的方法，将两个序列绘制在同一张图上。

![](img/d3d235a1a75bfa178363fffefeea287d_37.png)

![](img/d3d235a1a75bfa178363fffefeea287d_39.png)

以下是绘制双轴折线图的步骤：

![](img/d3d235a1a75bfa178363fffefeea287d_41.png)

![](img/d3d235a1a75bfa178363fffefeea287d_43.png)

![](img/d3d235a1a75bfa178363fffefeea287d_45.png)

1.  使用 `plot` 方法，并指定 `secondary_y` 参数为第二个序列的列名，以创建第二个Y轴。
2.  为了更清晰地观察短期内的关系，我们可以通过切片操作只选取特定时间段（例如2010年至2012年）的数据进行绘图。
3.  通过 `figsize` 参数调整图表大小，以获得更好的视觉效果。

![](img/d3d235a1a75bfa178363fffefeea287d_47.png)

```python
# 选取2010年至2012年的数据
data_subset = data['2010-01-01':'2012-12-31']

![](img/d3d235a1a75bfa178363fffefeea287d_49.png)

![](img/d3d235a1a75bfa178363fffefeea287d_51.png)

# 绘制双Y轴折线图
ax = data_subset.plot(secondary_y=['VIX'], figsize=(12, 6))
```

![](img/d3d235a1a75bfa178363fffefeea287d_53.png)

![](img/d3d235a1a75bfa178363fffefeea287d_55.png)

![](img/d3d235a1a75bfa178363fffefeea287d_57.png)

通过观察生成的图表，我们可以初步发现：当 `SPX`（蓝色线）下降时，`VIX`（绿色线）往往上升，反之亦然，这暗示了二者之间存在负相关关系。

![](img/d3d235a1a75bfa178363fffefeea287d_59.png)

## 第三步：绘制散点图矩阵进行深入分析

![](img/d3d235a1a75bfa178363fffefeea287d_61.png)

折线图展示了时间序列上的趋势，而散点图矩阵能更好地展示变量两两之间的分布与相关性。我们将使用增长率数据来绘制散点图矩阵。

![](img/d3d235a1a75bfa178363fffefeea287d_63.png)

首先，计算 `SPX` 和 `VIX` 的日对数收益率：

```python
# 计算日对数收益率
returns = np.log(data / data.shift(1))
returns = returns.dropna() # 删除第一个NaN值
```

![](img/d3d235a1a75bfa178363fffefeea287d_65.png)

![](img/d3d235a1a75bfa178363fffefeea287d_67.png)

接下来，使用Pandas的 `scatter_matrix` 函数绘制散点图矩阵：

```python
from pandas.plotting import scatter_matrix

![](img/d3d235a1a75bfa178363fffefeea287d_69.png)

# 绘制散点图矩阵
# diagonal='hist' 表示对角线绘制直方图
# diagonal='kde' 表示对角线绘制核密度估计图
scatter_matrix(returns,
               alpha=0.5,        # 设置点的透明度
               diagonal='kde',   # 对角线图形类型：'kde' 或 'hist'
               figsize=(10, 10))
```

![](img/d3d235a1a75bfa178363fffefeea287d_71.png)

在这个矩阵图中：
*   **对角线** 上的图是每个变量自身的分布图（核密度估计图），它比直方图更平滑，能更好地展示数据的分布形态。
*   **非对角线** 上的图是两个变量之间的散点图。从 `SPX` 和 `VIX` 的散点图中，可以清晰地看到数据点沿一条向下的斜线分布，这再次印证了它们之间的负相关关系。

![](img/d3d235a1a75bfa178363fffefeea287d_73.png)

![](img/d3d235a1a75bfa178363fffefeea287d_75.png)

## 第四步：进行回归分析

![](img/d3d235a1a75bfa178363fffefeea287d_77.png)

![](img/d3d235a1a75bfa178363fffefeea287d_79.png)

散点图展示了关系，而回归分析可以量化这种关系。我们可以在散点图上添加一条回归线，使得关系更加一目了然。这通常需要借助 `seaborn` 等更高级的绘图库来实现。

![](img/d3d235a1a75bfa178363fffefeea287d_81.png)

![](img/d3d235a1a75bfa178363fffefeea287d_83.png)

核心的回归方程可以表示为：
**Y = β₀ + β₁X + ε**
其中，`Y` 是因变量，`X` 是自变量，`β₀` 是截距，`β₁` 是斜率（即回归系数），`ε` 是误差项。在本例中，我们可以研究 `VIX` 的收益率对 `SPX` 收益率的回归。

![](img/d3d235a1a75bfa178363fffefeea287d_85.png)

![](img/d3d235a1a75bfa178363fffefeea287d_87.png)

## 总结

![](img/d3d235a1a75bfa178363fffefeea287d_89.png)

![](img/d3d235a1a75bfa178363fffefeea287d_91.png)

本节课中我们一起学习了金融指标相关性分析的完整流程：
1.  **数据准备**：从数据集中提取目标指标列。
2.  **可视化观察**：通过**双轴折线图**初步观察指标在时间序列上的互动趋势。
3.  **深入分析**：通过计算收益率并绘制**散点图矩阵**，直观展示变量间的分布与相关关系。对角线可用**直方图**或**核密度估计图**展示自身分布。
4.  **量化关系**：引入**回归分析**的概念，为下一步量化两个变量之间的具体关系做好准备。

![](img/d3d235a1a75bfa178363fffefeea287d_93.png)

![](img/d3d235a1a75bfa178363fffefeea287d_95.png)

通过本节的学习，你掌握了使用可视化工具快速洞察金融数据间相关性的基本方法，这是进行更复杂量化建模的重要基础。