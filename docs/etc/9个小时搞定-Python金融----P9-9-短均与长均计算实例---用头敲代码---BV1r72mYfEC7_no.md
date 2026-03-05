# Python金融量化分析：P9：短均与长均计算实例 📈

![](img/00a861123d15dc9eb4c4da93be904f0b_1.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的技术分析策略。我们将识别“黄金交叉”和“死亡交叉”这两种常见的交易信号，并通过代码和图表直观地展示它们。

![](img/00a861123d15dc9eb4c4da93be904f0b_3.png)

## 概述

![](img/00a861123d15dc9eb4c4da93be904f0b_5.png)

我们将分析苹果公司的股票数据。核心策略是计算两个移动平均线：一个短期均线和一个长期均线。通过观察这两条线的交叉点，我们可以判断潜在的买入或卖出时机。短期均线向上穿越长期均线被称为“黄金交叉”，通常被视为买入信号；反之，短期均线向下穿越长期均线被称为“死亡交叉”，通常被视为卖出信号。

![](img/00a861123d15dc9eb4c4da93be904f0b_7.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_9.png)

## 计算短期与长期移动平均线

上一节我们介绍了数据准备，本节中我们来看看如何计算核心指标。

首先，我们需要指定要分析的股票数据，这里我们使用苹果公司的股票。接着，我们定义两个时间窗口来计算移动平均线。短期窗口通常较小，例如5天或10天；长期窗口则较大，例如20天或30天。由于我们的数据集时间跨度较长，为了图表更清晰，我们将使用稍大的窗口值。

以下是计算步骤：

![](img/00a861123d15dc9eb4c4da93be904f0b_11.png)

1.  计算短期移动平均线（例如10天窗口）。
2.  计算长期移动平均线（例如30天窗口）。
3.  将计算结果添加到数据框中。

![](img/00a861123d15dc9eb4c4da93be904f0b_13.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_15.png)

对应的核心代码如下：

![](img/00a861123d15dc9eb4c4da93be904f0b_17.png)

```python
# 假设 df 是包含苹果股价的数据框，列名为 ‘AAPL’
# 计算短期移动平均线（10天窗口）
short_window = 10
df[‘M1’] = df[‘AAPL’].rolling(window=short_window).mean()

![](img/00a861123d15dc9eb4c4da93be904f0b_19.png)

# 计算长期移动平均线（30天窗口）
long_window = 30
df[‘M2’] = df[‘AAPL’].rolling(window=long_window).mean()
```

![](img/00a861123d15dc9eb4c4da93be904f0b_21.png)

## 可视化均线与交叉点

![](img/00a861123d15dc9eb4c4da93be904f0b_23.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_25.png)

计算完指标后，我们可以将它们绘制在图表上，以便直观地观察交叉情况。

![](img/00a861123d15dc9eb4c4da93be904f0b_27.png)

我们将苹果股价、短期均线（M1）和长期均线（M2）绘制在同一张图上。为了使图表更清晰，我们可以只选取最近一段时间的数据进行展示。

以下是绘图代码示例：

```python
import matplotlib.pyplot as plt

![](img/00a861123d15dc9eb4c4da93be904f0b_29.png)

# 选取最近250个交易日的数据进行绘图（约一年）
plot_data = df[[‘AAPL‘， ‘M1‘， ‘M2’]].tail(250)

![](img/00a861123d15dc9eb4c4da93be904f0b_31.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_33.png)

plt.figure(figsize=(12， 6))
plt.plot(plot_data[‘AAPL’]， label=‘Apple Stock Price’， color=‘blue’)
plt.plot(plot_data[‘M1’]， label=‘Short-term MA (10-day)’， color=‘green’)
plt.plot(plot_data[‘M2’]， label=‘Long-term MA (30-day)’， color=‘red’)
plt.legend()
plt.title(‘Apple Stock Price with Moving Averages’)
plt.show()
```

![](img/00a861123d15dc9eb4c4da93be904f0b_35.png)

在生成的图表中，我们可以寻找交叉点：
*   **死亡交叉**：当绿色短期均线向下穿越红色长期均线时，预示下跌趋势，为卖出信号。
*   **黄金交叉**：当绿色短期均线向上穿越红色长期均线时，预示上涨趋势，为买入信号。

![](img/00a861123d15dc9eb4c4da93be904f0b_37.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_39.png)

## 量化交叉信号

为了更精确地定位交叉点，我们可以创建一个信号列来量化短期均线与长期均线的关系。

我们使用 `np.where` 函数进行比较：当短期均线大于长期均线时，标记为 `1`；当短期均线小于长期均线时，标记为 `-1`。这个 `position` 列清晰地标明了均线的相对位置。

![](img/00a861123d15dc9eb4c4da93be904f0b_41.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_43.png)

以下是实现代码：

```python
import numpy as np

![](img/00a861123d15dc9eb4c4da93be904f0b_45.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_47.png)

# 生成交易信号
df[‘position’] = np.where(df[‘M1’] > df[‘M2’]， 1， -1)
```

![](img/00a861123d15dc9eb4c4da93be904f0b_49.png)

## 高级可视化：双坐标轴展示

![](img/00a861123d15dc9eb4c4da93be904f0b_51.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_53.png)

由于股价和信号值（1或-1）的量纲差异巨大，直接绘制在同一坐标轴上会导致信号线难以辨认。为了解决这个问题，我们使用双坐标轴进行绘图。

![](img/00a861123d15dc9eb4c4da93be904f0b_55.png)

主坐标轴（左侧）显示股价和移动平均线，次坐标轴（右侧）专门显示我们的交易信号线（`position`）。

以下是使用双坐标轴的绘图代码：

```python
fig， ax1 = plt.subplots(figsize=(14， 7))

![](img/00a861123d15dc9eb4c4da93be904f0b_57.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_59.png)

# 在主坐标轴（ax1）上绘制股价和均线
ax1.plot(plot_data[‘AAPL’]， label=‘Stock Price’， color=‘blue’)
ax1.plot(plot_data[‘M1’]， label=‘10-day MA’， color=‘green’)
ax1.plot(plot_data[‘M2’]， label=‘30-day MA’， color=‘red’)
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

![](img/00a861123d15dc9eb4c4da93be904f0b_61.png)

# 创建次坐标轴（ax2）用于绘制信号
ax2 = ax1.twinx()
ax2.plot(plot_data[‘position’]， label=‘Trading Signal’， color=‘orange’， linestyle=‘--’)
ax2.set_ylabel(‘Signal (1/-1)’)
ax2.legend(loc=‘upper right’)

plt.title(‘Stock Price， Moving Averages， and Trading Signals’)
plt.show()
```

在这张图上，信号线在 `1` 和 `-1` 之间切换。**从 `1` 切换到 `-1` 的点对应“死亡交叉”**，**从 `-1` 切换到 `1` 的点对应“黄金交叉”**。这使得买卖信号的识别变得一目了然。

![](img/00a861123d15dc9eb4c4da93be904f0b_63.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_65.png)

## 总结

![](img/00a861123d15dc9eb4c4da93be904f0b_67.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_69.png)

本节课中我们一起学习了如何利用移动平均线构建一个基础的量化交易策略。我们完成了以下步骤：
1.  计算了短期和长期移动平均线。
2.  通过图表直观展示了“黄金交叉”和“死亡交叉”。
3.  使用 `np.where` 函数量化了交叉信号。
4.  利用双坐标轴技术实现了股价、均线与交易信号的高级可视化。

![](img/00a861123d15dc9eb4c4da93be904f0b_71.png)

![](img/00a861123d15dc9eb4c4da93be904f0b_73.png)

这个策略是许多复杂模型的基础。在实际应用中，你可以调整窗口参数、结合其他指标（如成交量、波动率）或添加回测逻辑来优化策略效果。Pandas 和 Matplotlib 为我们提供了强大的工具，可以轻松实现这些金融数据分析与可视化任务。