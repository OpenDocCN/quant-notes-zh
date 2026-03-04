# Python金融量化：P7：指标相关情况分析 📊

在本节课中，我们将要学习如何使用Python进行回归分析，以探索金融指标（如标普500指数和VIX恐慌指数）之间的相互关系。我们将通过数据可视化（折线图、散点图）和统计分析来直观地理解变量间的关联。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_1.png)

---

![](img/8a7174918b8d5ef69836fabc2feb3e9f_3.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_5.png)

上一节我们介绍了数据处理的基础操作，本节中我们来看看如何分析两个指标之间的相关性。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_7.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_9.png)

回归分析的核心是观察变量之间的关系或相互影响。我们将选择两个指标：标普500指数（SPX）和VIX恐慌指数（VIX），并分析它们之间的走势关联。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_11.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_13.png)

以下是分析流程的第一步：数据准备。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_15.png)

```python
# 从data2数据集中选取‘SPX’和‘VIX’两列数据
data = data2[['SPX', 'VIX']]
print(data.head())
```

![](img/8a7174918b8d5ef69836fabc2feb3e9f_17.png)

数据读取完成后，索引没有问题，可以直接进行下一步。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_19.png)

---

![](img/8a7174918b8d5ef69836fabc2feb3e9f_21.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_23.png)

上一节我们完成了数据选取，本节中我们来看看如何通过可视化初步观察关系。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_25.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_27.png)

最简单的方法是直接绘制图表。以下是绘制两个指标折线图的代码。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_29.png)

```python
# 分别绘制两个指标的折线图
data.plot(subplots=True)
```

![](img/8a7174918b8d5ef69836fabc2feb3e9f_31.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_33.png)

执行后，结果会显示在两个独立的子图中。但这样难以直接比较趋势。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_35.png)

为了更清晰地观察，我们可以将两个指标绘制在同一张图中，但使用不同的Y轴。以下是实现代码。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_37.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_39.png)

```python
# 在同一图中绘制双Y轴折线图
ax = data['SPX'].plot()
data['VIX'].plot(ax=ax, secondary_y=True)
```

![](img/8a7174918b8d5ef69836fabc2feb3e9f_41.png)

执行后，可以初步观察趋势。但若数据时间跨度太长（如8年），变化过多，趋势可能不清晰。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_43.png)

为了更明显，我们可以选取其中一段时间的数据（例如2010年至2012年）并绘制更大的图。以下是代码示例。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_45.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_47.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_49.png)

```python
# 选取特定时间段的数据并绘图
selected_data = data['2010-01-01':'2012-12-31']
selected_data.plot(figsize=(12, 6))
```

![](img/8a7174918b8d5ef69836fabc2feb3e9f_51.png)

观察选取后的数据图，可以发现：当SPX指数下降时，VIX指数往往上升，反之亦然。这初步表明了两者之间存在负相关关系。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_53.png)

画图通常不是一步到位的。一般先绘制基本图形，发现问题后再进行改进，以使结果更清晰。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_55.png)

---

![](img/8a7174918b8d5ef69836fabc2feb3e9f_57.png)

除了折线图，散点图也是观察变量关系的有效工具。在绘制散点图前，我们可以先计算指标的日收益率（增长率）。

以下是计算日收益率的代码。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_59.png)

```python
# 计算日收益率（增长率）
returns = np.log(data / data.shift(1))
print(returns.head())
```

![](img/8a7174918b8d5ef69836fabc2feb3e9f_61.png)

得到收益率数据后，我们可以绘制散点图矩阵来观察变量自身的分布以及两两之间的关系。

以下是使用Pandas绘制散点图矩阵的代码。图中对角线可以显示每个变量自身的分布（直方图或核密度估计图）。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_63.png)

```python
# 绘制散点图矩阵，对角线显示直方图
pd.plotting.scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal='hist', hist_kwds={'bins': 50})
```

![](img/8a7174918b8d5ef69836fabc2feb3e9f_65.png)

执行后，可以看到一个2x2的矩阵图。对角线是每个指标自身的直方图。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_67.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_69.png)

我们也可以将对角线的图形改为更平滑的核密度估计（KDE）图。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_71.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_73.png)

```python
# 绘制散点图矩阵，对角线显示核密度估计图
pd.plotting.scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal='kde')
```

![](img/8a7174918b8d5ef69836fabc2feb3e9f_75.png)

核密度估计图与直方图趋势类似，但曲线更光滑。它本质上是将数据分成非常多的区间（bins）后得到的平滑曲线。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_77.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_79.png)

通过散点图，可以更清晰地观察到SPX和VIX收益率之间呈现负相关趋势，这与折线图的观察结果一致。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_81.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_83.png)

在实际分析中，选择哪种图形取决于个人偏好，目标是选择看起来最舒服、最能清晰展示关系的图形。

![](img/8a7174918b8d5ef69836fabc2feb3e9f_85.png)

---

![](img/8a7174918b8d5ef69836fabc2feb3e9f_87.png)

![](img/8a7174918b8d5ef69836fabc2feb3e9f_89.png)

本节课中我们一起学习了如何通过Python进行金融指标的回归分析。我们掌握了数据选取、双轴折线图绘制以观察趋势、以及散点图矩阵绘制以分析分布与相关性的方法。这些可视化工具能帮助我们直观地理解变量间的负相关等关系，为后续的量化分析打下基础。