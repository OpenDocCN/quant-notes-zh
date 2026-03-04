# Python金融分析与量化交易实战：P7：7.6.6-指标相关性分析 📈

![](img/d1795a192f2c89a993c18ac1eb69ba22_1.png)

在本节课中，我们将学习如何分析两个金融指标（如标普500指数和VIX恐慌指数）之间的相关性。我们将通过数据可视化（折线图、散点图）和回归分析来直观地理解它们之间的关系。

---

![](img/d1795a192f2c89a993c18ac1eb69ba22_3.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_5.png)

## 第一步：数据准备与提取

![](img/d1795a192f2c89a993c18ac1eb69ba22_7.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_9.png)

首先，我们需要从数据集中提取出我们想要分析的两个指标列。这里我们选择`SPX`（标普500指数）和`VIX`（恐慌指数）。

![](img/d1795a192f2c89a993c18ac1eb69ba22_11.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_13.png)

```python
# 假设我们有一个名为data2的DataFrame
data = data2[['SPX', 'VIX']]
print(data.head())
```

![](img/d1795a192f2c89a993c18ac1eb69ba22_15.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_17.png)

执行以上代码后，我们将得到一个只包含`SPX`和`VIX`两列数据的新DataFrame，并打印前几行以确认数据提取成功。

![](img/d1795a192f2c89a993c18ac1eb69ba22_19.png)

---

![](img/d1795a192f2c89a993c18ac1eb69ba22_21.png)

## 第二步：初步可视化观察

上一节我们提取了数据，本节中我们来看看如何通过画图来初步观察两个指标的关系。最直接的方法是分别绘制它们的折线图。

![](img/d1795a192f2c89a993c18ac1eb69ba22_23.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_25.png)

```python
data.plot(subplots=True, figsize=(10, 6))
```

![](img/d1795a192f2c89a993c18ac1eb69ba22_27.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_29.png)

然而，将两个指标画在两个独立的子图中，很难直接比较它们的趋势变化。一个更好的方法是将它们绘制在同一张图中，但使用不同的Y轴。

![](img/d1795a192f2c89a993c18ac1eb69ba22_31.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_33.png)

```python
ax = data['SPX'].plot(figsize=(10, 6))
ax2 = ax.twinx()
data['VIX'].plot(ax=ax2, color='green')
```

为了更清晰地观察短期趋势，我们可以只选取一段时间的数据（例如2010年至2012年）进行绘图。

![](img/d1795a192f2c89a993c18ac1eb69ba22_35.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_37.png)

```python
data_subset = data['2010-01-01':'2012-12-31']
ax = data_subset['SPX'].plot(figsize=(12, 6))
ax2 = ax.twinx()
data_subset['VIX'].plot(ax=ax2, color='green')
```

![](img/d1795a192f2c89a993c18ac1eb69ba22_39.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_41.png)

通过观察图形，我们可以初步发现：当`SPX`指数下降时，`VIX`指数往往上升，这表明两者可能存在负相关关系。

![](img/d1795a192f2c89a993c18ac1eb69ba22_43.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_45.png)

---

![](img/d1795a192f2c89a993c18ac1eb69ba22_47.png)

## 第三步：散点图与分布分析

除了折线图，散点图是观察两个变量关系的另一种有效工具。我们可以使用Pandas的`scatter_matrix`函数来绘制包含散点图和对角线分布图的矩阵。

![](img/d1795a192f2c89a993c18ac1eb69ba22_49.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_51.png)

以下是绘制散点矩阵图的步骤：

![](img/d1795a192f2c89a993c18ac1eb69ba22_53.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_55.png)

1.  首先，我们计算指标的日收益率（或对数收益率），这能更好地反映变化关系。
2.  然后，使用`scatter_matrix`函数进行绘图。

![](img/d1795a192f2c89a993c18ac1eb69ba22_57.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_59.png)

```python
import pandas as pd
import numpy as np

![](img/d1795a192f2c89a993c18ac1eb69ba22_61.png)

# 计算对数收益率
returns = np.log(data / data.shift(1))
returns = returns.dropna()

# 绘制散点矩阵图，对角线显示直方图
pd.plotting.scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal='hist', hist_kwds={'bins': 50})
```

![](img/d1795a192f2c89a993c18ac1eb69ba22_63.png)

我们也可以将对角线的图从直方图改为核密度估计图，它会呈现更平滑的分布曲线。

```python
# 绘制散点矩阵图，对角线显示核密度估计图
pd.plotting.scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal='kde')
```

从生成的散点图中，我们可以更直观地看到`SPX`收益率与`VIX`收益率呈负相关（点云从左上方延伸至右下方）。对角线上的图则分别展示了每个指标自身的分布情况。

![](img/d1795a192f2c89a993c18ac1eb69ba22_65.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_67.png)

---

## 第四步：进行回归分析

散点图展示了关系，但我们还需要量化这种关系。接下来，我们进行简单的线性回归分析，以得到一个描述两者关系的公式。

![](img/d1795a192f2c89a993c18ac1eb69ba22_69.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_71.png)

我们可以使用`statsmodels`或`scikit-learn`库。这里使用`statsmodels`进行演示：

```python
import statsmodels.api as sm

![](img/d1795a192f2c89a993c18ac1eb69ba22_73.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_75.png)

# 准备数据：X为SPX收益率，y为VIX收益率
X = returns['SPX']
y = returns['VIX']

![](img/d1795a192f2c89a993c18ac1eb69ba22_77.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_79.png)

# 为X添加常数项（截距）
X = sm.add_constant(X)

![](img/d1795a192f2c89a993c18ac1eb69ba22_81.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_83.png)

# 建立普通最小二乘（OLS）回归模型
model = sm.OLS(y, X).fit()

![](img/d1795a192f2c89a993c18ac1eb69ba22_85.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_87.png)

# 打印回归结果摘要
print(model.summary())
```

![](img/d1795a192f2c89a993c18ac1eb69ba22_89.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_91.png)

回归结果摘要会提供大量信息，其中我们需要关注：
*   **回归系数**：`const`（截距）和`SPX`的系数。`SPX`的系数为负值，证实了负相关关系。
*   **R-squared**：表示模型对数据变动的解释程度。
*   **P-value**：检验系数是否显著。通常P值小于0.05认为显著。

![](img/d1795a192f2c89a993c18ac1eb69ba22_93.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_95.png)

通过回归分析，我们得到了量化的关系式，例如：`VIX收益率 = 截距 + (系数) * SPX收益率`。这比单纯的图形观察更为精确。

---

![](img/d1795a192f2c89a993c18ac1eb69ba22_97.png)

![](img/d1795a192f2c89a993c18ac1eb69ba22_99.png)

本节课中我们一起学习了金融指标相关性分析的完整流程。我们从**数据提取**开始，通过**绘制双Y轴折线图**进行初步趋势观察，然后利用**散点矩阵图**深入分析变量间的分布与关系，最后通过**线性回归分析**量化了`SPX`与`VIX`之间的负相关关系。这套方法可以帮助你系统地探索和理解任意两个金融变量之间的联系。