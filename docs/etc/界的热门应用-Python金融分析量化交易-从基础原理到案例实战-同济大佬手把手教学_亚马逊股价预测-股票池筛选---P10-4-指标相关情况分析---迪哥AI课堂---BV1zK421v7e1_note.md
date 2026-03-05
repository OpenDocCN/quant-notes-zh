# Python金融分析量化交易：P10：4-指标相关情况分析 📊

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_1.png)

在本节课中，我们将学习如何分析金融指标之间的相关性。我们将通过可视化图表和回归分析，来探索两个关键指标（例如标普500指数和VIX恐慌指数）之间的关系，从而理解它们如何相互影响。

上一节我们介绍了数据预处理和增长率计算，本节中我们来看看如何分析指标间的相关性。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_3.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_5.png)

## 第一步：数据准备

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_7.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_9.png)

首先，我们需要从数据集中提取出我们想要分析的两个指标列。这里我们以标普500指数（SPX）和VIX恐慌指数为例。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_11.png)

以下是提取数据的代码：

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_13.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_15.png)

```python
# 假设我们的数据集名为 data2
data = data2[['SPX', 'VIX']]
print(data.head())
```

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_17.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_19.png)

执行这段代码后，我们就得到了一个只包含`SPX`和`VIX`两列数据的新DataFrame。

## 第二步：初步可视化观察

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_21.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_23.png)

拿到数据后，最直观的方法是将其可视化。我们可以先尝试将两个指标的走势画在同一张图上观察。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_25.png)

以下是绘制双轴折线图的代码：

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_27.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_29.png)

```python
import matplotlib.pyplot as plt

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_31.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_33.png)

fig, ax1 = plt.subplots(figsize=(10, 6))

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_35.png)

color = ‘tab:blue’
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘SPX’, color=color)
ax1.plot(data.index, data[‘SPX’], color=color)
ax1.tick_params(axis=‘y’, labelcolor=color)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_37.png)

ax2 = ax1.twinx()
color = ‘tab:orange’
ax2.set_ylabel(‘VIX’, color=color)
ax2.plot(data.index, data[‘VIX’], color=color)
ax2.tick_params(axis=‘y’, labelcolor=color)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_39.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_41.png)

fig.tight_layout()
plt.title(‘SPX vs VIX’)
plt.show()
```

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_43.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_45.png)

通过观察图表，我们可以初步发现，当SPX指数下降时，VIX指数往往上升，呈现出一种负相关趋势。为了更清晰地观察短期关系，我们可以只选取特定时间段（例如2010年至2012年）的数据进行绘图。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_47.png)

## 第三步：散点图与分布分析

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_49.png)

除了折线图，散点图矩阵是观察多个变量间两两关系的强大工具。它能同时展示单个变量的分布和两个变量之间的散点关系。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_51.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_53.png)

以下是使用Pandas绘制散点图矩阵的代码：

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_55.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_57.png)

```python
from pandas.plotting import scatter_matrix

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_59.png)

# 计算每日收益率（或变化率），以便分析变化量之间的关系
returns = np.log(data / data.shift(1)).dropna()

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_61.png)

# 绘制散点图矩阵
scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal=‘kde’, hist_kwds={‘bins’: 50})
plt.show()
```

在这张矩阵图中：
*   **对角线** 上的图是每个变量的核密度估计图，展示了该变量自身的分布情况。
*   **非对角线** 上的图是两个变量之间的散点图，可以直观地看出它们的关系形态（如线性、非线性）和相关性强弱。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_63.png)

从散点图中，我们可以更明确地看到SPX收益率与VIX收益率之间存在负向关系。

## 第四步：量化相关关系

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_65.png)

可视化给了我们直观感受，接下来我们需要用数值来量化这种关系。最常用的方法是计算**相关系数**。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_67.png)

以下是计算皮尔逊相关系数的代码：

```python
correlation_matrix = returns.corr()
print(correlation_matrix)
```

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_69.png)

输出结果是一个相关系数矩阵。`SPX`和`VIX`之间的相关系数应该是一个负值（例如-0.7左右），这从数值上证实了它们之间存在显著的负相关。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_71.png)

如果我们想在散点图上直观地加上一条趋势线（即一元线性回归线），可以使用Seaborn库。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_73.png)

以下是使用Seaborn绘制带回归线的散点图代码：

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_75.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_77.png)

```python
import seaborn as sns

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_79.png)

sns.regplot(x=returns[‘SPX’], y=returns[‘VIX’])
plt.xlabel(‘SPX Daily Returns’)
plt.ylabel(‘VIX Daily Returns’)
plt.title(‘Regression Line: VIX vs SPX Returns’)
plt.show()
```

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_81.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_83.png)

这条回归线的斜率直观地体现了两个变量变化的关系。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_85.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_87.png)

## 总结

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_89.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_91.png)

本节课中我们一起学习了金融指标相关性分析的完整流程。

1.  **数据准备**：从数据集中筛选出目标指标。
2.  **可视化观察**：通过**双轴折线图**观察指标随时间变化的趋势关系。
3.  **深入分析**：利用**散点图矩阵**同时观察变量分布和两两关系。
4.  **量化关系**：通过计算**相关系数**和绘制**回归线**，用数值量化变量间的相关性强弱和方向。

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_93.png)

![](img/3a0e59aa83678a3e7d4b6b004d118a2b_95.png)

通过这些步骤，我们可以清晰地得出结论：在本案例中，标普500指数（SPX）与VIX恐慌指数之间存在**负相关关系**，即市场下跌时，恐慌情绪倾向于上升。这是金融市场中一个经典的风险关联现象。掌握这种分析方法，是进行量化策略研究和风险管理的基石。