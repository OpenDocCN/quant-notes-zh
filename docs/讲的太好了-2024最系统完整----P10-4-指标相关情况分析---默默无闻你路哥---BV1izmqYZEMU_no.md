# Python金融分析与量化交易实战：P10：4-指标相关情况分析 📊

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_1.png)

在本节课中，我们将学习如何分析两个金融指标（如标普500指数和VIX恐慌指数）之间的相关关系。我们将通过数据可视化（折线图、散点图）和回归分析来直观地探索和量化它们之间的相互影响。

## 概述

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_3.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_5.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_7.png)

回归分析的核心是探究变量之间的关系。本节我们将选取两个具体的金融指标，通过一系列步骤来观察它们走势之间的相互影响。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_9.png)

上一节我们介绍了数据处理的基础，本节中我们来看看如何对指标进行相关性分析。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_11.png)

## 第一步：选择并提取数据

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_13.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_15.png)

首先，我们需要从数据集中选择两个感兴趣的指标进行分析。这里我们选择标普500指数（SPX）和VIX恐慌指数（VX）。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_17.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_19.png)

以下是提取这两列数据的代码：

```python
# 假设我们已经有一个名为data2的DataFrame
data = data2[['SPX', 'VX']]
print(data.head())
```

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_21.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_23.png)

执行上述代码后，我们将得到一个只包含`SPX`和`VX`两列数据的新DataFrame，便于后续分析。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_25.png)

## 第二步：初步可视化观察

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_27.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_29.png)

数据提取完成后，最直接的方法是将其可视化，以初步观察趋势。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_31.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_33.png)

最简单的方法是使用Pandas内置的绘图功能，将两个指标分别画在两个子图中。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_35.png)

```python
data.plot(subplots=True)
```

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_37.png)

然而，将两个指标分开绘制在子图中，难以直接比较它们之间的联动关系。一个更好的方法是将它们绘制在同一张图中，但使用不同的Y轴刻度。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_39.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_41.png)

以下是实现双Y轴绘图的代码：

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_43.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_45.png)

```python
ax = data['SPX'].plot(figsize=(10, 6), legend=True, color='blue')
ax2 = ax.twinx()
data['VX'].plot(ax=ax2, legend=True, color='green')
```

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_47.png)

为了更清晰地观察短期内的关系，我们可以只选取一段时间的数据（例如2010年至2012年）进行绘图。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_49.png)

```python
# 选取特定时间范围的数据
data_subset = data['2010-01-01':'2012-12-31']
ax = data_subset['SPX'].plot(figsize=(12, 8), legend=True, color='blue')
ax2 = ax.twinx()
data_subset['VX'].plot(ax=ax2, legend=True, color='green')
```

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_51.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_53.png)

通过观察双Y轴折线图，我们可以初步发现：当蓝色线（SPX指数）下降时，绿色线（VIX指数）往往上升，这表明两者可能存在负相关关系。可视化是一个迭代过程，通常从基本图形开始，然后不断调整参数（如时间范围、图形大小）以使关系更清晰。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_55.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_57.png)

## 第三步：散点图与分布分析

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_59.png)

除了折线图，散点图是观察两个变量关系的另一种有效工具。在画散点图之前，我们通常先计算指标的日收益率（或对数收益率），以分析变化率之间的关系。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_61.png)

以下是计算对数收益率的代码：

```python
import numpy as np
returns = np.log(data / data.shift(1))
print(returns.head())
```

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_63.png)

获得收益率数据后，我们可以使用Pandas的散点图矩阵功能，一次性查看所有变量两两之间的关系以及每个变量自身的分布。

以下是绘制散点图矩阵的代码，其中对角线显示核密度估计图：

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_65.png)

```python
pd.plotting.scatter_matrix(returns,
                           alpha=0.2,        # 设置点的透明度
                           diagonal='kde',   # 对角线显示核密度估计图
                           figsize=(10, 10))
```

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_67.png)

在这个矩阵图中：
*   **对角线**：展示了每个指标（如`SPX`收益率）自身的分布情况，核密度估计图比直方图能提供更平滑的分布曲线。
*   **非对角线**：展示了两个指标之间的散点图。从`SPX`和`VX`的散点图中，我们可以更直观地看到数据点呈现从左上到右下的趋势，这再次印证了它们之间的负相关关系。

通过散点图，我们可以比折线图更清晰地识别出变量间的整体相关性模式。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_69.png)

## 第四步：进行回归分析

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_71.png)

散点图展示了关系，而回归分析可以量化这种关系。我们可以在散点图的基础上，添加一条回归线，使得关系一目了然。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_73.png)

虽然Pandas的绘图功能强大，但添加回归线通常需要结合`seaborn`或`statsmodels`等库。这里我们介绍使用`seaborn`快速绘制带回归线的散点图。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_75.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_77.png)

首先确保安装了seaborn库（`pip install seaborn`），然后使用以下代码：

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_79.png)

```python
import seaborn as sns
import matplotlib.pyplot as plt

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_81.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_83.png)

sns.regplot(x=returns['SPX'], y=returns['VX'])
plt.title('SPX Returns vs VIX Returns with Regression Line')
plt.show()
```

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_85.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_87.png)

这条回归线的斜率和截距就构成了描述两者关系的线性方程。斜率（通常为负值）量化了`SPX`变动一个单位时，`VX`平均会反向变动多少，从而将我们直观观察到的“此消彼长”关系用数学公式表达出来。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_89.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_91.png)

## 总结

本节课中我们一起学习了金融指标相关性分析的完整流程：
1.  **数据选取**：从数据集中提取需要分析的目标指标列。
2.  **可视化探索**：
    *   使用**双Y轴折线图**在同一坐标系中观察两个指标随时间变化的趋势与反向关系。
    *   使用**散点图矩阵**观察指标（特别是其收益率）两两之间的分布与相关模式，对角线可用核密度估计图展示自身分布。
3.  **量化分析**：通过在散点图上添加**回归线**，将直观的相关性转化为可量化的线性模型（如 `y = ax + b`），从而更精确地描述变量间的影响程度。

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_93.png)

![](img/feaf23d4db62cb6c80a80ca14d9d4c26_95.png)

通过结合图形观察与量化分析，我们能够更深入、更准确地理解金融市场中不同指标间的动态关系，这是进行进一步量化策略研究的基础。