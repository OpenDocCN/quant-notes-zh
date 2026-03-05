# 人生苦短，我学量化！：5：指标相关情况分析

![](img/b6e2be4a883ddacd2033569673e14eb8_0.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_2.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_4.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_6.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_8.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_10.png)

在本节课中，我们将学习如何分析两个金融指标（例如标普500指数和VIX恐慌指数）之间的相关性。我们将通过数据可视化来直观地观察它们的关系，并介绍绘制散点图矩阵和核密度估计图的方法。

![](img/b6e2be4a883ddacd2033569673e14eb8_12.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_14.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_16.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_18.png)

## 数据准备与初步观察

![](img/b6e2be4a883ddacd2033569673e14eb8_20.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_22.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_24.png)

上一节我们介绍了数据处理，本节中我们来看看如何选取特定指标进行分析。首先，我们需要从数据集中提取我们关心的两个指标列。

![](img/b6e2be4a883ddacd2033569673e14eb8_26.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_28.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_30.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_32.png)

以下是提取数据的代码示例：
```python
# 假设 data2 是包含所有数据的 DataFrame
data = data2[['SPX', 'VIX']]
print(data.head())
```
执行这段代码后，我们将得到一个只包含‘SPX’（标普500指数）和‘VIX’（恐慌指数）两列的新DataFrame。通过打印数据，我们可以确认数据已正确加载。

![](img/b6e2be4a883ddacd2033569673e14eb8_34.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_36.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_38.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_40.png)

## 绘制双轴折线图

![](img/b6e2be4a883ddacd2033569673e14eb8_42.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_44.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_46.png)

直接观察原始数据表格难以发现趋势。最直观的方法是绘制图表。最初，我们可以将两个指标分别画在两个子图中。

![](img/b6e2be4a883ddacd2033569673e14eb8_48.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_50.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_52.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_54.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_56.png)

以下是绘制两个独立子图的代码：
```python
data.plot(subplots=True)
```
然而，将两个指标放在不同的坐标系中不利于直接比较它们的走势。更好的方法是将它们绘制在同一张图上，但使用左右两个不同的Y轴。

![](img/b6e2be4a883ddacd2033569673e14eb8_58.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_60.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_62.png)

以下是创建双Y轴折线图的代码：
```python
ax = data['SPX'].plot(figsize=(10, 6))
data['VIX'].plot(ax=ax, secondary_y=True)
```
为了更清晰地观察短期关系，我们可以只选取一段时间的数据（例如2010年至2012年）进行绘图。通过观察图形，我们可以初步判断：当SPX指数下降时，VIX指数往往上升，这表明两者可能存在负相关关系。

![](img/b6e2be4a883ddacd2033569673e14eb8_64.png)

## 绘制散点图矩阵

![](img/b6e2be4a883ddacd2033569673e14eb8_66.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_68.png)

除了折线图，散点图是观察两个变量关系的另一种有效工具。在绘制之前，我们可能需要计算指标的日收益率。

![](img/b6e2be4a883ddacd2033569673e14eb8_70.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_72.png)

以下是计算对数收益率的代码：
```python
returns = np.log(data / data.shift(1))
returns = returns.dropna() # 删除第一个NaN值
```
Pandas提供了便捷的函数来绘制散点图矩阵，它可以同时展示单变量的分布和双变量之间的关系。

![](img/b6e2be4a883ddacd2033569673e14eb8_74.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_76.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_78.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_80.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_82.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_84.png)

以下是绘制散点图矩阵的代码示例：
```python
pd.plotting.scatter_matrix(returns,
                           alpha=0.2, # 设置点的透明度
                           figsize=(10, 10),
                           diagonal='kde', # 对角线绘制核密度估计图
                           hist_kwds={'bins': 50})
```
在这个矩阵图中：
*   **对角线** 展示的是每个变量自身的分布。我们可以选择绘制直方图(`diagonal='hist'`)或更平滑的核密度估计图(`diagonal='kde'`)。
*   **非对角线** 的散点图展示了两个变量之间的关系。从SPX和VIX的散点图中，我们可以更清晰地看到数据点从左上到右下分布的趋势，这进一步印证了它们之间的负相关性。

![](img/b6e2be4a883ddacd2033569673e14eb8_86.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_88.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_90.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_92.png)

## 总结

![](img/b6e2be4a883ddacd2033569673e14eb8_94.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_96.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_98.png)

![](img/b6e2be4a883ddacd2033569673e14eb8_100.png)

本节课中我们一起学习了金融指标相关性分析的基本可视化方法。我们首先学习了如何提取特定指标数据，然后通过绘制**双Y轴折线图**在同一坐标系中对比指标走势。接着，我们引入了**散点图矩阵**，它可以同时展示变量的分布和变量间的关系，并通过**核密度估计图**获得更平滑的分布曲线。这些图表帮助我们直观地判断出标普500指数与恐慌指数之间存在负相关关系，为后续的定量分析（如计算相关系数或进行回归分析）奠定了坚实的基础。