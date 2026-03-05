# Python量化交易：P10：10.4-指标相关情况分析 📈

![](img/e46feb2692b73b0185692f010a02bb5b_1.png)

![](img/e46feb2692b73b0185692f010a02bb5b_3.png)

在本节课中，我们将学习如何分析两个金融指标（如标普500指数和恐慌指数）之间的相关性。我们将通过数据可视化（折线图、散点图）和回归分析来探索它们之间的关系，让初学者也能轻松理解。

![](img/e46feb2692b73b0185692f010a02bb5b_5.png)

![](img/e46feb2692b73b0185692f010a02bb5b_6.png)

![](img/e46feb2692b73b0185692f010a02bb5b_8.png)

---

![](img/e46feb2692b73b0185692f010a02bb5b_9.png)

上一节我们介绍了如何计算指标的增长率，本节中我们来看看如何分析两个指标之间的相关关系。

![](img/e46feb2692b73b0185692f010a02bb5b_11.png)

![](img/e46feb2692b73b0185692f010a02bb5b_12.png)

![](img/e46feb2692b73b0185692f010a02bb5b_13.png)

![](img/e46feb2692b73b0185692f010a02bb5b_14.png)

回归分析的核心是观察变量之间是否存在某种关系或相互影响。我们的目标是分析标普500指数（SPX）和恐慌指数（VIX）之间的关系。

![](img/e46feb2692b73b0185692f010a02bb5b_16.png)

![](img/e46feb2692b73b0185692f010a02bb5b_17.png)

以下是分析流程的第一步：选择并提取数据。

![](img/e46feb2692b73b0185692f010a02bb5b_19.png)

![](img/e46feb2692b73b0185692f010a02bb5b_20.png)

```python
# 从原始数据中提取我们关心的两列：SPX 和 VIX
data2 = data[['SPX', 'VIX']]
print(data2.head())
```

![](img/e46feb2692b73b0185692f010a02bb5b_21.png)

![](img/e46feb2692b73b0185692f010a02bb5b_22.png)

![](img/e46feb2692b73b0185692f010a02bb5b_23.png)

![](img/e46feb2692b73b0185692f010a02bb5b_25.png)

数据读取完成后，我们可以开始进行可视化分析。最简单直接的方法是绘制折线图。

![](img/e46feb2692b73b0185692f010a02bb5b_27.png)

![](img/e46feb2692b73b0185692f010a02bb5b_28.png)

```python
# 分别绘制两个指标的折线图
data2.plot(subplots=True, figsize=(10, 6))
```

![](img/e46feb2692b73b0185692f010a02bb5b_30.png)

![](img/e46feb2692b73b0185692f010a02bb5b_31.png)

然而，将两个指标分别画在两个子图中，难以直观比较它们的趋势。一个更好的方法是将它们绘制在同一个坐标系中，但使用不同的Y轴。

![](img/e46feb2692b73b0185692f010a02bb5b_32.png)

![](img/e46feb2692b73b0185692f010a02bb5b_33.png)

![](img/e46feb2692b73b0185692f010a02bb5b_35.png)

![](img/e46feb2692b73b0185692f010a02bb5b_36.png)

```python
# 在同一个图中绘制双Y轴折线图，以便观察趋势
ax = data2['SPX'].plot(figsize=(12, 6), label='SPX', color='blue')
ax2 = ax.twinx()
data2['VIX'].plot(ax=ax2, label='VIX', color='green')
ax.legend(loc='upper left')
ax2.legend(loc='upper right')
```

![](img/e46feb2692b73b0185692f010a02bb5b_37.png)

![](img/e46feb2692b73b0185692f010a02bb5b_38.png)

![](img/e46feb2692b73b0185692f010a02bb5b_40.png)

为了更清晰地观察短期内的关系，我们可以只选取一段时间的数据进行分析。

![](img/e46feb2692b73b0185692f010a02bb5b_42.png)

![](img/e46feb2692b73b0185692f010a02bb5b_44.png)

```python
# 选取2010年至2012年的数据进行绘图
data_subset = data2['2010-01-01':'2012-12-31']
ax = data_subset['SPX'].plot(figsize=(12, 6), label='SPX', color='blue')
ax2 = ax.twinx()
data_subset['VIX'].plot(ax=ax2, label='VIX', color='green')
```

通过观察图形，我们可以发现一个初步规律：当SPX指数下降时，VIX指数往往上升，显示出一种负相关关系。

![](img/e46feb2692b73b0185692f010a02bb5b_46.png)

![](img/e46feb2692b73b0185692f010a02bb5b_47.png)

除了折线图，散点图是观察两个变量关系的另一种有效工具。在绘制散点图前，我们通常使用数据的对数收益率。

```python
# 计算对数收益率
returns = np.log(data2 / data2.shift(1))
returns = returns.dropna() # 删除包含NaN值的行
print(returns.head())
```

![](img/e46feb2692b73b0185692f010a02bb5b_49.png)

![](img/e46feb2692b73b0185692f010a02bb5b_50.png)

以下是绘制散点图矩阵的方法，它可以同时展示单个变量的分布和两个变量之间的关系。

![](img/e46feb2692b73b0185692f010a02bb5b_52.png)

![](img/e46feb2692b73b0185692f010a02bb5b_54.png)

![](img/e46feb2692b73b0185692f010a02bb5b_55.png)

```python
# 使用pandas的绘图功能绘制散点图矩阵，对角线显示核密度估计图
pd.plotting.scatter_matrix(returns,
                           alpha=0.5,        # 设置点的透明度
                           figsize=(10, 10),
                           diagonal='kde',   # 对角线画核密度估计图
                           hist_kwds={'bins': 50}) # 如果画直方图，设置分箱数
```

![](img/e46feb2692b73b0185692f010a02bb5b_57.png)

从散点图中，我们可以更清晰地看到SPX收益率与VIX收益率呈负相关趋势。为了量化这种关系，下一步就是构建回归方程。

![](img/e46feb2692b73b0185692f010a02bb5b_59.png)

![](img/e46feb2692b73b0185692f010a02bb5b_60.png)

![](img/e46feb2692b73b0185692f010a02bb5b_61.png)

![](img/e46feb2692b73b0185692f010a02bb5b_62.png)

![](img/e46feb2692b73b0185692f010a02bb5b_63.png)

---

![](img/e46feb2692b73b0185692f010a02bb5b_64.png)

![](img/e46feb2692b73b0185692f010a02bb5b_66.png)

![](img/e46feb2692b73b0185692f010a02bb5b_67.png)

本节课中我们一起学习了如何通过数据可视化（双Y轴折线图、散点图矩阵）来初步分析两个金融指标之间的相关关系。我们发现标普500指数（SPX）与恐慌指数（VIX）之间存在明显的负相关趋势，这为后续进行更精确的回归分析奠定了基础。