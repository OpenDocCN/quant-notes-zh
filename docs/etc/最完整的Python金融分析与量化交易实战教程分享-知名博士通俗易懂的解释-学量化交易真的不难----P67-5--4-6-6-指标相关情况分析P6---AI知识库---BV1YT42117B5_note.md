# 量化交易完全可自学教程：P67：5.4.6.6-指标相关情况分析P6 📈

![](img/fc597be74135cedcde280a4b9b4121be_1.png)

在本节课中，我们将学习如何使用Python进行金融数据的回归分析。回归分析的核心是探索两个或多个变量之间的关系，例如，观察一个指标（如标普500指数）的变动如何影响另一个指标（如VIX恐慌指数）。我们将通过数据可视化（折线图、散点图）和统计方法（核密度估计）来直观地展示并分析这种关系。

![](img/fc597be74135cedcde280a4b9b4121be_3.png)

![](img/fc597be74135cedcde280a4b9b4121be_5.png)

---

![](img/fc597be74135cedcde280a4b9b4121be_7.png)

![](img/fc597be74135cedcde280a4b9b4121be_9.png)

上一节我们介绍了如何计算增长率等指标，本节中我们来看看如何分析两个指标之间的相关性。

![](img/fc597be74135cedcde280a4b9b4121be_11.png)

![](img/fc597be74135cedcde280a4b9b4121be_13.png)

![](img/fc597be74135cedcde280a4b9b4121be_15.png)

回归分析旨在探究变量之间的关系，例如，观察一个变量的走势或增长率是否对另一个变量产生影响。以下是进行此类分析的标准流程。



![](img/fc597be74135cedcde280a4b9b4121be_17.png)

![](img/fc597be74135cedcde280a4b9b4121be_19.png)

## 第一步：数据准备

![](img/fc597be74135cedcde280a4b9b4121be_21.png)

首先，我们需要从数据集中选择两个感兴趣的指标进行分析。这里我们选择标普500指数（`SPX`）和VIX恐慌指数（`VIX`）。

![](img/fc597be74135cedcde280a4b9b4121be_23.png)

![](img/fc597be74135cedcde280a4b9b4121be_25.png)

```python
# 从data2数据集中提取SPX和VIX两列数据
data = data2[['SPX', 'VIX']]
print(data.head())
```

![](img/fc597be74135cedcde280a4b9b4121be_27.png)

![](img/fc597be74135cedcde280a4b9b4121be_29.png)

执行上述代码后，我们便获得了只包含这两列数据的新DataFrame，索引保持不变。



![](img/fc597be74135cedcde280a4b9b4121be_31.png)

![](img/fc597be74135cedcde280a4b9b4121be_33.png)

![](img/fc597be74135cedcde280a4b9b4121be_35.png)

## 第二步：初步可视化观察

![](img/fc597be74135cedcde280a4b9b4121be_37.png)

![](img/fc597be74135cedcde280a4b9b4121be_39.png)

为了直观感受两个指标间的关系，最直接的方法是绘制图表。我们首先尝试将它们画在同一张图上。

![](img/fc597be74135cedcde280a4b9b4121be_41.png)

![](img/fc597be74135cedcde280a4b9b4121be_43.png)

```python
# 尝试在同一坐标系下绘制两条折线
data.plot()
```

![](img/fc597be74135cedcde280a4b9b4121be_45.png)

![](img/fc597be74135cedcde280a4b9b4121be_47.png)

![](img/fc597be74135cedcde280a4b9b4121be_49.png)

然而，将两个量级和单位可能完全不同的指标画在同一Y轴上，趋势往往难以辨认。因此，我们需要改进可视化方法。



![](img/fc597be74135cedcde280a4b9b4121be_51.png)

## 第三步：改进可视化（双Y轴图）

![](img/fc597be74135cedcde280a4b9b4121be_53.png)

为了让两个指标的趋势对比更清晰，我们可以使用双Y轴图，让每个指标拥有独立的Y轴刻度。

![](img/fc597be74135cedcde280a4b9b4121be_55.png)

![](img/fc597be74135cedcde280a4b9b4121be_57.png)

![](img/fc597be74135cedcde280a4b9b4121be_59.png)

```python
# 创建双Y轴图，左侧为SPX，右侧为VIX
ax = data['SPX'].plot(legend=True)
ax2 = ax.twinx()  # 创建共享X轴的新Y轴
data['VIX'].plot(ax=ax2, legend=True, color='r')
ax.figure.legend()
```

![](img/fc597be74135cedcde280a4b9b4121be_61.png)

![](img/fc597be74135cedcde280a4b9b4121be_63.png)

通过观察双Y轴图，我们可以初步发现：当蓝色线（SPX）下降时，红色线（VIX）往往上升，反之亦然，这暗示了二者可能存在负相关关系。

![](img/fc597be74135cedcde280a4b9b4121be_65.png)

为了更清晰地观察短期趋势，我们可以只选取特定时间段的数据（例如2010年至2012年）并绘制更大的图表。

![](img/fc597be74135cedcde280a4b9b4121be_67.png)

```python
# 选取2010-01-01到2012-12-31期间的数据
data_subset = data['2010-01-01':'2012-12-31']
# 绘制双Y轴图，并指定图形尺寸
ax = data_subset['SPX'].plot(figsize=(10,6), legend=True)
ax2 = ax.twinx()
data_subset['VIX'].plot(ax=ax2, legend=True, color='r')
ax.figure.legend()
```

图表绘制通常不是一步到位的，需要根据初步结果不断调整参数（如时间范围、图形尺寸），以使关系呈现得更清晰。



![](img/fc597be74135cedcde280a4b9b4121be_69.png)

![](img/fc597be74135cedcde280a4b9b4121be_71.png)

## 第四步：散点图与分布分析

除了折线图，散点图是观察两个变量关系的强大工具。同时，我们还可以分析每个变量自身的分布。

![](img/fc597be74135cedcde280a4b9b4121be_73.png)

以下是使用Pandas绘制散点图矩阵的方法，它可以同时展示变量间的散点关系以及每个变量的分布（直方图或核密度估计图）。

![](img/fc597be74135cedcde280a4b9b4121be_75.png)

```python
# 计算指标的日收益率（使用对数差分法）
returns = np.log(data / data.shift(1))
returns = returns.dropna()

![](img/fc597be74135cedcde280a4b9b4121be_77.png)

![](img/fc597be74135cedcde280a4b9b4121be_79.png)

# 绘制散点图矩阵，对角线显示核密度估计图
pd.plotting.scatter_matrix(returns,
                           alpha=0.5,        # 设置点的透明度，避免重叠
                           figsize=(10, 10),
                           diagonal='kde',   # 对角线图形类型：'kde'为核密度估计，'hist'为直方图
                           range_padding=0.5)
```

![](img/fc597be74135cedcde280a4b9b4121be_81.png)

![](img/fc597be74135cedcde280a4b9b4121be_83.png)

在这个矩阵图中：
*   **对角线**：展示了单个变量（如`SPX`收益率）的分布。`kde`（核密度估计）通过平滑的曲线来近似数据的概率分布，比直方图更连续。
*   **非对角线**：展示了两个变量之间的散点图。从`SPX`与`VIX`的散点图中，可以更明确地看到数据点从左上到右下分布的负相关趋势。

![](img/fc597be74135cedcde280a4b9b4121be_85.png)

![](img/fc597be74135cedcde280a4b9b4121be_87.png)

![](img/fc597be74135cedcde280a4b9b4121be_89.png)

通过散点图，我们可以更定量、更清晰地确认两个指标间的负相关关系。



![](img/fc597be74135cedcde280a4b9b4121be_91.png)

![](img/fc597be74135cedcde280a4b9b4121be_93.png)

## 总结

![](img/fc597be74135cedcde280a4b9b4121be_95.png)

本节课中我们一起学习了如何分析金融指标间的相关性。
1.  **数据准备**：首先从数据集中提取出需要分析的两个指标列。
2.  **趋势观察**：通过绘制**双Y轴折线图**，可以直观对比两个指标随时间变化的趋势，初步判断其关系（如负相关）。
3.  **关系与分布分析**：利用**散点图矩阵**，可以同时观察两个变量间的散点关系以及各自的数据分布。对角线使用**核密度估计（KDE）** 能提供比直方图更平滑的分布视图。

![](img/fc597be74135cedcde280a4b9b4121be_97.png)

![](img/fc597be74135cedcde280a4b9b4121be_99.png)

![](img/fc597be74135cedcde280a4b9b4121be_101.png)

这些可视化方法是量化分析中探索数据关系的基石，能帮助我们形成直观认识，为后续更严谨的统计建模（如计算相关系数、拟合回归方程）奠定基础。