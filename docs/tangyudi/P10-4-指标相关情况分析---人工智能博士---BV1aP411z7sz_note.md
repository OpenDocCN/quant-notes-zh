# 数据分析入门：P10：4-指标相关情况分析 📊

在本节课中，我们将学习如何分析两个金融指标（标普500指数和恐慌指数）之间的关系。我们将通过数据可视化（折线图、散点图）和回归分析来探索它们之间的相关性，特别是负相关关系。

![](img/0a55de50bc5ac94e851f037e39e3b21e_1.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_3.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_5.png)

---

![](img/0a55de50bc5ac94e851f037e39e3b21e_7.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_9.png)

## 第一步：数据准备与提取

![](img/0a55de50bc5ac94e851f037e39e3b21e_11.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_13.png)

首先，我们需要从数据集中提取出要分析的两个指标列。这里我们选择 `SPX`（标普500指数）和 `VIX`（恐慌指数）。

![](img/0a55de50bc5ac94e851f037e39e3b21e_15.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_17.png)

```python
# 假设我们有一个名为 data2 的 DataFrame
data = data2[['SPX', 'VIX']]
print(data.head())
```

执行以上代码后，我们将得到一个只包含 `SPX` 和 `VIX` 两列数据的新 DataFrame。

![](img/0a55de50bc5ac94e851f037e39e3b21e_19.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_21.png)

---

![](img/0a55de50bc5ac94e851f037e39e3b21e_23.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_25.png)

## 第二步：初步可视化观察

![](img/0a55de50bc5ac94e851f037e39e3b21e_27.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_29.png)

为了直观地观察两个指标随时间的变化趋势，我们首先尝试将它们绘制在同一张图中。

![](img/0a55de50bc5ac94e851f037e39e3b21e_31.png)

```python
data.plot()
```

![](img/0a55de50bc5ac94e851f037e39e3b21e_33.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_35.png)

然而，直接将两个量级和单位不同的指标画在同一坐标系下，趋势并不清晰。因此，一个更好的方法是使用双Y轴图，让每个指标拥有自己的纵坐标轴。

![](img/0a55de50bc5ac94e851f037e39e3b21e_37.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_39.png)

```python
ax = data.plot(secondary_y=['VIX'], figsize=(10, 6))
```

![](img/0a55de50bc5ac94e851f037e39e3b21e_41.png)

通过指定 `secondary_y` 参数，`VIX` 数据将使用右侧的Y轴。这样，两个指标的走势对比就清晰多了。我们可以观察到，当 `SPX` 下降时，`VIX` 往往上升，初步暗示了负相关关系。

![](img/0a55de50bc5ac94e851f037e39e3b21e_43.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_45.png)

为了更清晰地观察短期关系，我们可以只选取一段时间的数据（例如2010年至2012年）进行绘图。

![](img/0a55de50bc5ac94e851f037e39e3b21e_47.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_49.png)

```python
data_subset = data['2010-01-01':'2012-12-31']
ax = data_subset.plot(secondary_y=['VIX'], figsize=(12, 6))
```

![](img/0a55de50bc5ac94e851f037e39e3b21e_51.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_53.png)

---

![](img/0a55de50bc5ac94e851f037e39e3b21e_55.png)

## 第三步：计算变化率并绘制散点图矩阵

![](img/0a55de50bc5ac94e851f037e39e3b21e_57.png)

为了更深入地分析关系，我们计算指标的日变化率。这可以通过当前值除以前一日值（使用 `.shift()` 方法）来实现。

```python
returns = np.log(data / data.shift(1))
print(returns.head())
```

![](img/0a55de50bc5ac94e851f037e39e3b21e_59.png)

接下来，我们使用散点图矩阵来同时观察单个指标的分布以及两个指标两两之间的关系。Pandas 提供了便捷的绘图函数。

![](img/0a55de50bc5ac94e851f037e39e3b21e_61.png)

以下是绘制包含直方图（对角线）的散点图矩阵的代码：

```python
pd.plotting.scatter_matrix(returns,
                           alpha=0.2,        # 设置点的透明度，避免重叠
                           diagonal='hist',   # 对角线绘制直方图
                           hist_kwds={'bins': 50}, # 直方图分为50个区间
                           figsize=(10, 10))
```

![](img/0a55de50bc5ac94e851f037e39e3b21e_63.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_65.png)

执行后，我们会得到一个2x2的矩阵图：
*   **对角线**：分别是 `SPX` 和 `VIX` 变化率的分布直方图。
*   **非对角线**：是 `SPX` 与 `VIX` 变化率之间的散点图，可以直观看到数据点的分布形态。

我们也可以将对角线的图换成更平滑的核密度估计图：

![](img/0a55de50bc5ac94e851f037e39e3b21e_67.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_69.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_71.png)

```python
pd.plotting.scatter_matrix(returns,
                           alpha=0.2,
                           diagonal='kde',   # 对角线绘制核密度估计图
                           figsize=(10, 10))
```

![](img/0a55de50bc5ac94e851f037e39e3b21e_73.png)

核密度估计图通过平滑处理，能更清晰地展示数据的分布轮廓。从散点图中，我们可以更明确地看到 `SPX` 变化率与 `VIX` 变化率呈现的负相关趋势（一个上升，另一个倾向于下降）。

![](img/0a55de50bc5ac94e851f037e39e3b21e_75.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_77.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_79.png)

---

![](img/0a55de50bc5ac94e851f037e39e3b21e_81.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_83.png)

## 总结

![](img/0a55de50bc5ac94e851f037e39e3b21e_85.png)

本节课中我们一起学习了如何分析两个变量间的相关关系：
1.  **数据提取**：首先从数据集中筛选出目标指标。
2.  **趋势观察**：通过绘制双Y轴折线图，直观对比两个指标随时间的变化趋势。
3.  **深入分析**：计算指标的变化率，并利用散点图矩阵同时观察单变量分布和双变量关系。散点图能清晰地揭示变量间是否存在线性相关（如我们观察到的负相关），而对角线上的直方图或核密度图则展示了每个变量自身的分布情况。

![](img/0a55de50bc5ac94e851f037e39e3b21e_87.png)

![](img/0a55de50bc5ac94e851f037e39e3b21e_89.png)

这些可视化方法是探索性数据分析的基础，能帮助我们快速把握数据特征，为后续的定量分析（如计算相关系数、进行回归建模）提供重要依据。