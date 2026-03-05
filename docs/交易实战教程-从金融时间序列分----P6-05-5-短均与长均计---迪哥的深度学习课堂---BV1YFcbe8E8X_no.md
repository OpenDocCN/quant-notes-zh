# Python金融分析与量化交易实战：P6：05-5-短均与长均计算实例 📈

![](img/c7b6ede68f3d28d85e013d1a38278a4b_1.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的技术分析策略。我们将识别“黄金交叉”和“死亡交叉”这两种常见的交易信号，并通过代码和图表直观地展示它们。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_3.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_5.png)

上一节我们介绍了金融时间序列数据的基本处理。本节中，我们来看看如何利用移动平均线进行策略分析。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_7.png)

## 策略核心：黄金交叉与死亡交叉

![](img/c7b6ede68f3d28d85e013d1a38278a4b_9.png)

该分析策略的核心是计算两个关键指标：短期移动平均线和长期移动平均线。通过观察这两条线的交叉点，我们可以判断潜在的买入或卖出时机。

*   **黄金交叉**：当短期均线从下方上穿长期均线时，通常被视为**买入信号**，预示着价格可能开始上涨。
*   **死亡交叉**：当短期均线从上方下穿长期均线时，通常被视为**卖出信号**，预示着价格可能开始下跌。

我们的目标就是通过代码计算出这两条均线，并找出它们的交叉点。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_11.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_13.png)

## 计算短期与长期移动平均线

![](img/c7b6ede68f3d28d85e013d1a38278a4b_15.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_17.png)

首先，我们需要为股票价格数据计算移动平均线。我们将以苹果公司（AAPL）的股价为例。

以下是计算步骤：
1.  选择目标股票数据（这里使用苹果股价）。
2.  分别计算短期（例如10天）和长期（例如30天）的移动平均值。
3.  将计算结果添加到数据框中。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_19.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_21.png)

```python
# 假设 df 是包含苹果股价的数据框，其中‘AAPL’是股价列
# 计算10天短期移动平均 (M1)
df[‘M1’] = df[‘AAPL’].rolling(window=10).mean()

![](img/c7b6ede68f3d28d85e013d1a38278a4b_23.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_25.png)

# 计算30天长期移动平均 (M2)
df[‘M2’] = df[‘AAPL’].rolling(window=30).mean()
```

![](img/c7b6ede68f3d28d85e013d1a38278a4b_27.png)

**代码解释**：
*   `df[‘AAPL’].rolling(window=10)` 创建了一个窗口大小为10的滚动窗口对象。
*   `.mean()` 对这个滚动窗口内的数据计算平均值，从而得到移动平均线。

> **注意**：在实际操作中，短期窗口常设为5天（一周交易日），长期窗口设为20或30天（一个月交易日）。由于示例数据时间跨度较长，我们使用了稍大的窗口以使图表更清晰。

## 可视化均线与交叉点

![](img/c7b6ede68f3d28d85e013d1a38278a4b_29.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_31.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_33.png)

计算出移动平均线后，我们可以通过绘图来直观地观察股价走势以及均线的交叉情况。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_35.png)

以下是绘图代码：
```python
import matplotlib.pyplot as plt

![](img/c7b6ede68f3d28d85e013d1a38278a4b_37.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_39.png)

# 绘制股价和移动平均线
df[[‘AAPL‘， ’M1‘， ’M2‘]].plot(figsize=(12， 6))
plt.title(‘Apple Stock Price with Moving Averages‘)
plt.show()
```

在生成的图表中：
*   **蓝色线**代表苹果股价。
*   **绿色线**代表短期移动平均线（M1）。
*   **红色线**代表长期移动平均线（M2）。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_41.png)

观察图表，我们可以手动识别交叉点：
*   当**绿线（短期）上穿红线（长期）**时，形成**黄金交叉**，是潜在的买入机会。
*   当**绿线（短期）下穿红线（长期）**时，形成**死亡交叉**，是潜在的卖出或观望信号。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_43.png)

## 进阶：量化标识交叉区域

![](img/c7b6ede68f3d28d85e013d1a38278a4b_45.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_47.png)

为了让信号更清晰，我们可以使用一个指标来量化显示当前是短期均线占优还是长期均线占优。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_49.png)

我们定义一个`position`列：
*   当短期均线 **大于** 长期均线时，标记为 **1**。
*   当短期均线 **小于** 长期均线时，标记为 **-1**。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_51.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_53.png)

这样，`position`值从 **-1 变为 1** 的点就是**黄金交叉**，从 **1 变为 -1** 的点就是**死亡交叉**。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_55.png)

以下是实现代码：
```python
import numpy as np

# 使用np.where创建位置信号
df[‘position’] = np.where(df[‘M1’] > df[‘M2’]， 1， -1)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_57.png)

# 创建双Y轴图表进行可视化
fig， ax1 = plt.subplots(figsize=(12，6))

![](img/c7b6ede68f3d28d85e013d1a38278a4b_59.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_61.png)

# 左Y轴：绘制股价和均线
color = ‘tab:blue‘
ax1.set_xlabel(‘Date‘)
ax1.set_ylabel(‘Price‘， color=color)
ax1.plot(df.index， df[‘AAPL‘]， color=color， label=‘Price‘， alpha=0.3)
ax1.plot(df.index， df[‘M1‘]， ‘g--‘， label=‘Short MA (M1)‘)
ax1.plot(df.index， df[‘M2‘]， ‘r--‘， label=‘Long MA (M2)‘)
ax1.tick_params(axis=’y‘， labelcolor=color)
ax1.legend(loc=’upper left‘)

# 右Y轴：绘制位置信号
ax2 = ax1.twinx()
color = ‘tab:purple‘
ax2.set_ylabel(‘Position Signal‘， color=color)
ax2.plot(df.index， df[‘position‘]， color=color， label=‘Position (1/-1)‘， linewidth=2)
ax2.tick_params(axis=’y‘， labelcolor=color)
ax2.set_ylim([-1.5， 1.5])
ax2.legend(loc=’upper right‘)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_63.png)

plt.title(‘Apple Stock with MA Crossover Signals‘)
fig.tight_layout()
plt.show()
```

![](img/c7b6ede68f3d28d85e013d1a38278a4b_65.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_67.png)

在这张双轴图中：
*   左轴（蓝色）清晰展示了股价与两条移动平均线的走势。
*   右轴（紫色）的`position`线在**1**和**-1**之间切换。
*   **紫色线从-1跃升到1的转折点**，对应左图中的**黄金交叉**。
*   **紫色线从1下降到-1的转折点**，对应左图中的**死亡交叉**。

![](img/c7b6ede68f3d28d85e013d1a38278a4b_69.png)

![](img/c7b6ede68f3d28d85e013d1a38278a4b_71.png)

本节课中我们一起学习了如何利用Pandas计算移动平均线，并基于“黄金交叉”和“死亡交叉”的概念构建了一个简单的可视化分析策略。我们不仅绘制了均线，还通过创建位置信号量化了交叉区域，使买卖信号更加一目了然。这为后续构建更复杂的量化交易模型打下了基础。