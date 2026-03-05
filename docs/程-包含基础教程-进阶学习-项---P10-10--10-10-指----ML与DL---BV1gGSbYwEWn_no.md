# 量化交易教程：P10：指标相关情况分析 📊

![](img/a9e38716aa2914fe2049303a52f4b274_1.png)

在本节课中，我们将学习如何使用Python进行金融数据的回归分析。我们将通过可视化图表和统计方法来探索两个金融指标（如标普500指数和VIX恐慌指数）之间的相互关系，特别是它们是否呈现负相关性。

![](img/a9e38716aa2914fe2049303a52f4b274_3.png)

---

![](img/a9e38716aa2914fe2049303a52f4b274_5.png)

![](img/a9e38716aa2914fe2049303a52f4b274_7.png)

## 数据准备与初步观察

![](img/a9e38716aa2914fe2049303a52f4b274_9.png)

![](img/a9e38716aa2914fe2049303a52f4b274_11.png)

首先，我们需要从数据集中提取出我们感兴趣的两个指标列。假设我们有一个名为`data2`的DataFrame，其中包含`SPX`（标普500指数）和`VIX`（恐慌指数）的数据。

![](img/a9e38716aa2914fe2049303a52f4b274_13.png)

![](img/a9e38716aa2914fe2049303a52f4b274_15.png)

![](img/a9e38716aa2914fe2049303a52f4b274_17.png)

```python
# 选择我们需要的两列数据
data = data2[['SPX', 'VIX']]
print(data.head())
```

![](img/a9e38716aa2914fe2049303a52f4b274_19.png)

执行上述代码后，我们将得到一个仅包含`SPX`和`VIX`两列的新DataFrame，并打印前几行以确认数据读取正确。

![](img/a9e38716aa2914fe2049303a52f4b274_21.png)

---

![](img/a9e38716aa2914fe2049303a52f4b274_23.png)

![](img/a9e38716aa2914fe2049303a52f4b274_25.png)

## 可视化分析：双轴折线图

![](img/a9e38716aa2914fe2049303a52f4b274_27.png)

上一节我们提取了数据，本节中我们来看看如何通过可视化来直观地观察两个指标之间的关系。最简单的方法是绘制折线图，但由于两个指标的数值范围可能差异很大，将它们画在同一坐标系下可能难以观察。

![](img/a9e38716aa2914fe2049303a52f4b274_29.png)

![](img/a9e38716aa2914fe2049303a52f4b274_31.png)

![](img/a9e38716aa2914fe2049303a52f4b274_33.png)

因此，我们采用双Y轴的方法，将两个序列绘制在同一张图上，但使用不同的坐标轴刻度。

![](img/a9e38716aa2914fe2049303a52f4b274_35.png)

```python
import matplotlib.pyplot as plt

![](img/a9e38716aa2914fe2049303a52f4b274_37.png)

fig, ax1 = plt.subplots(figsize=(12, 6))

![](img/a9e38716aa2914fe2049303a52f4b274_39.png)

![](img/a9e38716aa2914fe2049303a52f4b274_41.png)

![](img/a9e38716aa2914fe2049303a52f4b274_43.png)

# 绘制第一个指标（SPX）在左侧Y轴
color = ‘tab:blue’
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘SPX’, color=color)
ax1.plot(data.index, data[‘SPX’], color=color)
ax1.tick_params(axis=‘y’, labelcolor=color)

![](img/a9e38716aa2914fe2049303a52f4b274_45.png)

![](img/a9e38716aa2914fe2049303a52f4b274_47.png)

# 创建第二个Y轴，绘制第二个指标（VIX）
ax2 = ax1.twinx()
color = ‘tab:green’
ax2.set_ylabel(‘VIX’, color=color)
ax2.plot(data.index, data[‘VIX’], color=color)
ax2.tick_params(axis=‘y’, labelcolor=color)

fig.tight_layout()
plt.title(‘SPX vs VIX Over Time’)
plt.show()
```

![](img/a9e38716aa2914fe2049303a52f4b274_49.png)

![](img/a9e38716aa2914fe2049303a52f4b274_51.png)

![](img/a9e38716aa2914fe2049303a52f4b274_53.png)

为了更清晰地观察短期趋势，我们可以只选取特定时间段（例如2010年至2012年）的数据进行绘图。通过观察图形，我们可以初步判断：当SPX指数下降时，VIX指数往往上升，这暗示着一种负相关关系。

![](img/a9e38716aa2914fe2049303a52f4b274_55.png)

![](img/a9e38716aa2914fe2049303a52f4b274_57.png)

---

![](img/a9e38716aa2914fe2049303a52f4b274_59.png)

## 进阶可视化：散点图与分布图

![](img/a9e38716aa2914fe2049303a52f4b274_61.png)

除了折线图，散点图矩阵是观察多个变量间关系及各自分布的强大工具。它能同时展示变量两两之间的散点图和对角线上的分布图。

![](img/a9e38716aa2914fe2049303a52f4b274_63.png)

以下是使用Pandas绘制散点图矩阵的步骤：

1.  首先，我们计算指标的日收益率（或对数收益率），这能更好地反映变化关系。
2.  然后，使用`scatter_matrix`函数进行绘图。

![](img/a9e38716aa2914fe2049303a52f4b274_65.png)

![](img/a9e38716aa2914fe2049303a52f4b274_67.png)

```python
import pandas as pd
import numpy as np
from pandas.plotting import scatter_matrix

![](img/a9e38716aa2914fe2049303a52f4b274_69.png)

# 计算日对数收益率
returns = np.log(data / data.shift(1))
returns = returns.dropna() # 删除第一个NaN值

# 绘制散点图矩阵
scatter_matrix(returns,
               alpha=0.5, # 设置点的透明度，避免重叠
               figsize=(10, 10),
               diagonal=‘kde’, # 对角线绘制核密度估计图，也可选‘hist’直方图
               hist_kwds={‘bins’: 50}) # 如果选‘hist’，此参数设置分箱数

![](img/a9e38716aa2914fe2049303a52f4b274_71.png)

![](img/a9e38716aa2914fe2049303a52f4b274_73.png)

plt.suptitle(‘Scatter Matrix of Returns (SPX vs VIX)’)
plt.show()
```

![](img/a9e38716aa2914fe2049303a52f4b274_75.png)

**代码解释**：
*   `alpha=0.5`：设置散点的透明度，在数据点密集时非常有用。
*   `diagonal=‘kde’`：在对角线位置绘制**核密度估计图**，它比直方图更平滑，能更好地展示数据的分布形状。若改为`‘hist’`则绘制直方图。
*   `hist_kwds`：当`diagonal=‘hist’`时，用于向直方图传递参数，例如设置分箱数量。

![](img/a9e38716aa2914fe2049303a52f4b274_77.png)

![](img/a9e38716aa2914fe2049303a52f4b274_79.png)

通过生成的散点图矩阵，我们可以：
*   在对角线上看到每个指标收益率的分布情况。
*   在非对角线的散点图中，清晰地观察到`SPX`收益率与`VIX`收益率呈现的负相关趋势（点云沿左下-右上方向分布）。

![](img/a9e38716aa2914fe2049303a52f4b274_81.png)

---

![](img/a9e38716aa2914fe2049303a52f4b274_83.png)

![](img/a9e38716aa2914fe2049303a52f4b274_85.png)

![](img/a9e38716aa2914fe2049303a52f4b274_87.png)

## 核心概念总结

![](img/a9e38716aa2914fe2049303a52f4b274_89.png)

![](img/a9e38716aa2914fe2049303a52f4b274_91.png)

本节课中我们一起学习了如何对金融指标进行回归分析前的可视化探索：

![](img/a9e38716aa2914fe2049303a52f4b274_93.png)

1.  **数据选取**：从数据集中精确提取目标指标列。
2.  **关系初探**：使用**双Y轴折线图**，将不同量级的指标绘制于同一图中，直观观察其走势的相反关系（负相关）。
3.  **深入分析**：通过**散点图矩阵**，同时考察指标的分布（对角线）和两两关系（散点图）。其中，核密度估计（**KDE**）是对直方图的平滑处理，能更优雅地展示数据分布。

![](img/a9e38716aa2914fe2049303a52f4b274_95.png)

![](img/a9e38716aa2914fe2049303a52f4b274_97.png)

这些可视化方法是量化分析中探索数据关系的基础，能帮助我们在进行复杂的统计建模（如线性回归）之前，获得对数据的直观理解。