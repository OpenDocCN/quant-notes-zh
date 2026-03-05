# 人工智能数据挖掘实战：P10：指标相关情况分析 📊

![](img/64c9d2b574fb4047caef7bb3d10b0587_1.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_3.png)

在本节课中，我们将学习如何分析两个金融指标（标普500指数和VIX恐慌指数）之间的相关性。我们将通过数据可视化（折线图、散点图）和回归分析来直观地探索它们之间的关系，这对于理解市场动态至关重要。

![](img/64c9d2b574fb4047caef7bb3d10b0587_5.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_7.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_9.png)

## 第一步：数据准备与提取

![](img/64c9d2b574fb4047caef7bb3d10b0587_11.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_13.png)

首先，我们需要从数据集中提取出要分析的两个指标列。这里我们选择 `SPX`（标普500指数）和 `VIX`（恐慌指数）。

![](img/64c9d2b574fb4047caef7bb3d10b0587_15.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_17.png)

```python
# 从data2数据集中提取SPX和VIX两列数据
data = data2[['SPX', 'VIX']]
# 打印数据以确认
print(data.head())
```

![](img/64c9d2b574fb4047caef7bb3d10b0587_19.png)

执行上述代码后，我们便获得了只包含目标两列的数据，为后续分析做好准备。

![](img/64c9d2b574fb4047caef7bb3d10b0587_21.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_23.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_25.png)

## 第二步：绘制双轴折线图

![](img/64c9d2b574fb4047caef7bb3d10b0587_27.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_29.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_31.png)

上一节我们提取了数据，本节中我们来看看如何将两个指标绘制在同一张图中以便观察趋势。由于两个指标的量纲和数值范围不同，直接绘制会难以观察，因此我们使用双Y轴的方法。

![](img/64c9d2b574fb4047caef7bb3d10b0587_33.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_35.png)

以下是绘制双轴折线图的步骤：

![](img/64c9d2b574fb4047caef7bb3d10b0587_37.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_39.png)

1.  使用 `plot` 方法并指定 `secondary_y` 参数，为第二个指标创建右侧Y轴。
2.  为了更清晰地观察短期关系，我们可以选取一段时间窗口（例如2010年至2012年）的数据进行绘制。
3.  通过 `figsize` 参数调整图表大小，使趋势更易读。

![](img/64c9d2b574fb4047caef7bb3d10b0587_41.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_43.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_45.png)

```python
# 选取2010年至2012年的数据
data_subset = data['2010-01-01':'2012-12-31']
# 绘制双轴折线图，VIX使用右侧Y轴
ax = data_subset.plot(secondary_y=['VIX'], figsize=(12, 6))
# 显示图表
plt.show()
```

![](img/64c9d2b574fb4047caef7bb3d10b0587_47.png)

通过观察生成的图表，我们可以初步发现：当蓝色线（SPX指数）下降时，绿色线（VIX指数）往往上升，这表明两者可能存在负相关关系。数据可视化是一个迭代过程，通常从基础图表开始，再根据观察结果进行调整和优化。

![](img/64c9d2b574fb4047caef7bb3d10b0587_49.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_51.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_53.png)

## 第三步：绘制散点图与分布图

![](img/64c9d2b574fb4047caef7bb3d10b0587_55.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_57.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_59.png)

除了折线图，散点图矩阵是观察变量间关系及各自分布的强大工具。在绘制之前，我们通常使用收益率数据（对数收益率）进行分析，这能更好地反映指标的变化情况。

![](img/64c9d2b574fb4047caef7bb3d10b0587_61.png)

以下是计算对数收益率并绘制散点图矩阵的代码：

![](img/64c9d2b574fb4047caef7bb3d10b0587_63.png)

```python
# 计算SPX和VIX的对数收益率
returns = np.log(data / data.shift(1))
# 使用pandas的scatter_matrix方法绘制散点图矩阵
pd.plotting.scatter_matrix(returns,
                           alpha=0.5, # 设置点的透明度，避免重叠
                           diagonal='kde', # 对角线绘制核密度估计图
                           figsize=(10, 10))
plt.show()
```

在这张矩阵图中：
*   **对角线**：展示了每个指标自身的分布情况。`kde`（核密度估计）能生成平滑的分布曲线，比直方图（`hist`）更直观。你可以通过修改 `diagonal` 参数在两者间切换。
*   **非对角线**：展示了两个指标两两之间的散点图。从 `SPX` 与 `VIX` 的散点图中，可以更清晰地看到数据点从左上到右下分布的负相关趋势。

![](img/64c9d2b574fb4047caef7bb3d10b0587_65.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_67.png)

## 第四步：进行回归分析

散点图展示了关系，但为了量化这种关系，我们需要进行回归分析。这可以让我们得到一条拟合的趋势线及其方程。

![](img/64c9d2b574fb4047caef7bb3d10b0587_69.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_71.png)

虽然课程视频中未展示完整的回归代码，但其核心思想是：以 `VIX` 收益率为因变量（Y），`SPX` 收益率为自变量（X），拟合一个线性模型。模型公式可以表示为：

![](img/64c9d2b574fb4047caef7bb3d10b0587_73.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_75.png)

**`VIX_Return = β₀ + β₁ * SPX_Return + ε`**

![](img/64c9d2b574fb4047caef7bb3d10b0587_77.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_79.png)

其中，`β₁` 就是回归系数，它量化了 `SPX` 变动一个单位时，`VIX` 的平均变动量。一个显著的负 `β₁` 值将证实我们观察到的负相关关系。在实际操作中，你可以使用 `statsmodels` 或 `scikit-learn` 库来完成线性回归，并可将得到的回归线叠加到之前的散点图上，使关系一目了然。

![](img/64c9d2b574fb4047caef7bb3d10b0587_81.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_83.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_85.png)

## 总结

![](img/64c9d2b574fb4047caef7bb3d10b0587_87.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_89.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_91.png)

本节课中我们一起学习了金融指标相关性分析的完整流程：
1.  **数据准备**：精准提取目标指标数据。
2.  **可视化探索**：通过**双轴折线图**观察时间序列上的互动趋势，通过**散点图矩阵**（含分布图）全面观察变量间关系与各自分布。
3.  **量化分析**：引入**回归分析**的概念，通过拟合模型来量化指标间的相关程度。

![](img/64c9d2b574fb4047caef7bb3d10b0587_93.png)

![](img/64c9d2b574fb4047caef7bb3d10b0587_95.png)

这些方法是数据分析和金融量化研究的基础，掌握后你可以将其应用于任何需要探究变量关系的场景中。选择哪种图表取决于你的具体目标，核心是让数据所揭示的故事清晰、直观地呈现出来。