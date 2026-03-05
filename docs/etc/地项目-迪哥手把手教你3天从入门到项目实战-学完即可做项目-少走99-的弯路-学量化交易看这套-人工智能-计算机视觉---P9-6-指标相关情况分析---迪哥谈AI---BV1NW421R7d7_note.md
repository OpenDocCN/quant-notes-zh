# 金融量化分析：P9：6-指标相关情况分析 📈

在本节课中，我们将学习如何分析金融指标之间的相关性。我们将通过数据可视化和回归分析，直观地探索两个关键指标（如标普500指数和VIX恐慌指数）之间的关系，并学习如何解读这些关系。

![](img/647193cc8ba41fed549cbfe8927a52be_1.png)

## 概述

![](img/647193cc8ba41fed549cbfe8927a52be_3.png)

![](img/647193cc8ba41fed549cbfe8927a52be_5.png)

![](img/647193cc8ba41fed549cbfe8927a52be_6.png)

上一节我们介绍了数据处理的基础操作。本节中，我们来看看如何分析变量之间的关系，即观察不同金融指标（如价格走势、增长率）之间是否存在相互影响或关联。

![](img/647193cc8ba41fed549cbfe8927a52be_8.png)

## 第一步：选择并提取数据

![](img/647193cc8ba41fed549cbfe8927a52be_9.png)

首先，我们需要从数据集中选择两个感兴趣的指标进行分析。以下是具体步骤：

1.  从名为 `data2` 的数据集中，提取“SPX”（标普500指数）和“VIX”（恐慌指数）这两列数据。
2.  确保列名与原始数据中的大写格式一致。

![](img/647193cc8ba41fed549cbfe8927a52be_11.png)

![](img/647193cc8ba41fed549cbfe8927a52be_12.png)

以下是提取数据的代码：

![](img/647193cc8ba41fed549cbfe8927a52be_13.png)

![](img/647193cc8ba41fed549cbfe8927a52be_14.png)

```python
# 选择 SPX 和 VIX 两列数据
data = data2[['SPX', 'VIX']]
# 打印数据前几行进行确认
print(data.head())
```

![](img/647193cc8ba41fed549cbfe8927a52be_16.png)

![](img/647193cc8ba41fed549cbfe8927a52be_17.png)

## 第二步：初步可视化分析

数据准备完成后，最直接的方法是将其可视化。我们可以通过绘图来初步观察两个指标的变化趋势。

![](img/647193cc8ba41fed549cbfe8927a52be_19.png)

![](img/647193cc8ba41fed549cbfe8927a52be_20.png)

以下是绘制两个指标折线图的代码：

![](img/647193cc8ba41fed549cbfe8927a52be_21.png)

![](img/647193cc8ba41fed549cbfe8927a52be_22.png)

![](img/647193cc8ba41fed549cbfe8927a52be_23.png)

```python
# 分别绘制两个指标的折线图
data.plot(subplots=True, figsize=(10, 6))
```

![](img/647193cc8ba41fed549cbfe8927a52be_25.png)

执行后，我们会得到两个独立的子图。然而，将两个序列放在不同的图中，很难直接比较它们之间的联动关系。

![](img/647193cc8ba41fed549cbfe8927a52be_27.png)

![](img/647193cc8ba41fed549cbfe8927a52be_28.png)

## 第三步：优化可视化（双Y轴图）

为了更清晰地观察两个指标在同一时间范围内的变化关系，我们可以将它们绘制在同一张图上，但使用不同的Y轴。

![](img/647193cc8ba41fed549cbfe8927a52be_30.png)

![](img/647193cc8ba41fed549cbfe8927a52be_31.png)

以下是创建双Y轴图的代码：

![](img/647193cc8ba41fed549cbfe8927a52be_32.png)

![](img/647193cc8ba41fed549cbfe8927a52be_33.png)

```python
# 在同一坐标系下，使用左右两个Y轴绘制SPX和VIX
ax = data['SPX'].plot(figsize=(10, 6), style='b-')
ax2 = ax.twinx() # 创建共享X轴的第二个Y轴
data['VIX'].plot(ax=ax2, style='r-')
```

![](img/647193cc8ba41fed549cbfe8927a52be_35.png)

![](img/647193cc8ba41fed549cbfe8927a52be_36.png)

![](img/647193cc8ba41fed549cbfe8927a52be_37.png)

为了更清晰地观察短期内的关系，我们可以只选取一段时间的数据（例如2010年至2012年）进行绘图。

![](img/647193cc8ba41fed549cbfe8927a52be_38.png)

```python
# 选取特定时间范围的数据
data_subset = data['2010-01-01':'2012-12-31']
# 绘制双Y轴图
ax = data_subset['SPX'].plot(figsize=(12, 6), style='b-')
ax2 = ax.twinx()
data_subset['VIX'].plot(ax=ax2, style='g-')
```

![](img/647193cc8ba41fed549cbfe8927a52be_40.png)

通过观察优化后的图表，我们可以发现一个初步规律：当标普500指数（蓝色线）下跌时，恐慌指数VIX（绿色线）往往上升，反之亦然。这表明两者可能存在负相关关系。

![](img/647193cc8ba41fed549cbfe8927a52be_42.png)

## 第四步：散点图与分布分析

![](img/647193cc8ba41fed549cbfe8927a52be_44.png)

除了折线图，散点图是观察两个变量关系的强大工具。在绘制散点图前，我们通常使用收益率（对数收益率）而非原始价格，以消除价格尺度的影响并关注变化率。

以下是计算对数收益率的代码：

```python
# 计算SPX和VIX的对数收益率
returns = np.log(data / data.shift(1))
print(returns.head())
```

![](img/647193cc8ba41fed549cbfe8927a52be_46.png)

![](img/647193cc8ba41fed549cbfe8927a52be_47.png)

Pandas提供了强大的绘图功能，可以一次性展示变量自身的分布以及两两之间的关系。

以下是绘制散点图矩阵的代码，其中对角线显示变量分布：

![](img/647193cc8ba41fed549cbfe8927a52be_49.png)

![](img/647193cc8ba41fed549cbfe8927a52be_50.png)

```python
# 方法一：对角线显示直方图
pd.plotting.scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal='hist', hist_kwds={'bins': 50})
```

```python
# 方法二：对角线显示核密度估计图（更平滑的分布曲线）
pd.plotting.scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal='kde')
```

![](img/647193cc8ba41fed549cbfe8927a52be_52.png)

![](img/647193cc8ba41fed549cbfe8927a52be_54.png)

通过观察散点图矩阵：
*   **对角线**：展示了每个指标自身的分布情况（直方图或核密度估计）。
*   **非对角线**：展示了两个指标之间的散点图。从 `SPX` 和 `VIX` 的散点图中，可以更直观地看到数据点呈现从左上到右下的趋势，这进一步证实了它们之间的负相关关系。

![](img/647193cc8ba41fed549cbfe8927a52be_55.png)

## 第五步：量化相关关系（回归分析）

![](img/647193cc8ba41fed549cbfe8927a52be_57.png)

可视化给出了直观感受，但我们还需要量化这种关系。接下来，我们将通过构建回归方程来精确描述 `SPX` 收益率与 `VIX` 收益率之间的关系。

回归分析可以帮助我们得到如 `VIX_Return = a + b * SPX_Return` 这样的公式，其中系数 `b` 可以量化 `SPX` 变动一个单位时，`VIX` 平均变动多少，从而将两者的关系具体化。

![](img/647193cc8ba41fed549cbfe8927a52be_59.png)

![](img/647193cc8ba41fed549cbfe8927a52be_60.png)

![](img/647193cc8ba41fed549cbfe8927a52be_61.png)

![](img/647193cc8ba41fed549cbfe8927a52be_62.png)

## 总结

![](img/647193cc8ba41fed549cbfe8927a52be_63.png)

![](img/647193cc8ba41fed549cbfe8927a52be_64.png)

本节课中我们一起学习了金融指标相关性分析的基本流程：
1.  **数据选取**：明确分析目标，提取相关指标列。
2.  **初步可视化**：通过折线图观察各自趋势。
3.  **关系可视化**：利用双Y轴图、散点图矩阵等工具，直观展示指标间的联动与分布。
4.  **关系量化**（引子）：认识到需要通过回归分析等统计方法，来量化变量间的具体关系（如下一节课将深入讲解的回归方程）。

![](img/647193cc8ba41fed549cbfe8927a52be_66.png)

![](img/647193cc8ba41fed549cbfe8927a52be_67.png)

通过本课的学习，你掌握了探索金融数据内在关联性的核心可视化方法，这是进行深入量化建模和分析的重要基础。