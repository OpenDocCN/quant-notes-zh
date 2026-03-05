# Python金融量化与股票交易：P9：10.10.4-指标相关性分析 📊

在本节课中，我们将学习如何分析金融指标之间的相关性。具体来说，我们将通过数据可视化和回归分析，来探究两个关键市场指标（如标普500指数和VIX恐慌指数）之间的相互影响关系。

![](img/5b2d89f4cd882d8af2bb3194852bafaf_1.png)

---

![](img/5b2d89f4cd882d8af2bb3194852bafaf_3.png)

上一节我们介绍了数据处理与增长率计算，本节中我们来看看如何分析指标间的相关性。

![](img/5b2d89f4cd882d8af2bb3194852bafaf_5.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_6.png)

## 第一步：数据准备与提取

首先，我们需要从数据集中提取出我们想要分析的两个指标列。假设我们的数据集名为 `data2`，我们选择 `SPX`（标普500指数）和 `VIX`（恐慌指数）这两列进行分析。

![](img/5b2d89f4cd882d8af2bb3194852bafaf_8.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_9.png)

以下是提取数据的代码：

```python
# 从data2数据集中提取‘SPX’和‘VIX’两列数据
data = data2[['SPX', 'VIX']]
# 打印数据前几行以确认
print(data.head())
```

执行上述代码后，我们便得到了一个只包含 `SPX` 和 `VIX` 两列的新DataFrame `data`，索引是日期。

![](img/5b2d89f4cd882d8af2bb3194852bafaf_11.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_12.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_13.png)

---

![](img/5b2d89f4cd882d8af2bb3194852bafaf_14.png)

## 第二步：可视化初步探索

![](img/5b2d89f4cd882d8af2bb3194852bafaf_16.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_17.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_18.png)

数据准备好后，最直观的分析方法就是将它们可视化。我们可以先尝试将两个指标的走势画在同一张图上进行观察。

以下是绘制双轴折线图的代码：

```python
import matplotlib.pyplot as plt

![](img/5b2d89f4cd882d8af2bb3194852bafaf_20.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_21.png)

# 创建图形和主坐标轴
fig, ax1 = plt.subplots(figsize=(12, 6))

![](img/5b2d89f4cd882d8af2bb3194852bafaf_22.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_23.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_24.png)

# 在主坐标轴上绘制SPX（蓝色）
color = ‘tab:blue’
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘SPX’, color=color)
ax1.plot(data.index, data[‘SPX’], color=color)
ax1.tick_params(axis=‘y’, labelcolor=color)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_26.png)

# 创建副坐标轴，用于绘制VIX（绿色）
ax2 = ax1.twinx()
color = ‘tab:green’
ax2.set_ylabel(‘VIX’, color=color)
ax2.plot(data.index, data[‘VIX’], color=color)
ax2.tick_params(axis=‘y’, labelcolor=color)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_28.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_30.png)

fig.tight_layout()
plt.title(‘SPX vs VIX (Dual Y-Axis)’)
plt.show()
```

**注意**：在初次绘图后，如果发现数据时间跨度太长导致趋势不清晰，我们可以通过切片选择特定时间段（例如2010年至2012年）的数据进行绘图，这样能更清楚地观察短期内的关系。

```python
# 选择特定时间段的数据
data_subset = data[‘2010-01-01’:‘2012-12-31’]
# 使用data_subset重复上述绘图步骤
```

![](img/5b2d89f4cd882d8af2bb3194852bafaf_32.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_33.png)

通过观察双轴图，我们可以初步发现：当 `SPX`（蓝线）下降时，`VIX`（绿线）往往上升，反之亦然，这暗示了二者可能存在负相关关系。

![](img/5b2d89f4cd882d8af2bb3194852bafaf_34.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_35.png)

---

![](img/5b2d89f4cd882d8af2bb3194852bafaf_37.png)

## 第三步：散点图与分布图分析

![](img/5b2d89f4cd882d8af2bb3194852bafaf_38.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_39.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_40.png)

除了折线图，散点图是观察两个变量关系的更直接工具。同时，我们还可以结合分布图来查看每个指标自身的分布情况。

Pandas的 `plotting.scatter_matrix` 函数可以方便地绘制出指标两两之间的散点图矩阵，以及对角线上的分布图。

![](img/5b2d89f4cd882d8af2bb3194852bafaf_42.png)

以下是绘制散点图矩阵的代码：

![](img/5b2d89f4cd882d8af2bb3194852bafaf_44.png)

```python
from pandas.plotting import scatter_matrix

![](img/5b2d89f4cd882d8af2bb3194852bafaf_46.png)

# 计算指标的日对数收益率，以观察变化率之间的关系
returns = np.log(data / data.shift(1)).dropna()

# 绘制散点图矩阵，对角线显示核密度估计图
scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal=‘kde’)
plt.suptitle(‘Scatter Matrix of SPX and VIX Returns’)
plt.show()
```

**代码参数解释**：
*   `alpha=0.5`：设置散点的透明度，有助于在点密集时看清分布。
*   `diagonal=‘kde’`：在对角线位置绘制**核密度估计图**，这是一种平滑的分布曲线。你也可以将其改为 `‘hist’` 来绘制直方图。

![](img/5b2d89f4cd882d8af2bb3194852bafaf_48.png)

通过散点图矩阵，我们可以更清晰地看到 `SPX` 收益率与 `VIX` 收益率呈现的负相关趋势（散点图呈左下到右上的分布）。对角线上的核密度图则展示了每个收益率序列自身的分布形态。

![](img/5b2d89f4cd882d8af2bb3194852bafaf_49.png)

---

## 第四步：进行回归分析

![](img/5b2d89f4cd882d8af2bb3194852bafaf_51.png)

散点图展示了关系，但我们还需要量化这种关系。接下来，我们通过线性回归来拟合一个方程，并计算相关系数。

![](img/5b2d89f4cd882d8af2bb3194852bafaf_52.png)

我们可以使用 `scipy.stats` 库中的 `linregress` 函数进行线性回归分析。

以下是进行线性回归并绘制回归线的代码：

![](img/5b2d89f4cd882d8af2bb3194852bafaf_54.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_56.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_57.png)

```python
from scipy import stats

![](img/5b2d89f4cd882d8af2bb3194852bafaf_59.png)

# 提取X（SPX收益率）和Y（VIX收益率）
x = returns[‘SPX’]
y = returns[‘VIX’]

# 执行线性回归
slope, intercept, r_value, p_value, std_err = stats.linregress(x, y)

# 打印回归结果
print(f“回归方程: y = {slope:.4f} * x + {intercept:.4f}”)
print(f“相关系数 R: {r_value:.4f}”)
print(f“R-squared: {r_value**2:.4f}”)
print(f“P-value: {p_value:.4f}”)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_61.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_62.png)

# 绘制散点图及回归线
plt.figure(figsize=(8, 6))
plt.scatter(x, y, alpha=0.5, label=‘Data Points’)
plt.plot(x, slope * x + intercept, color=‘red’, label=f‘Regression Line (R={r_value:.3f})’)
plt.xlabel(‘SPX Daily Log Return’)
plt.ylabel(‘VIX Daily Log Return’)
plt.title(‘Linear Regression: VIX vs SPX Returns’)
plt.legend()
plt.grid(True)
plt.show()
```

![](img/5b2d89f4cd882d8af2bb3194852bafaf_63.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_64.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_65.png)

**结果解读**：
*   **斜率 (`slope`)**：为负值，证实了负相关关系。其绝对值大小代表了 `VIX` 对 `SPX` 变化的敏感程度。
*   **相关系数 (`r_value`)**：介于-1到1之间。越接近-1，负相关性越强。
*   **R平方 (`r_value**2`)**：表示 `VIX` 的波动有多少可以由 `SPX` 的波动来解释。
*   **P值 (`p_value`)**：用于判断相关性是否显著。通常P值小于0.05时，我们认为相关性是显著的。

![](img/5b2d89f4cd882d8af2bb3194852bafaf_66.png)

---

![](img/5b2d89f4cd882d8af2bb3194852bafaf_68.png)

![](img/5b2d89f4cd882d8af2bb3194852bafaf_69.png)

本节课中我们一起学习了金融指标相关性分析的完整流程。我们从**数据提取**开始，通过**双轴折线图**进行初步趋势观察，然后利用**散点图矩阵**直观展示变量间的分布与关系，最后通过**线性回归分析**量化了指标间的负相关程度，并得到了具体的回归方程和统计指标。这套方法是探索和理解市场变量相互作用的基础工具。