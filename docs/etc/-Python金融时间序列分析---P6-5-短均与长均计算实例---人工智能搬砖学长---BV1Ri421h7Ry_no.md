# Python金融时间序列分析与量化交易实战教程：P6：短均与长均计算实例 📈

![](img/7be246e7cb62f2b649332f81aab455e6_0.png)

![](img/7be246e7cb62f2b649332f81aab455e6_2.png)

在本节课中，我们将学习如何计算股票的短期和长期移动平均线，并利用它们识别“黄金交叉”和“死亡交叉”这两种常见的交易信号。我们将通过具体的代码实例，一步步展示如何计算、可视化并分析这些指标。

![](img/7be246e7cb62f2b649332f81aab455e6_4.png)

![](img/7be246e7cb62f2b649332f81aab455e6_6.png)

## 概述

![](img/7be246e7cb62f2b649332f81aab455e6_8.png)

移动平均线是技术分析中最基础也最重要的工具之一。通过计算不同时间窗口内的平均价格，我们可以平滑价格波动，观察趋势。当短期移动平均线从下方上穿长期移动平均线时，形成“黄金交叉”，通常被视为买入信号；反之，当短期线从上方下穿长期线时，形成“死亡交叉”，通常被视为卖出信号。本节我们将使用Python的Pandas库来实现这一分析策略。

![](img/7be246e7cb62f2b649332f81aab455e6_10.png)

## 计算短期与长期移动平均线

![](img/7be246e7cb62f2b649332f81aab455e6_12.png)

首先，我们需要计算两个移动平均线：一个短期（例如10天）和一个长期（例如30天）。我们将以苹果公司（AAPL）的股票数据为例。

以下是计算移动平均线的步骤：

![](img/7be246e7cb62f2b649332f81aab455e6_14.png)

![](img/7be246e7cb62f2b649332f81aab455e6_16.png)

1.  **选择目标股票数据**：从数据集中提取苹果公司的股价数据。
2.  **计算短期移动平均**：使用`.rolling(window).mean()`方法计算指定窗口（如10天）的平均值。
3.  **计算长期移动平均**：使用相同的方法，但采用更大的窗口（如30天）。
4.  **整合结果**：将计算结果作为新列添加到数据框中。

![](img/7be246e7cb62f2b649332f81aab455e6_18.png)

![](img/7be246e7cb62f2b649332f81aab455e6_20.png)

```python
# 假设 `data` 是包含苹果股价的DataFrame，列名为 ‘AAPL‘
# 计算10日短期移动平均
data[‘M1‘] = data[‘AAPL‘].rolling(window=10).mean()

![](img/7be246e7cb62f2b649332f81aab455e6_22.png)

# 计算30日长期移动平均
data[‘M2‘] = data[‘AAPL‘].rolling(window=30).mean()
```

![](img/7be246e7cb62f2b649332f81aab455e6_24.png)

![](img/7be246e7cb62f2b649332f81aab455e6_26.png)

## 可视化移动平均线与股价

![](img/7be246e7cb62f2b649332f81aab455e6_28.png)

![](img/7be246e7cb62f2b649332f81aab455e6_30.png)

![](img/7be246e7cb62f2b649332f81aab455e6_32.png)

计算完成后，最直观的方式是将股价与两条移动平均线绘制在同一张图上进行观察。

![](img/7be246e7cb62f2b649332f81aab455e6_34.png)

![](img/7be246e7cb62f2b649332f81aab455e6_36.png)

以下是绘制图表的代码：

![](img/7be246e7cb62f2b649332f81aab455e6_38.png)

```python
import matplotlib.pyplot as plt

# 绘制股价与移动平均线
data[[‘AAPL‘, ‘M1‘, ‘M2‘]].plot(figsize=(12, 6))
plt.title(‘Apple Stock Price with Moving Averages‘)
plt.xlabel(‘Date‘)
plt.ylabel(‘Price‘)
plt.show()
```

![](img/7be246e7cb62f2b649332f81aab455e6_40.png)

![](img/7be246e7cb62f2b649332f81aab455e6_42.png)

在实际操作中，如果数据时间跨度太长，图表会显得非常拥挤，交叉点难以辨认。为了解决这个问题，我们可以截取最近一段时间（例如250个交易日，约一年）的数据进行绘图，这样趋势和交叉点会更加清晰。

![](img/7be246e7cb62f2b649332f81aab455e6_44.png)

![](img/7be246e7cb62f2b649332f81aab455e6_46.png)

## 识别黄金交叉与死亡交叉

![](img/7be246e7cb62f2b649332f81aab455e6_48.png)

![](img/7be246e7cb62f2b649332f81aab455e6_50.png)

通过观察图表，我们可以手动识别交叉点。当绿色的短期均线（M1）从下方上穿红色的长期均线（M2）时，那个交叉点就是“黄金交叉”，预示着可能的上涨趋势，是买入时机。当短期均线从上方下穿长期均线时，那个交叉点就是“死亡交叉”，预示着可能的下跌趋势，是卖出或观望时机。

![](img/7be246e7cb62f2b649332f81aab455e6_52.png)

![](img/7be246e7cb62f2b649332f81aab455e6_54.png)

为了更精确、自动化地识别这些交叉点，我们可以创建一个信号列。这个信号列在短期均线高于长期均线时标记为1，反之标记为-1。这样，信号从-1变为1的点就是黄金交叉，从1变为-1的点就是死亡交叉。

以下是创建信号列的代码：

![](img/7be246e7cb62f2b649332f81aab455e6_56.png)

![](img/7be246e7cb62f2b649332f81aab455e6_58.png)

```python
import numpy as np

![](img/7be246e7cb62f2b649332f81aab455e6_60.png)

# 创建信号列：M1 > M2 时为1，否则为-1
data[‘position‘] = np.where(data[‘M1‘] > data[‘M2‘], 1, -1)
```

![](img/7be246e7cb62f2b649332f81aab455e6_62.png)

![](img/7be246e7cb62f2b649332f81aab455e6_64.png)

![](img/7be246e7cb62f2b649332f81aab455e6_66.png)

## 高级可视化：结合信号线

![](img/7be246e7cb62f2b649332f81aab455e6_68.png)

![](img/7be246e7cb62f2b649332f81aab455e6_70.png)

为了更直观地展示买卖信号点，我们可以将信号线也绘制在图上。由于信号值（1和-1）与股价数值相差巨大，直接绘制在同一坐标轴下会几乎看不见。因此，我们需要使用双Y轴来分别显示价格和信号。

![](img/7be246e7cb62f2b649332f81aab455e6_72.png)

以下是使用双Y轴绘图的代码：

![](img/7be246e7cb62f2b649332f81aab455e6_74.png)

![](img/7be246e7cb62f2b649332f81aab455e6_76.png)

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/7be246e7cb62f2b649332f81aab455e6_78.png)

![](img/7be246e7cb62f2b649332f81aab455e6_80.png)

# 第一个Y轴：绘制股价和移动平均线
color = ‘tab:blue‘
ax1.set_xlabel(‘Date‘)
ax1.set_ylabel(‘Price‘, color=color)
ax1.plot(data.index, data[‘AAPL‘], color=color, alpha=0.3, label=‘AAPL Price‘)
ax1.plot(data.index, data[‘M1‘], ‘g--‘, label=‘Short MA (10)‘)
ax1.plot(data.index, data[‘M2‘], ‘r--‘, label=‘Long MA (30)‘)
ax1.tick_params(axis=‘y‘, labelcolor=color)
ax1.legend(loc=‘upper left‘)

![](img/7be246e7cb62f2b649332f81aab455e6_82.png)

![](img/7be246e7cb62f2b649332f81aab455e6_84.png)

![](img/7be246e7cb62f2b649332f81aab455e6_86.png)

# 第二个Y轴：绘制信号线
ax2 = ax1.twinx()
color = ‘tab:purple‘
ax2.set_ylabel(‘Signal‘, color=color)
ax2.plot(data.index, data[‘position‘], color=color, linestyle=‘-‘, label=‘Signal (1/-1)‘, alpha=0.7)
ax2.tick_params(axis=‘y‘, labelcolor=color)
ax2.set_ylim(-1.5, 1.5) # 固定信号轴范围
ax2.legend(loc=‘upper right‘)

![](img/7be246e7cb62f2b649332f81aab455e6_88.png)

![](img/7be246e7cb62f2b649332f81aab455e6_90.png)

plt.title(‘Apple Stock with Moving Averages and Trading Signals‘)
fig.tight_layout()
plt.show()
```

![](img/7be246e7cb62f2b649332f81aab455e6_92.png)

![](img/7be246e7cb62f2b649332f81aab455e6_94.png)

在这张图中，紫色的信号线在1和-1之间切换。当它从-1向上突破到1时，对应图表中短期均线上穿长期均线的“黄金交叉”点。当它从1向下突破到-1时，则对应“死亡交叉”点。这样，交易信号就一目了然。

![](img/7be246e7cb62f2b649332f81aab455e6_96.png)

![](img/7be246e7cb62f2b649332f81aab455e6_98.png)

## 总结

本节课我们一起学习了金融时间序列分析中的一个核心实战策略：利用移动平均线的交叉进行交易信号判断。

![](img/7be246e7cb62f2b649332f81aab455e6_100.png)

![](img/7be246e7cb62f2b649332f81aab455e6_102.png)

我们首先介绍了如何用Pandas计算短期和长期移动平均线，核心是`DataFrame[‘column‘].rolling(window).mean()`这个公式。接着，我们通过Matplotlib将股价与均线可视化，并解释了如何从图上识别“黄金交叉”和“死亡交叉”。

![](img/7be246e7cb62f2b649332f81aab455e6_104.png)

![](img/7be246e7cb62f2b649332f81aab455e6_106.png)

![](img/7be246e7cb62f2b649332f81aab455e6_108.png)

为了更精确地定位交叉点，我们进一步使用NumPy的`np.where`函数创建了买卖信号列，并通过双Y轴绘图技术，将价格走势与交易信号清晰地整合在一张图中展示。

![](img/7be246e7cb62f2b649332f81aab455e6_110.png)

![](img/7be246e7cb62f2b649332f81aab455e6_112.png)

这个实例展示了如何将基础的Pandas数据操作、数值计算与可视化结合起来，完成一个完整的、有逻辑的金融数据分析流程，为后续构建更复杂的量化交易模型打下了坚实基础。