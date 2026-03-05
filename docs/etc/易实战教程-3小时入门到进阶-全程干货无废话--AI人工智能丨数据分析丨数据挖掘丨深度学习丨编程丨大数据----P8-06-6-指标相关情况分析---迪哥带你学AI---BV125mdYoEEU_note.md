# Python金融分析与量化交易实战：P8：06-6-指标相关情况分析 📈

![](img/f1af86a76d56bb3a1bb390a08340480d_1.png)

在本节课中，我们将学习如何分析两个金融指标之间的关系，特别是通过可视化和回归分析来探索标普500指数（S&P 500）与VIX恐慌指数之间的关联。

![](img/f1af86a76d56bb3a1bb390a08340480d_3.png)

## 概述

![](img/f1af86a76d56bb3a1bb390a08340480d_5.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_7.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_9.png)

上一节我们介绍了数据的基本处理。本节中，我们来看看如何分析两个变量之间的相关性。我们将通过画图直观展示关系，并最终进行回归分析，量化这种关系。

![](img/f1af86a76d56bb3a1bb390a08340480d_11.png)

## 第一步：选择并提取数据

![](img/f1af86a76d56bb3a1bb390a08340480d_13.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_15.png)

首先，我们需要从数据集中选择两个感兴趣的指标进行分析。这里我们选择“SPX”（标普500指数）和“VIX”（恐慌指数）。

![](img/f1af86a76d56bb3a1bb390a08340480d_17.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_19.png)

以下是提取这两列数据的代码：

```python
data2 = data2[['SPX', 'VIX']]
print(data2.head())
```

![](img/f1af86a76d56bb3a1bb390a08340480d_21.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_23.png)

执行这段代码后，我们便得到了一个只包含`SPX`和`VIX`两列数据的DataFrame，便于后续分析。

![](img/f1af86a76d56bb3a1bb390a08340480d_25.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_27.png)

## 第二步：初步可视化观察关系

![](img/f1af86a76d56bb3a1bb390a08340480d_29.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_31.png)

得到数据后，最直接的方法是将其可视化。我们可以尝试将两个指标的走势画在同一张图上观察。

![](img/f1af86a76d56bb3a1bb390a08340480d_33.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_35.png)

最初，我们可能尝试简单的绘图：

![](img/f1af86a76d56bb3a1bb390a08340480d_37.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_39.png)

```python
data2.plot()
```

![](img/f1af86a76d56bb3a1bb390a08340480d_41.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_43.png)

但这样画出的折线图可能因为两个指标的量纲和数值范围不同，导致趋势难以观察。

![](img/f1af86a76d56bb3a1bb390a08340480d_45.png)

## 第三步：优化可视化方法

![](img/f1af86a76d56bb3a1bb390a08340480d_47.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_49.png)

为了让对比更清晰，我们可以使用双Y轴的方法，将两个序列绘制在同一张图的不同坐标轴上。

![](img/f1af86a76d56bb3a1bb390a08340480d_51.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_53.png)

```python
ax = data2['SPX'].plot(legend=True, figsize=(10,6))
data2['VIX'].plot(ax=ax, legend=True, secondary_y=True)
```

![](img/f1af86a76d56bb3a1bb390a08340480d_55.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_57.png)

为了更清晰地观察短期关系，我们还可以只选取特定时间段的数据（例如2010年至2012年）进行绘图：

![](img/f1af86a76d56bb3a1bb390a08340480d_59.png)

```python
data_subset = data2['2010-01-01':'2012-12-31']
ax = data_subset['SPX'].plot(legend=True, figsize=(10,6))
data_subset['VIX'].plot(ax=ax, legend=True, secondary_y=True)
```

![](img/f1af86a76d56bb3a1bb390a08340480d_61.png)

通过观察优化后的图表，我们可以初步发现：当标普500指数（SPX）下降时，恐慌指数（VIX）往往上升，呈现出一种负相关的趋势。

## 第四步：使用散点图矩阵深入分析

![](img/f1af86a76d56bb3a1bb390a08340480d_63.png)

除了折线图，散点图矩阵是观察多个变量间两两关系的强大工具。它可以同时展示单个变量的分布以及变量之间的散点关系。

![](img/f1af86a76d56bb3a1bb390a08340480d_65.png)

以下是绘制散点图矩阵的代码：

```python
from pandas.plotting import scatter_matrix
scatter_matrix(data2, alpha=0.2, figsize=(10, 10), diagonal='kde')
```

![](img/f1af86a76d56bb3a1bb390a08340480d_67.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_69.png)

在这张图中：
*   **对角线**：展示了每个变量自身的分布情况，这里我们使用核密度估计图，它比直方图更平滑。
*   **非对角线**：展示了两个变量之间的散点图。从`SPX`和`VIX`的散点图中，可以更直观地看到它们呈负相关关系，点云呈现从左上到右下的分布趋势。

## 第五步：进行回归分析

![](img/f1af86a76d56bb3a1bb390a08340480d_71.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_73.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_75.png)

散点图展示了关系，而回归分析可以量化这种关系。虽然本节没有完全展开代码，但其核心思想是建立一个数学模型（例如线性回归模型）来描述`VIX`如何随`SPX`变化。

![](img/f1af86a76d56bb3a1bb390a08340480d_77.png)

一个简单的线性回归模型公式如下：
**VIX ≈ β₀ + β₁ * SPX + ε**

![](img/f1af86a76d56bb3a1bb390a08340480d_79.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_81.png)

其中，`β₁`就是回归系数，如果为负值，则证实了我们观察到的负相关关系。通过计算相关系数或拟合回归线，我们可以得到具体的量化指标。

![](img/f1af86a76d56bb3a1bb390a08340480d_83.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_85.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_87.png)

## 总结

![](img/f1af86a76d56bb3a1bb390a08340480d_89.png)

本节课中我们一起学习了分析指标间相关性的完整流程：
1.  **数据准备**：从数据集中提取目标指标列。
2.  **可视化探索**：通过绘制双Y轴折线图初步观察关系。
3.  **深入分析**：利用散点图矩阵同时观察单变量分布和双变量关系。
4.  **量化关系**（引子）：引入回归分析的概念，为量化变量间的相关性打下基础。

![](img/f1af86a76d56bb3a1bb390a08340480d_91.png)

![](img/f1af86a76d56bb3a1bb390a08340480d_93.png)

整个过程体现了数据分析中“从直观到量化”的思路：先通过图表形成直观认识，再通过统计方法进行精确验证。