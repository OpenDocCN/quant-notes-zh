# Python金融量化与股票交易：P10：10.10.4-指标相关性分析 📈

![](img/23a24dc882b28c38077e66ed05d50dcc_1.png)

在本节课中，我们将学习如何使用Python进行金融指标的回归分析。我们将通过可视化图表来探索两个关键指标（标普500指数和VIX恐慌指数）之间的关系，并最终通过回归分析量化这种关系。

![](img/23a24dc882b28c38077e66ed05d50dcc_3.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_5.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_7.png)

## 概述

![](img/23a24dc882b28c38077e66ed05d50dcc_9.png)

回归分析的核心是探究变量之间的关系。具体来说，我们将观察标普500指数（SPX）和VIX恐慌指数（VX）的走势或增长率之间是否存在相互影响。本节将引导你完成从数据选择、可视化到回归分析的完整流程。

![](img/23a24dc882b28c38077e66ed05d50dcc_11.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_13.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_15.png)

## 第一步：数据选择与准备

![](img/23a24dc882b28c38077e66ed05d50dcc_17.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_19.png)

首先，我们需要从数据集中提取我们感兴趣的两个指标列。我们使用一个名为`data2`的DataFrame，并从中选择`SPX`和`VX`这两列数据。

![](img/23a24dc882b28c38077e66ed05d50dcc_21.png)

以下是数据准备的代码：
```python
# 从data2中选择SPX和VX两列数据
data = data2[['SPX', 'VX']]
# 打印数据以确认
print(data.head())
```
执行上述代码后，我们便获得了用于后续分析的数据集。

![](img/23a24dc882b28c38077e66ed05d50dcc_23.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_25.png)

## 第二步：初步可视化观察

![](img/23a24dc882b28c38077e66ed05d50dcc_27.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_29.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_31.png)

为了直观感受两个指标之间的关系，最直接的方法是绘制图表。我们首先尝试将两个指标的折线图画在同一张图上。

![](img/23a24dc882b28c38077e66ed05d50dcc_33.png)

以下是绘制双轴折线图的代码：
```python
import matplotlib.pyplot as plt

![](img/23a24dc882b28c38077e66ed05d50dcc_35.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_37.png)

# 创建图形和主坐标轴
fig, ax1 = plt.subplots(figsize=(10, 6))

![](img/23a24dc882b28c38077e66ed05d50dcc_39.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_41.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_43.png)

# 绘制SPX数据（左侧Y轴）
color = ‘tab:blue’
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘SPX’, color=color)
ax1.plot(data.index, data[‘SPX’], color=color)
ax1.tick_params(axis=‘y’, labelcolor=color)

![](img/23a24dc882b28c38077e66ed05d50dcc_45.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_47.png)

# 创建共享X轴的第二个坐标轴（右侧Y轴）
ax2 = ax1.twinx()
color = ‘tab:green’
ax2.set_ylabel(‘VX’, color=color)
ax2.plot(data.index, data[‘VX’], color=color)
ax2.tick_params(axis=‘y’, labelcolor=color)

![](img/23a24dc882b28c38077e66ed05d50dcc_49.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_51.png)

fig.tight_layout()
plt.show()
```
然而，当数据时间跨度较长（例如8年）时，图表中的细节和趋势可能不够清晰。为了提高可读性，我们可以选择其中一段时间（例如2010年至2012年）的数据进行绘制。

![](img/23a24dc882b28c38077e66ed05d50dcc_53.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_55.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_57.png)

以下是筛选数据并重新绘图的代码：
```python
# 筛选2010年至2012年的数据
data_subset = data[‘2010-01-01’:‘2012-12-31’]

![](img/23a24dc882b28c38077e66ed05d50dcc_59.png)

# 使用相同的双轴代码绘制data_subset
# … (绘图代码与上文相同，将data替换为data_subset)
```
通过观察缩短时间范围后的图表，我们可以更清晰地看到趋势：当蓝色线（SPX指数）下降时，绿色线（VIX恐慌指数）往往上升，这表明两者可能存在负相关关系。

![](img/23a24dc882b28c38077e66ed05d50dcc_61.png)

## 第三步：散点图与分布观察

![](img/23a24dc882b28c38077e66ed05d50dcc_63.png)

除了折线图，散点图矩阵是观察变量间关系及各自分布的强大工具。在绘制散点图前，我们通常使用收益率数据（对数收益率）进行分析，以消除价格序列的非平稳性。

以下是计算对数收益率并绘制散点图矩阵的代码：
```python
import numpy as np
import pandas as pd

![](img/23a24dc882b28c38077e66ed05d50dcc_65.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_67.png)

# 计算对数收益率
returns = np.log(data / data.shift(1)).dropna()

# 使用pandas的scatter_matrix函数绘制散点图矩阵
pd.plotting.scatter_matrix(returns,
                           alpha=0.5, # 设置点的透明度，避免重叠
                           figsize=(12, 8),
                           diagonal=‘kde’, # 对角线绘制核密度估计图
                           hist_kwds={‘bins’: 50}) # 如果对角线用直方图，可设置分箱数
plt.show()
```
在这个矩阵图中：
*   对角线上的图是每个变量自身的**核密度估计图**，它比直方图更平滑地展示了数据的分布形态。
*   非对角线上的图是两个变量之间的散点图，可以直观展示它们的相关性。从散点图中，我们能更明确地看到`SPX`收益率与`VX`收益率呈负向聚集的趋势。

![](img/23a24dc882b28c38077e66ed05d50dcc_69.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_71.png)

## 第四步：进行回归分析

![](img/23a24dc882b28c38077e66ed05d50dcc_73.png)

散点图展示了关系，而回归分析可以量化这种关系。我们可以在散点图的基础上，添加一条回归线，使得关系一目了然。

![](img/23a24dc882b28c38077e66ed05d50dcc_75.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_77.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_79.png)

以下是使用`seaborn`库绘制带回归线的散点图的代码：
```python
import seaborn as sns

![](img/23a24dc882b28c38077e66ed05d50dcc_81.png)

# 绘制SPX与VX收益率的散点图，并添加线性回归线和95%置信区间
sns.regplot(x=returns[‘SPX’], y=returns[‘VX’])
plt.xlabel(‘SPX Returns’)
plt.ylabel(‘VX Returns’)
plt.title(‘Regression Analysis: SPX vs VX Returns’)
plt.show()
```
`seaborn`的`regplot`函数会自动拟合一条线性回归线并绘制在图上，同时阴影部分表示回归的置信区间。这条线的斜率和截距就是回归方程的系数，量化了`VX`收益率随`SPX`收益率变化而变化的平均程度。

![](img/23a24dc882b28c38077e66ed05d50dcc_83.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_85.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_87.png)

## 总结

![](img/23a24dc882b28c38077e66ed05d50dcc_89.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_91.png)

本节课中，我们一起学习了金融指标相关性分析的完整流程。
1.  我们首先**选择并准备了**需要分析的数据列（SPX和VX）。
2.  接着，我们通过**绘制双轴折线图**进行初步观察，发现两者存在此消彼长的现象。
3.  然后，我们计算收益率并**绘制散点图矩阵**，从分布和相关性的角度更全面地审视数据。
4.  最后，我们通过**回归分析**，在散点图上添加了回归线，从而量化了两个指标收益率之间的负相关关系。

![](img/23a24dc882b28c38077e66ed05d50dcc_93.png)

![](img/23a24dc882b28c38077e66ed05d50dcc_95.png)

整个分析过程体现了数据探索的典型思路：从直观可视化到定量分析，逐步深入地理解变量间的内在联系。