# Python金融分析与量化交易实战：9.6：指标相关情况分析 📊

![](img/f921ea2968448c9ab0603ade680d1507_1.png)

在本节课中，我们将学习如何分析不同金融指标之间的关系。我们将通过画图和回归分析两种方法，直观地探索变量之间的相互影响，例如标普指数与恐慌指数之间的关联。

---

![](img/f921ea2968448c9ab0603ade680d1507_3.png)

![](img/f921ea2968448c9ab0603ade680d1507_5.png)

## 数据准备

![](img/f921ea2968448c9ab0603ade680d1507_7.png)

![](img/f921ea2968448c9ab0603ade680d1507_9.png)

上一节我们介绍了数据处理的基础操作，本节中我们来看看如何选取特定指标进行分析。首先，我们需要从数据集中提取出我们关心的两个指标列。

![](img/f921ea2968448c9ab0603ade680d1507_11.png)

```python
data2 = data2[['SPX', 'VIX']]
print(data2.head())
```

![](img/f921ea2968448c9ab0603ade680d1507_13.png)

![](img/f921ea2968448c9ab0603ade680d1507_15.png)

以上代码从名为`data2`的DataFrame中选取了‘SPX’（标普500指数）和‘VIX’（恐慌指数）两列数据，并打印了前几行以确认数据读取正确。

![](img/f921ea2968448c9ab0603ade680d1507_17.png)

![](img/f921ea2968448c9ab0603ade680d1507_19.png)

---

## 可视化分析：折线图

![](img/f921ea2968448c9ab0603ade680d1507_21.png)

在分析关系之前，最直接的方法是进行可视化。以下是绘制两个指标折线图的步骤。

![](img/f921ea2968448c9ab0603ade680d1507_23.png)

首先，我们尝试将两个指标画在同一张图中。

![](img/f921ea2968448c9ab0603ade680d1507_25.png)

![](img/f921ea2968448c9ab0603ade680d1507_27.png)

```python
data2.plot()
```

![](img/f921ea2968448c9ab0603ade680d1507_29.png)

![](img/f921ea2968448c9ab0603ade680d1507_31.png)

执行后我们发现，由于两个指标的数值范围差异较大，放在同一坐标系下难以观察趋势。因此，我们需要改进绘图方法。

![](img/f921ea2968448c9ab0603ade680d1507_33.png)

我们可以使用双Y轴的方法，将两个序列绘制在同一张图的不同坐标轴上，以便更清晰地对比趋势。

![](img/f921ea2968448c9ab0603ade680d1507_35.png)

```python
ax = data2['SPX'].plot(figsize=(10,6), color='blue')
ax2 = ax.twinx()
data2['VIX'].plot(ax=ax2, color='green')
```

![](img/f921ea2968448c9ab0603ade680d1507_37.png)

![](img/f921ea2968448c9ab0603ade680d1507_39.png)

为了更清晰地观察短期内的关系，我们可以只选取特定时间段的数据，例如2010年到2012年。

![](img/f921ea2968448c9ab0603ade680d1507_41.png)

![](img/f921ea2968448c9ab0603ade680d1507_43.png)

```python
data_subset = data2['2010-01-01':'2012-12-31']
ax = data_subset['SPX'].plot(figsize=(10,6), color='blue')
ax2 = ax.twinx()
data_subset['VIX'].plot(ax=ax2, color='green')
```

![](img/f921ea2968448c9ab0603ade680d1507_45.png)

通过观察图形，我们可以初步发现：当蓝色线（SPX标普指数）下降时，绿色线（VIX恐慌指数）往往上升，这表明两者可能存在负相关关系。

![](img/f921ea2968448c9ab0603ade680d1507_47.png)

---

![](img/f921ea2968448c9ab0603ade680d1507_49.png)

![](img/f921ea2968448c9ab0603ade680d1507_51.png)

## 可视化分析：散点图与分布图

![](img/f921ea2968448c9ab0603ade680d1507_53.png)

![](img/f921ea2968448c9ab0603ade680d1507_55.png)

除了折线图，散点图矩阵是观察多变量间关系及各自分布的强大工具。以下是使用Pandas绘制散点图矩阵的方法。

![](img/f921ea2968448c9ab0603ade680d1507_57.png)

首先，我们计算指标的日收益率，这能更好地反映每日的变化关系。

![](img/f921ea2968448c9ab0603ade680d1507_59.png)

```python
returns = np.log(data2 / data2.shift(1))
returns = returns.dropna()
```

![](img/f921ea2968448c9ab0603ade680d1507_61.png)

接下来，我们使用`pandas.plotting.scatter_matrix`函数来绘制散点图矩阵。

```python
pd.plotting.scatter_matrix(returns,
                           alpha=0.5,        # 设置点的透明度
                           figsize=(10, 10),
                           diagonal='hist',   # 对角线显示直方图
                           hist_kwds={'bins': 50}) # 直方图参数
```

执行代码后，我们会得到一个2x2的矩阵图。对角线上的图是每个指标自身的分布直方图。非对角线上的图则是两两指标之间的散点图，可以直观地看到它们的相关性。

![](img/f921ea2968448c9ab0603ade680d1507_63.png)

我们也可以将对角线的图从直方图改为核密度估计图，它会呈现更平滑的分布曲线。

![](img/f921ea2968448c9ab0603ade680d1507_65.png)

```python
pd.plotting.scatter_matrix(returns,
                           alpha=0.5,
                           figsize=(10, 10),
                           diagonal='kde')   # 对角线显示核密度估计图
```

从散点图中，我们可以更明确地看到SPX收益率与VIX收益率呈负相关趋势，即点云整体向右下角倾斜。

![](img/f921ea2968448c9ab0603ade680d1507_67.png)

---

![](img/f921ea2968448c9ab0603ade680d1507_69.png)

## 核心概念：相关性分析

![](img/f921ea2968448c9ab0603ade680d1507_71.png)

通过上述可视化，我们定性地观察到了指标间的负相关关系。在量化分析中，我们通常还需要定量地衡量这种关系的强弱，这就是相关性分析。最常用的指标是**皮尔逊相关系数**，其公式为：

![](img/f921ea2968448c9ab0603ade680d1507_73.png)

![](img/f921ea2968448c9ab0603ade680d1507_75.png)

**公式：**
\[
\rho_{X,Y} = \frac{\text{cov}(X,Y)}{\sigma_X \sigma_Y}
\]

![](img/f921ea2968448c9ab0603ade680d1507_77.png)

其中，\(\text{cov}(X,Y)\) 是X和Y的协方差，\(\sigma_X\) 和 \(\sigma_Y\) 分别是X和Y的标准差。相关系数 \(\rho\) 的取值范围在-1到1之间。接近-1表示强负相关，接近1表示强正相关，接近0表示无线性相关。

![](img/f921ea2968448c9ab0603ade680d1507_79.png)

![](img/f921ea2968448c9ab0603ade680d1507_81.png)

在Python中，我们可以直接用Pandas计算相关系数矩阵：

![](img/f921ea2968448c9ab0603ade680d1507_83.png)

![](img/f921ea2968448c9ab0603ade680d1507_85.png)

```python
correlation_matrix = returns.corr()
print(correlation_matrix)
```

![](img/f921ea2968448c9ab0603ade680d1507_87.png)

![](img/f921ea2968448c9ab0603ade680d1507_89.png)

这行代码将输出一个矩阵，其中就包含了SPX和VIX之间的相关系数，预期结果应该是一个负值，从而定量证实我们可视化观察到的负相关关系。

---

![](img/f921ea2968448c9ab0603ade680d1507_91.png)

![](img/f921ea2968448c9ab0603ade680d1507_93.png)

本节课中我们一起学习了如何通过可视化的方法（双Y轴折线图、散点图矩阵）来探索金融指标间的相互关系，并引入了相关性分析的核心概念。这些是金融数据探索性分析的基础，能帮助我们直观、定量地理解市场指标间的联动效应。