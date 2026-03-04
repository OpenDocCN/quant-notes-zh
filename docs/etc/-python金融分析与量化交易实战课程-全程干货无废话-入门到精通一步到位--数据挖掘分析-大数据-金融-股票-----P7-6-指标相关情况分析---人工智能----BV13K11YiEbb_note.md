# Python金融分析与量化交易实战课程：P7：6-指标相关情况分析 📈

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_1.png)

在本节课中，我们将要学习如何分析两个金融指标（如标普500指数和VIX恐慌指数）之间的相关性。我们将通过数据可视化（折线图、散点图）和回归分析来直观地探索和理解它们之间的关系。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_3.png)

## 概述

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_5.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_7.png)

回归分析的核心是探究变量之间的关系，例如观察一个变量的走势或增长率是否会影响另一个变量。我们将遵循一个清晰的流程来完成这项分析。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_9.png)

## 第一步：数据准备

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_11.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_13.png)

首先，我们需要从数据集中提取出我们关心的两个指标列。假设我们有一个名为`data2`的DataFrame，其中包含`SPX`（标普500指数）和`VIX`（恐慌指数）的数据。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_15.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_17.png)

以下是提取这两列数据的代码：

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_19.png)

```python
# 从data2中提取SPX和VIX两列数据
data = data2[['SPX', 'VIX']]
# 打印数据以确认
print(data.head())
```

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_21.png)

执行上述代码后，我们便得到了一个只包含`SPX`和`VIX`两列的新DataFrame `data`，为后续分析做好了准备。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_23.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_25.png)

## 第二步：初步可视化观察

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_27.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_29.png)

在上一节我们准备好了数据，本节中我们来看看如何通过画图来直观地观察两个指标的关系。最直接的方法是分别绘制它们的折线图。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_31.png)

以下是绘制两个独立子图的代码：

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_33.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_35.png)

```python
import matplotlib.pyplot as plt
# 创建包含两个子图的画布
fig, axes = plt.subplots(2, 1, figsize=(10, 8))
# 分别绘制SPX和VIX的折线图
data['SPX'].plot(ax=axes[0], title='SPX')
data['VIX'].plot(ax=axes[1], title='VIX', color='green')
plt.tight_layout()
plt.show()
```

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_37.png)

然而，将两个指标分别画在两个图中，很难直接比较它们之间的联动关系。一个更好的方法是将它们绘制在同一张图上，但使用不同的Y轴刻度。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_39.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_41.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_43.png)

以下是使用双Y轴绘制在同一张图上的代码：

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_45.png)

```python
# 创建图形和第一个坐标轴
fig, ax1 = plt.subplots(figsize=(12, 6))
# 在第一个坐标轴上绘制SPX（蓝色）
color = 'tab:blue'
ax1.set_xlabel('Date')
ax1.set_ylabel('SPX', color=color)
ax1.plot(data.index, data['SPX'], color=color)
ax1.tick_params(axis='y', labelcolor=color)
# 创建第二个共享X轴的坐标轴
ax2 = ax1.twinx()
# 在第二个坐标轴上绘制VIX（绿色）
color = 'tab:green'
ax2.set_ylabel('VIX', color=color)
ax2.plot(data.index, data['VIX'], color=color)
ax2.tick_params(axis='y', labelcolor=color)
fig.tight_layout()
plt.show()
```

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_47.png)

为了更清晰地观察短期趋势，我们可以只选取一段时间的数据（例如2010年至2012年）进行绘图。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_49.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_51.png)

以下是选取特定时间段数据的代码：

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_53.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_55.png)

```python
# 选取2010年至2012年的数据
data_subset = data['2010-01-01':'2012-12-31']
# 使用双Y轴绘制子集数据
fig, ax1 = plt.subplots(figsize=(12, 6))
ax1.plot(data_subset.index, data_subset['SPX'], color='tab:blue')
ax1.set_ylabel('SPX', color='tab:blue')
ax2 = ax1.twinx()
ax2.plot(data_subset.index, data_subset['VIX'], color='tab:green')
ax2.set_ylabel('VIX', color='tab:green')
plt.show()
```

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_57.png)

从图中可以观察到，当SPX指数下降时，VIX指数往往上升，这初步表明两者之间存在负相关关系。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_59.png)

## 第三步：散点图与分布分析

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_61.png)

上一节我们通过折线图观察了趋势，本节中我们来看看如何通过散点图矩阵更全面地分析变量关系。散点图矩阵可以同时展示单变量的分布和两变量之间的散点关系。

以下是绘制散点图矩阵的代码，其中对角线显示核密度估计图：

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_63.png)

```python
from pandas.plotting import scatter_matrix
# 计算每日收益率（对数差分），以分析变化率之间的关系
returns = np.log(data / data.shift(1)).dropna()
# 绘制散点图矩阵，对角线显示核密度估计图
scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal='kde', bins=50)
plt.show()
```

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_65.png)

在这个矩阵图中：
*   对角线上的图是每个指标（SPX收益率、VIX收益率）自身的**核密度估计（KDE）图**，它类似于平滑后的直方图，展示了该变量取值的分布情况。
*   非对角线上的图是两个指标之间的**散点图**。从SPX与VIX的散点图中，可以更清晰地看到数据点从左上到右下分布的态势，这直观地印证了它们之间的负相关关系。

## 第四步：进行回归分析

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_67.png)

散点图展示了关系，但为了量化这种关系，我们需要进行回归分析。我们可以直接在散点图上添加一条回归线，使得关系一目了然。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_69.png)

虽然Pandas和Matplotlib没有内置的一键添加回归线功能，但我们可以结合Scikit-learn或Statsmodels库轻松计算并绘制。以下是一个示例流程：

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_71.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_73.png)

1.  计算回归模型参数（斜率、截距）。
2.  根据参数生成回归线的预测值。
3.  在散点图上绘制这条回归线。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_75.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_77.png)

以下是使用线性回归并绘制回归线的示例代码：

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_79.png)

```python
import numpy as np
from sklearn.linear_model import LinearRegression
# 准备数据：X为SPX收益率，y为VIX收益率
X = returns[['SPX']].values  # 需要是二维数组
y = returns['VIX'].values
# 创建并训练线性回归模型
model = LinearRegression()
model.fit(X, y)
# 生成回归线的预测值
line_X = np.linspace(X.min(), X.max(), 100).reshape(-1, 1)
line_y = model.predict(line_X)
# 绘制散点图和回归线
plt.figure(figsize=(8, 6))
plt.scatter(returns['SPX'], returns['VIX'], alpha=0.5)
plt.plot(line_X, line_y, color='red', linewidth=2, label=f‘y = {model.coef_[0]:.4f}x + {model.intercept_:.4f}’)
plt.xlabel(‘SPX Returns’)
plt.ylabel(‘VIX Returns’)
plt.legend()
plt.title(‘SPX vs VIX Returns with Regression Line’)
plt.show()
```

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_81.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_83.png)

这条红色的回归线清晰地概括了两个变量之间的线性趋势，其方程`y = ax + b`中的系数`a`（斜率）量化了相关性的强度和方向（负值代表负相关）。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_85.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_87.png)

## 总结

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_89.png)

本节课中我们一起学习了如何分析金融指标间的相关性。
1.  我们首先**准备数据**，提取出需要分析的特定指标列。
2.  接着，通过**绘制双Y轴折线图**，我们在同一坐标系下观察了两个指标随时间变化的趋势，发现了此消彼长的初步迹象。
3.  然后，我们利用**散点图矩阵**进行了更深入的分析，该图同时展示了每个指标的分布（通过KDE图）和两两指标间的散点关系，使负相关模式更加直观。
4.  最后，我们引入了**回归分析**，通过在散点图上添加回归线，用线性方程`y = ax + b`量化了变量之间的关系，其中斜率`a`明确指出了相关性的方向和强度。

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_91.png)

![](img/7b0dfe8f63d14afbc8ef3b82a6097928_93.png)

整个流程体现了数据分析中从直观观察到量化验证的完整思路，是探索变量间关系的有效方法。