# Python金融分析量化交易：P10：4-指标相关情况分析 📊

![](img/1a3204a3dc20e6e38b88e36e287b855f_1.png)

在本节课中，我们将学习如何分析两个金融指标（如标普500指数和VIX恐慌指数）之间的相关性。我们将通过数据可视化（折线图、散点图）和回归分析来直观地探索它们之间的关系，并理解如何解读这些关系。

![](img/1a3204a3dc20e6e38b88e36e287b855f_3.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_5.png)

## 第一步：数据准备与提取

![](img/1a3204a3dc20e6e38b88e36e287b855f_7.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_9.png)

上一节我们介绍了数据处理的基础，本节中我们来看看如何提取特定的指标数据进行分析。首先，我们需要从数据集中选择两个感兴趣的指标列。

![](img/1a3204a3dc20e6e38b88e36e287b855f_11.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_13.png)

```python
# 假设我们有一个名为data2的DataFrame，包含多个金融指标
# 我们选择‘SPX’（标普500指数）和‘VIX’（恐慌指数）这两列
selected_data = data2[['SPX', 'VIX']]
print(selected_data.head())
```

![](img/1a3204a3dc20e6e38b88e36e287b855f_15.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_17.png)

执行以上代码后，我们便得到了一个仅包含‘SPX’和‘VIX’两列数据的新DataFrame。这是后续所有分析的基础。

![](img/1a3204a3dc20e6e38b88e36e287b855f_19.png)

## 第二步：初步可视化观察

![](img/1a3204a3dc20e6e38b88e36e287b855f_21.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_23.png)

在分析关系之前，最直观的方法是绘制图表。以下是绘制两个指标各自走势图的方法。

![](img/1a3204a3dc20e6e38b88e36e287b855f_25.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_27.png)

```python
import matplotlib.pyplot as plt

![](img/1a3204a3dc20e6e38b88e36e287b855f_29.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_31.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_33.png)

# 创建两个子图，分别展示SPX和VIX的走势
fig, axes = plt.subplots(2, 1, figsize=(10, 8))
selected_data['SPX'].plot(ax=axes[0], title='SPX Index')
selected_data['VIX'].plot(ax=axes[1], title='VIX Index', color='green')
plt.tight_layout()
plt.show()
```

![](img/1a3204a3dc20e6e38b88e36e287b855f_35.png)

通过观察两个独立的图表，我们可以初步感知各自的波动情况，但难以直接比较它们之间的互动关系。

![](img/1a3204a3dc20e6e38b88e36e287b855f_37.png)

## 第三步：双Y轴图表绘制

![](img/1a3204a3dc20e6e38b88e36e287b855f_39.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_41.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_43.png)

为了更清晰地比较两个指标在时间上的联动关系，我们可以将它们绘制在同一张图上，但使用不同的Y轴。

![](img/1a3204a3dc20e6e38b88e36e287b855f_45.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_47.png)

```python
# 在同一张图上绘制SPX和VIX，使用双Y轴
ax1 = selected_data['SPX'].plot(figsize=(12, 6), color='blue', title='SPX vs VIX')
ax2 = ax1.twinx()  # 创建共享X轴的第二个Y轴
selected_data['VIX'].plot(ax=ax2, color='green')
ax1.set_ylabel('SPX')
ax2.set_ylabel('VIX')
plt.show()
```

![](img/1a3204a3dc20e6e38b88e36e287b855f_49.png)

这种方法可以让我们直观地看到，当SPX指数下降时（蓝线），VIX指数往往上升（绿线），暗示着一种负相关关系。为了更清晰地观察，我们可以只选取一小段时间的数据（例如2010年至2012年）进行绘图。

![](img/1a3204a3dc20e6e38b88e36e287b855f_51.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_53.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_55.png)

## 第四步：散点图与分布分析

![](img/1a3204a3dc20e6e38b88e36e287b855f_57.png)

除了时间序列图，散点图是分析两个变量关系的强大工具。它能直接展示一个变量随另一个变量变化的情况。

![](img/1a3204a3dc20e6e38b88e36e287b855f_59.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_61.png)

以下是使用Pandas绘制散点图矩阵的方法，它可以同时展示变量的分布和两两关系。

![](img/1a3204a3dc20e6e38b88e36e287b855f_63.png)

```python
from pandas.plotting import scatter_matrix

# 计算指标的日收益率（或对数收益率），以分析变化率之间的关系
returns = np.log(selected_data / selected_data.shift(1)).dropna()

![](img/1a3204a3dc20e6e38b88e36e287b855f_65.png)

# 绘制散点图矩阵，对角线显示核密度估计图
scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal='kde')
plt.show()
```

![](img/1a3204a3dc20e6e38b88e36e287b855f_67.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_69.png)

在这个矩阵图中：
*   对角线上的图是每个指标自身的**核密度估计（KDE）图**，它展示了该指标收益率的分布情况，比直方图更平滑。
*   非对角线上的图是两两指标的**散点图**。从SPX和VIX的散点图中，我们可以清晰地看到点云呈向下倾斜的趋势，这直观地证实了它们之间的负相关关系。

## 第五步：进行回归分析

![](img/1a3204a3dc20e6e38b88e36e287b855f_71.png)

散点图展示了关系，而回归分析可以量化这种关系。我们可以拟合一条直线（或曲线）来描述一个变量如何随另一个变量变化。

![](img/1a3204a3dc20e6e38b88e36e287b855f_73.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_75.png)

虽然本段教程没有给出具体的回归代码，但其核心思想是建立一个模型。例如，一个简单的线性回归模型可以表示为：

![](img/1a3204a3dc20e6e38b88e36e287b855f_77.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_79.png)

**公式：** `VIX ≈ β₀ + β₁ * SPX + ε`

![](img/1a3204a3dc20e6e38b88e36e287b855f_81.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_83.png)

其中：
*   `β₀` 是截距。
*   `β₁` 是斜率（回归系数），它量化了SPX变化一个单位时，VIX平均变化多少。**负的β₁值证实了负相关关系**。
*   `ε` 是误差项。

![](img/1a3204a3dc20e6e38b88e36e287b855f_85.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_87.png)

在Python中，可以使用`statsmodels`或`scikit-learn`库来方便地计算这些系数并进行统计检验。

![](img/1a3204a3dc20e6e38b88e36e287b855f_89.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_91.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_93.png)

## 总结

![](img/1a3204a3dc20e6e38b88e36e287b855f_95.png)

本节课中我们一起学习了金融指标相关性分析的完整流程：
1.  **数据提取**：从数据集中筛选出目标指标。
2.  **可视化观察**：通过绘制**双Y轴折线图**，在时间维度上直观观察指标的联动趋势。
3.  **关系深入分析**：利用**散点图矩阵**同时观察变量的分布和两两关系，散点图的模式能清晰指示正相关、负相关或无相关。
4.  **量化分析**：通过**回归分析**，用具体的数学公式（如线性回归方程）和系数来量化变量间的关系强度与方向。

![](img/1a3204a3dc20e6e38b88e36e287b855f_97.png)

![](img/1a3204a3dc20e6e38b88e36e287b855f_99.png)

这套“可视化先行，量化验证在后”的方法，是探索和理解金融数据中复杂关系的有效手段。