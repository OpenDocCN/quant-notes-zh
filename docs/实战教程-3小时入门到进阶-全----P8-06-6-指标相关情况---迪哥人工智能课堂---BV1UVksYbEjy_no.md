# Python金融分析与量化交易实战：P8：06-6-指标相关情况分析 📈

在本节课中，我们将学习如何分析两个金融指标（如标普500指数和VIX恐慌指数）之间的相关性。我们将通过数据可视化（折线图、散点图）和回归分析来直观地展示和理解它们之间的关系。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_1.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_3.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_5.png)

## 第一步：数据准备与初步观察

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_7.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_9.png)

上一节我们介绍了数据清洗与处理，本节中我们来看看如何选取并观察两个特定指标。首先，我们需要从数据集中提取我们关心的两列数据。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_11.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_13.png)

以下是提取数据的代码：
```python
# 假设我们有一个名为data2的DataFrame，包含‘SPX’和‘VIX’两列
data = data2[['SPX', 'VIX']]
print(data.head())
```
执行这段代码后，我们便得到了一个只包含‘SPX’（标普500指数）和‘VIX’（恐慌指数）的新DataFrame `data`。我们只关注这两个指标之间的关系。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_15.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_17.png)

## 第二步：绘制双轴折线图

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_19.png)

直接观察原始数据可能不够直观。最简单的方法是绘制图表。我们可以将两个指标画在同一张图上，但由于它们的数值范围不同，使用双Y轴会更清晰。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_21.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_23.png)

以下是绘制双轴折线图的步骤：
1.  使用Pandas的`plot`方法，并指定`secondary_y`参数来创建第二个Y轴。
2.  为了更清晰地观察短期趋势，我们可以选取一段时间范围（例如2010年至2012年）的数据进行绘制。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_25.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_27.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_29.png)

以下是实现代码：
```python
import matplotlib.pyplot as plt

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_31.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_33.png)

# 选取2010年至2012年的数据
data_subset = data['2010-01-01':'2012-12-31']

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_35.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_37.png)

# 绘制双轴折线图
ax = data_subset['SPX'].plot(figsize=(10,6), legend=True, color='blue', label='SPX')
ax2 = data_subset['VIX'].plot(secondary_y=True, ax=ax, legend=True, color='green', label='VIX')

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_39.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_41.png)

# 设置图例和标签
ax.set_ylabel('SPX')
ax2.set_ylabel('VIX')
plt.title('SPX vs VIX (2010-2012)')
plt.show()
```
通过观察生成的图表，我们可以发现一个初步规律：当蓝色线（SPX指数）下降时，绿色线（VIX恐慌指数）往往上升，反之亦然。这表明两者可能存在负相关关系。图表绘制通常不是一步到位的，需要根据初步结果进行调整，以获得更清晰的展示效果。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_43.png)

## 第三步：绘制散点图与分布图

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_45.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_47.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_49.png)

除了折线图，散点图矩阵是观察多个变量间两两关系的强大工具。它能同时展示每个变量的分布以及任意两个变量之间的相关性。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_51.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_53.png)

以下是使用Pandas绘制散点图矩阵的方法：
1.  使用`pandas.plotting.scatter_matrix`函数。
2.  可以指定对角线上的图形是直方图（`hist`）还是核密度估计图（`kde`），用于展示单个变量的分布。
3.  通过`alpha`参数设置散点透明度，避免数据点过度重叠。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_55.png)

以下是绘制散点图矩阵的代码：
```python
from pandas.plotting import scatter_matrix

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_57.png)

# 计算每日收益率（或变化率），使数据更平稳，便于观察关系
returns = np.log(data / data.shift(1))
returns = returns.dropna()

# 绘制散点图矩阵，对角线使用核密度估计图
scatter_matrix(returns, alpha=0.5, figsize=(10, 10), diagonal='kde')
plt.show()
```
生成的图表是一个2x2的矩阵。对角线上的图是每个指标自身的核密度估计，展示了其分布形态。非对角线上的散点图则清晰地展示了`SPX`收益率与`VIX`收益率之间的关系。从散点图中可以更明确地看到数据点呈现从左上到右下的分布趋势，这直观地证实了二者之间的负相关关系。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_59.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_61.png)

## 第四步：进行回归分析

散点图展示了关系，但我们还需要一个量化的描述。回归分析可以帮助我们拟合出一条最能代表数据点趋势的直线，并用公式`y = ax + b`来描述这种关系，其中`a`是斜率（回归系数），直接体现了相关的强度和方向。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_63.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_65.png)

以下是使用`statsmodels`库进行简单线性回归分析的步骤：
1.  确定自变量（X）和因变量（Y）。例如，研究`VIX`变化对`SPX`的影响。
2.  使用`OLS`（普通最小二乘法）进行拟合。
3.  查看回归结果的摘要，获取回归系数、R平方等统计量。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_67.png)

以下是实现代码：
```python
import statsmodels.api as sm

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_69.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_71.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_73.png)

# 准备数据，X为VIX收益率，添加常数项
X = returns['VIX']
X = sm.add_constant(X)  # 添加常数项，对应截距b
y = returns['SPX']

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_75.png)

# 建立并拟合模型
model = sm.OLS(y, X).fit()

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_77.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_79.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_81.png)

# 打印详细的回归结果摘要
print(model.summary())

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_83.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_85.png)

# 提取并打印核心的回归系数
print(f"\n回归方程: SPX收益率 = {model.params['const']:.6f} + ({model.params['VIX']:.6f}) * VIX收益率")
print(f"回归系数（VIX的斜率）: {model.params['VIX']:.6f}")
```
运行代码后，我们可以在结果摘要中重点关注`VIX`对应的系数（`coef`）。如果这个系数是一个显著的负值（通常P值`P>|t|`小于0.05），就从统计上确认了`VIX`与`SPX`存在负相关关系。`R-squared`值则告诉我们这个模型能解释因变量（`SPX`）变化的百分比。

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_87.png)

![](img/9a71041b1d5bd34e88a194f2bf5dff0f_89.png)

本节课中我们一起学习了如何分析金融指标间的相关性。我们从**数据选取**开始，通过**绘制双轴折线图**进行初步观察，然后利用**散点图矩阵**直观展示变量分布与两两关系，最后通过**线性回归分析**量化了这种关系，得到了描述`SPX`与`VIX`负相关关系的具体**回归方程**。这套从可视化到量化分析的流程，是金融数据分析中探索变量关系的核心方法。