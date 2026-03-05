# 金融量化分析：P10：4-指标相关情况分析 📊

![](img/2eb619e741e38e508b6490c17890f4bb_1.png)

在本节课中，我们将学习如何分析两个金融指标（如标普500指数和VIX恐慌指数）之间的相关性。我们将通过数据可视化（折线图、散点图）和回归分析来直观地探索它们之间的关系。

## 概述

![](img/2eb619e741e38e508b6490c17890f4bb_3.png)

![](img/2eb619e741e38e508b6490c17890f4bb_5.png)

![](img/2eb619e741e38e508b6490c17890f4bb_7.png)

上一节我们介绍了数据预处理和增长率计算。本节中，我们来看看如何分析两个变量之间的相关性。我们将通过画图来直观展示关系，并学习如何在同一张图中对比两个不同量纲的指标。

![](img/2eb619e741e38e508b6490c17890f4bb_9.png)

## 第一步：选择并提取数据

![](img/2eb619e741e38e508b6490c17890f4bb_11.png)

![](img/2eb619e741e38e508b6490c17890f4bb_13.png)

首先，我们需要从数据集中选择两个感兴趣的指标进行分析。以下是提取数据的步骤：

![](img/2eb619e741e38e508b6490c17890f4bb_15.png)

1.  从名为 `data2` 的数据集中，选择 `SPX`（标普500指数）和 `VIX`（恐慌指数）这两列数据。
2.  注意列名在原始数据中是大写的，需要保持一致。

![](img/2eb619e741e38e508b6490c17890f4bb_17.png)

![](img/2eb619e741e38e508b6490c17890f4bb_19.png)

以下是提取数据的代码：

```python
# 选择 SPX 和 VIX 两列数据
data = data2[['SPX', 'VIX']]
# 打印数据，查看前几行
print(data.head())
```

![](img/2eb619e741e38e508b6490c17890f4bb_21.png)

![](img/2eb619e741e38e508b6490c17890f4bb_23.png)

## 第二步：初步可视化分析

![](img/2eb619e741e38e508b6490c17890f4bb_25.png)

提取数据后，最直接的方法是将其可视化。以下是绘制两个指标各自走势图的步骤：

![](img/2eb619e741e38e508b6490c17890f4bb_27.png)

![](img/2eb619e741e38e508b6490c17890f4bb_29.png)

1.  使用 `plot` 方法可以快速绘制折线图。
2.  为了更清晰地对比，我们使用 `subplots` 将两个图并排显示。

![](img/2eb619e741e38e508b6490c17890f4bb_31.png)

![](img/2eb619e741e38e508b6490c17890f4bb_33.png)

以下是绘制子图的代码：

![](img/2eb619e741e38e508b6490c17890f4bb_35.png)

```python
import matplotlib.pyplot as plt

![](img/2eb619e741e38e508b6490c17890f4bb_37.png)

![](img/2eb619e741e38e508b6490c17890f4bb_39.png)

# 创建包含两个子图的画布
fig, axes = plt.subplots(1, 2, figsize=(12, 4))
# 分别绘制 SPX 和 VIX 的走势图
data['SPX'].plot(ax=axes[0], title='SPX')
data['VIX'].plot(ax=axes[1], title='VIX', color='green')
plt.tight_layout()
plt.show()
```

![](img/2eb619e741e38e508b6490c17890f4bb_41.png)

![](img/2eb619e741e38e508b6490c17890f4bb_43.png)

通过并排的折线图，我们可以分别观察每个指标随时间的变化，但难以直接看出它们之间的互动关系。

![](img/2eb619e741e38e508b6490c17890f4bb_45.png)

![](img/2eb619e741e38e508b6490c17890f4bb_47.png)

## 第三步：使用双Y轴进行对比

为了在同一张图中对比两个指标，我们可以使用双Y轴。这样，两个序列可以共享同一个X轴（时间），但拥有各自的Y轴刻度。

![](img/2eb619e741e38e508b6490c17890f4bb_49.png)

![](img/2eb619e741e38e508b6490c17890f4bb_51.png)

以下是使用双Y轴绘图的代码：

![](img/2eb619e741e38e508b6490c17890f4bb_53.png)

![](img/2eb619e741e38e508b6490c17890f4bb_55.png)

```python
# 创建图形和第一个Y轴
fig, ax1 = plt.subplots(figsize=(10, 6))
# 在第一个Y轴上绘制SPX（蓝色）
color = 'tab:blue'
ax1.set_xlabel('Date')
ax1.set_ylabel('SPX', color=color)
ax1.plot(data.index, data['SPX'], color=color)
ax1.tick_params(axis='y', labelcolor=color)

![](img/2eb619e741e38e508b6490c17890f4bb_57.png)

# 创建第二个Y轴，共享同一个X轴
ax2 = ax1.twinx()
# 在第二个Y轴上绘制VIX（绿色）
color = 'tab:green'
ax2.set_ylabel('VIX', color=color)
ax2.plot(data.index, data['VIX'], color=color)
ax2.tick_params(axis='y', labelcolor=color)

![](img/2eb619e741e38e508b6490c17890f4bb_59.png)

plt.title('SPX vs VIX (Dual Y-Axis)')
plt.show()
```

![](img/2eb619e741e38e508b6490c17890f4bb_61.png)

为了更清晰地观察短期关系，我们可以只选取一段时间的数据（例如2010年至2012年）进行绘图。

![](img/2eb619e741e38e508b6490c17890f4bb_63.png)

```python
# 选取2010-01-01到2012-12-31期间的数据
data_subset = data['2010-01-01':'2012-12-31']

# 使用同样的双Y轴方法绘制子集数据
fig, ax1 = plt.subplots(figsize=(12, 6))
# ... (绘制代码与上方相同，将`data`替换为`data_subset`)
plt.show()
```

观察图形可以发现，当SPX指数下降时（蓝色线），VIX指数往往上升（绿色线），显示出一种负相关的趋势。

![](img/2eb619e741e38e508b6490c17890f4bb_65.png)

![](img/2eb619e741e38e508b6490c17890f4bb_67.png)

## 第四步：绘制散点图与分布图

除了时间序列图，散点图矩阵是观察变量间关系及各自分布的强大工具。我们可以使用Pandas的 `scatter_matrix` 函数。

首先，我们计算指标的日收益率（或增长率），这能更好地反映每日变化关系。

![](img/2eb619e741e38e508b6490c17890f4bb_69.png)

![](img/2eb619e741e38e508b6490c17890f4bb_71.png)

```python
import numpy as np
# 计算日收益率：当日值 / 前一日值 - 1
returns = np.log(data / data.shift(1))
print(returns.head())
```

![](img/2eb619e741e38e508b6490c17890f4bb_73.png)

接下来，使用 `scatter_matrix` 绘制图形。

![](img/2eb619e741e38e508b6490c17890f4bb_75.png)

![](img/2eb619e741e38e508b6490c17890f4bb_77.png)

以下是绘制散点图矩阵的代码：

![](img/2eb619e741e38e508b6490c17890f4bb_79.png)

```python
from pandas.plotting import scatter_matrix

![](img/2eb619e741e38e508b6490c17890f4bb_81.png)

![](img/2eb619e741e38e508b6490c17890f4bb_83.png)

# 绘制散点图矩阵
scatter_matrix(returns,
               alpha=0.5,        # 设置点的透明度，避免重叠
               figsize=(10, 10),
               diagonal='kde',   # 对角线显示核密度估计图
               hist_kwds={'bins': 50}) # 如果对角线用直方图，可设置分箱数
plt.suptitle('Scatter Matrix of Returns (SPX vs VIX)')
plt.show()
```

![](img/2eb619e741e38e508b6490c17890f4bb_85.png)

*   **散点图**：非对角线上的图是两两指标的散点图，可以直观看到SPX收益率和VIX收益率呈负相关（点集中在第二、四象限）。
*   **对角线图**：显示每个指标自身的分布情况。`diagonal=‘kde’` 会绘制平滑的核密度估计曲线；若改为 `diagonal=‘hist’` 则绘制直方图。

![](img/2eb619e741e38e508b6490c17890f4bb_87.png)

![](img/2eb619e741e38e508b6490c17890f4bb_89.png)

![](img/2eb619e741e38e508b6490c17890f4bb_91.png)

## 总结

本节课中我们一起学习了金融指标相关性分析的基本方法：
1.  **数据提取**：从数据集中精准选取需要分析的指标列。
2.  **可视化探索**：
    *   通过**并排折线图**观察各自走势。
    *   利用**双Y轴折线图**在同一坐标系下对比两个量纲不同的指标，直观发现负相关现象。
    *   通过**散点图矩阵**综合观察变量间的相关关系以及各自的分布特征，其中散点图能清晰展示负相关趋势，对角线图则展示了数据分布。

![](img/2eb619e741e38e508b6490c17890f4bb_93.png)

![](img/2eb619e741e38e508b6490c17890f4bb_95.png)

这些可视化方法是量化分析中探索数据关系的首要且关键步骤，能为我们后续的统计建模（如回归分析）提供直观依据和方向。下一节，我们可以在此基础上，量化这种相关关系，例如计算相关系数或进行线性回归拟合。