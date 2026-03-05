# Python金融分析与量化交易实战：P7：6-指标相关情况分析 📊

在本节课中，我们将学习如何使用Python进行金融指标的回归分析。我们将通过可视化图表和统计方法来探索两个关键金融指标（标普500指数和VIX恐慌指数）之间的关系，理解它们是如何相互影响的。

![](img/5cb5e54603d898f9ed16548493b4b1c2_1.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_3.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_5.png)

---

![](img/5cb5e54603d898f9ed16548493b4b1c2_7.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_9.png)

## 第一步：数据准备与提取

![](img/5cb5e54603d898f9ed16548493b4b1c2_11.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_13.png)

上一节我们介绍了数据处理的基础，本节中我们来看看如何提取我们感兴趣的特定指标数据进行分析。

![](img/5cb5e54603d898f9ed16548493b4b1c2_15.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_17.png)

首先，我们需要从数据集中提取出要分析的两列数据：标普500指数（SPX）和VIX恐慌指数（VIX）。

以下是提取数据的代码：
```python
data2 = data2[['SPX', 'VIX']]
print(data2.head())
```
执行这段代码后，我们得到了一个只包含`SPX`和`VIX`两列的新DataFrame，便于后续的专门分析。

![](img/5cb5e54603d898f9ed16548493b4b1c2_19.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_21.png)

---

![](img/5cb5e54603d898f9ed16548493b4b1c2_23.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_25.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_27.png)

## 第二步：初步可视化观察

![](img/5cb5e54603d898f9ed16548493b4b1c2_29.png)

提取数据后，最直观的方法是将其可视化。我们先尝试将两个指标分别画在两个子图中进行观察。

![](img/5cb5e54603d898f9ed16548493b4b1c2_31.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_33.png)

以下是绘制两个独立子图的代码：
```python
data2.plot(subplots=True)
```
然而，将两个指标画在独立的坐标系中，很难直接观察它们之间的联动关系。因此，我们需要改进可视化方法。

![](img/5cb5e54603d898f9ed16548493b4b1c2_35.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_37.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_39.png)

---

![](img/5cb5e54603d898f9ed16548493b4b1c2_41.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_43.png)

## 第三步：改进可视化——双Y轴图表

![](img/5cb5e54603d898f9ed16548493b4b1c2_45.png)

为了更清晰地观察两个指标在同一时间范围内的变化趋势，我们可以将它们绘制在同一张图上，但使用左右两个不同的Y轴。

![](img/5cb5e54603d898f9ed16548493b4b1c2_47.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_49.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_51.png)

以下是创建双Y轴图表的代码：
```python
ax = data2['SPX'].plot()
data2['VIX'].plot(secondary_y=True, ax=ax)
```
通过这张图，我们可以初步观察到：当蓝色线（SPX）下降时，绿色线（VIX）往往在上升，暗示两者可能存在负相关关系。为了使趋势更明显，我们可以只选取特定时间段（例如2010年至2012年）的数据进行绘图。

![](img/5cb5e54603d898f9ed16548493b4b1c2_53.png)

---

![](img/5cb5e54603d898f9ed16548493b4b1c2_55.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_57.png)

## 第四步：计算指标变化率

![](img/5cb5e54603d898f9ed16548493b4b1c2_59.png)

为了进行更深入的相关性分析，我们常常需要观察指标的变化率，而非原始值。我们可以计算指标的日对数收益率。

以下是计算日对数收益率的公式和代码：
**公式**：`log_return = log(price_t / price_{t-1})`
```python
import numpy as np
returns = np.log(data2 / data2.shift(1))
print(returns.head())
```
计算出的`returns` DataFrame包含了`SPX`和`VIX`的日收益率，这是进行散点图和回归分析的基础数据。

![](img/5cb5e54603d898f9ed16548493b4b1c2_61.png)

---

![](img/5cb5e54603d898f9ed16548493b4b1c2_63.png)

## 第五步：散点图矩阵分析

接下来，我们使用散点图矩阵来综合展示两个指标各自的数据分布以及两两之间的关系。对角线可以展示每个指标自身的分布情况。

![](img/5cb5e54603d898f9ed16548493b4b1c2_65.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_67.png)

以下是绘制散点图矩阵的代码，对角线使用核密度估计图：
```python
from pandas.plotting import scatter_matrix
scatter_matrix(returns, alpha=0.2, figsize=(10, 10), diagonal='kde')
```
执行代码后，我们会得到一个2x2的矩阵图：
*   **对角线**：展示了`SPX`和`VIX`各自收益率的分布（平滑的核密度估计曲线）。
*   **非对角线**：展示了`SPX`收益率与`VIX`收益率之间的散点图。从点的分布趋势可以更清晰地看出两者的负相关关系。

![](img/5cb5e54603d898f9ed16548493b4b1c2_69.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_71.png)

---

![](img/5cb5e54603d898f9ed16548493b4b1c2_73.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_75.png)

## 第六步：进行回归分析

![](img/5cb5e54603d898f9ed16548493b4b1c2_77.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_79.png)

散点图展示了关系，而回归分析可以量化这种关系。我们可以使用`statsmodels`或`scikit-learn`库来拟合一个简单的线性回归模型，描述`VIX`收益率如何随`SPX`收益率变化。

![](img/5cb5e54603d898f9ed16548493b4b1c2_81.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_83.png)

以下是使用`statsmodels`进行线性回归的示例代码：
```python
import statsmodels.api as sm
# 准备数据：X为SPX收益率，y为VIX收益率
X = returns['SPX'].dropna()
y = returns['VIX'].dropna()
# 添加常数项（截距）
X = sm.add_constant(X)
# 拟合普通最小二乘模型
model = sm.OLS(y, X).fit()
# 打印回归结果摘要
print(model.summary())
```
回归结果摘要会提供**回归系数**、**R-squared**等关键统计量，从而量化两个指标间的线性关系强度和方向。

![](img/5cb5e54603d898f9ed16548493b4b1c2_85.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_87.png)

---

![](img/5cb5e54603d898f9ed16548493b4b1c2_89.png)

![](img/5cb5e54603d898f9ed16548493b4b1c2_91.png)

本节课中我们一起学习了金融指标相关性分析的完整流程。我们从**数据提取**开始，通过**绘制双Y轴折线图**进行初步趋势观察，然后计算**变化率**并绘制**散点图矩阵**来直观展示分布与关系，最后引入**线性回归分析**来量化指标间的统计关系。这套方法能帮助你系统地探索和理解不同金融变量之间的相互作用。