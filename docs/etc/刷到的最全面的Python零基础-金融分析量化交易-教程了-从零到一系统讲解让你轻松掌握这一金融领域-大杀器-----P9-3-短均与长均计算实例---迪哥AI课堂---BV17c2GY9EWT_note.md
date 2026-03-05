# Python金融分析量化交易：P9：3-短均与长均计算实例 📈

![](img/f52dbb9fd3681bf60a90849c8227eb91_1.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的分析策略。我们将识别“黄金交叉”和“死亡交叉”这两种关键信号，并通过可视化清晰地展示它们。

![](img/f52dbb9fd3681bf60a90849c8227eb91_3.png)

上一节我们介绍了数据获取与处理的基础知识，本节中我们来看看如何应用这些知识进行具体的指标计算和策略分析。

![](img/f52dbb9fd3681bf60a90849c8227eb91_5.png)

## 策略概述：黄金交叉与死亡交叉

![](img/f52dbb9fd3681bf60a90849c8227eb91_7.png)

该分析策略的核心是计算两个技术指标：短期移动平均线和长期移动平均线。通过观察这两条线的交叉点，我们可以判断市场的潜在买卖时机。

![](img/f52dbb9fd3681bf60a90849c8227eb91_9.png)

*   **黄金交叉**：当短期均线从下方上穿长期均线时，通常被视为买入信号，预示着价格可能开始上涨。
*   **死亡交叉**：当短期均线从上方下穿长期均线时，通常被视为卖出信号，预示着价格可能开始下跌。

接下来，我们将按照以下步骤实现这个策略。

## 第一步：计算短期移动平均线

![](img/f52dbb9fd3681bf60a90849c8227eb91_11.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_13.png)

首先，我们需要计算短期移动平均线。在股票市场中，5日移动平均线（对应一周的交易日）较为常见。但由于我们的示例数据时间跨度较长，为了更清晰地展示效果，我们将使用稍大的窗口。

![](img/f52dbb9fd3681bf60a90849c8227eb91_15.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_17.png)

以下是计算短期移动平均线的代码。我们以苹果公司（AAPL）的股价数据为例，计算其10日移动平均线。

![](img/f52dbb9fd3681bf60a90849c8227eb91_19.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_21.png)

```python
# 假设 df 是包含苹果股价的DataFrame，其中‘AAPL’是股价列
short_window = 10
df[‘M1’] = df[‘AAPL’].rolling(window=short_window).mean()
```

这段代码使用 `.rolling(window=10)` 创建了一个10天的滚动窗口，然后 `.mean()` 计算了该窗口内股价的平均值，并将结果存储在新列 `‘M1’` 中。

![](img/f52dbb9fd3681bf60a90849c8227eb91_23.png)

## 第二步：计算长期移动平均线

![](img/f52dbb9fd3681bf60a90849c8227eb91_25.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_27.png)

接着，我们计算长期移动平均线。常见的长期窗口有20日或30日（对应约一个月的交易日）。我们继续使用苹果股价数据，计算30日移动平均线。

![](img/f52dbb9fd3681bf60a90849c8227eb91_29.png)

以下是计算长期移动平均线的代码。

![](img/f52dbb9fd3681bf60a90849c8227eb91_31.png)

```python
long_window = 30
df[‘M2’] = df[‘AAPL’].rolling(window=long_window).mean()
```

这段代码计算了30日移动平均值，并将结果存储在新列 `‘M2’` 中。

## 第三步：可视化股价与均线

![](img/f52dbb9fd3681bf60a90849c8227eb91_33.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_35.png)

计算完指标后，最直观的方式是将它们绘制出来进行观察。我们将苹果股价、短期均线（M1）和长期均线（M2）绘制在同一张图上。

![](img/f52dbb9fd3681bf60a90849c8227eb91_37.png)

以下是绘制股价与移动平均线的代码。

![](img/f52dbb9fd3681bf60a90849c8227eb91_39.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_41.png)

```python
import matplotlib.pyplot as plt

![](img/f52dbb9fd3681bf60a90849c8227eb91_43.png)

# 绘制图形
plt.figure(figsize=(12, 6))
plt.plot(df[‘AAPL’], label=‘AAPL Price’, alpha=0.5)
plt.plot(df[‘M1’], label=‘10-Day MA’, color=‘green’)
plt.plot(df[‘M2’], label=‘30-Day MA’, color=‘red’)
plt.legend()
plt.show()
```

通过观察生成的图表，我们可以手动寻找短期均线（绿色）与长期均线（红色）的交叉点。交叉点附近就是潜在的“黄金交叉”或“死亡交叉”信号。

![](img/f52dbb9fd3681bf60a90849c8227eb91_45.png)

## 第四步：量化交叉信号

![](img/f52dbb9fd3681bf60a90849c8227eb91_47.png)

为了更精确、自动化地识别交叉信号，我们可以创建一个信号列。当短期均线高于长期均线时，标记为 `1`（看涨信号）；当短期均线低于长期均线时，标记为 `-1`（看跌信号）。

![](img/f52dbb9fd3681bf60a90849c8227eb91_49.png)

以下是使用NumPy的 `where` 函数生成信号列的代码。

![](img/f52dbb9fd3681bf60a90849c8227eb91_51.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_53.png)

```python
import numpy as np
df[‘position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
```

![](img/f52dbb9fd3681bf60a90849c8227eb91_55.png)

现在，`df[‘position’]` 列中的 `1` 和 `-1` 清晰地标明了每个时间点短期均线与长期均线的相对位置关系。

![](img/f52dbb9fd3681bf60a90849c8227eb91_57.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_59.png)

## 第五步：高级可视化：双Y轴图表

![](img/f52dbb9fd3681bf60a90849c8227eb91_61.png)

由于股价和信号值（1/-1）的量纲差异巨大，直接绘制在同一坐标轴上会导致信号线几乎无法辨认。为了解决这个问题，我们使用双Y轴进行绘图。

以下是创建双Y轴图表的代码，左侧Y轴显示股价和均线，右侧Y轴显示信号。

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/f52dbb9fd3681bf60a90849c8227eb91_63.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_65.png)

# 左侧Y轴：绘制股价和均线
ax1.plot(df[‘AAPL’], label=‘AAPL Price’, color=‘blue’, alpha=0.3)
ax1.plot(df[‘M1’], label=‘10-Day MA’, color=‘green’)
ax1.plot(df[‘M2’], label=‘30-Day MA’, color=‘red’)
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

![](img/f52dbb9fd3681bf60a90849c8227eb91_67.png)

# 创建右侧Y轴：绘制信号线
ax2 = ax1.twinx()
ax2.plot(df[‘position’], label=‘Signal (1/-1)’, color=‘purple’, linestyle=‘--’)
ax2.set_ylabel(‘Signal’)
ax2.legend(loc=‘upper right’)

plt.title(‘AAPL Price with Moving Averages and Trading Signal’)
plt.show()
```

![](img/f52dbb9fd3681bf60a90849c8227eb91_69.png)

在这张图中，紫色虚线（信号线）在 `1` 和 `-1` 之间切换。信号线从 `-1` 向上穿越到 `1` 的点，对应图表中绿色短期均线上穿红色长期均线的点，即“黄金交叉”。反之，信号线从 `1` 向下穿越到 `-1` 的点，则对应“死亡交叉”。这样，买卖信号就变得一目了然。

![](img/f52dbb9fd3681bf60a90849c8227eb91_71.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_73.png)

## 总结

![](img/f52dbb9fd3681bf60a90849c8227eb91_75.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_77.png)

本节课中我们一起学习了如何构建一个基于移动平均线交叉的简单股票分析策略。

![](img/f52dbb9fd3681bf60a90849c8227eb91_79.png)

![](img/f52dbb9fd3681bf60a90849c8227eb91_81.png)

我们首先计算了短期（10日）和长期（30日）移动平均线，然后通过可视化图表观察了“黄金交叉”和“死亡交叉”。为了更精确地识别这些信号，我们引入了 `np.where` 函数来量化生成交易信号（1表示看涨，-1表示看跌）。最后，通过绘制双Y轴图表，我们清晰地将价格走势、技术指标和交易信号整合在一起，完成了从指标计算到信号可视化的完整分析流程。这个实例展示了如何利用Pandas进行灵活的金融数据计算，并利用Matplotlib进行有效的策略结果展示。