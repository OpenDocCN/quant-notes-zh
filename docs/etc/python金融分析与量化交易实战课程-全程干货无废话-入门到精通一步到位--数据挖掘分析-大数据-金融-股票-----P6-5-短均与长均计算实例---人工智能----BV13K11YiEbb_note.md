# Python金融分析与量化交易实战课程：P6：短均与长均计算实例 📈

![](img/ba83d90d95cca5cf6e96b63ab0caae20_1.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的技术分析策略。我们将重点理解“黄金交叉”和“死亡交叉”这两个核心概念，并使用Python代码实现计算与可视化。

![](img/ba83d90d95cca5cf6e96b63ab0caae20_3.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_5.png)

## 概述

上一节我们介绍了数据处理的基础，本节中我们来看看如何应用这些知识进行实际的技术指标计算。我们将以苹果公司的股票数据为例，计算其短期和长期移动平均线，并通过图表分析买卖信号。

![](img/ba83d90d95cca5cf6e96b63ab0caae20_7.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_9.png)

## 计算短期与长期移动平均线

要分析黄金交叉和死亡交叉，首先需要计算两个关键指标：短期移动平均线和长期移动平均线。移动平均线是过去一段时间内股价的平均值，能平滑价格波动，反映趋势。

以下是计算步骤：

![](img/ba83d90d95cca5cf6e96b63ab0caae20_11.png)

1.  **选择数据**：我们使用苹果公司（AAPL）的历史股价数据。
2.  **定义窗口**：短期窗口通常较小，例如5天或10天；长期窗口较大，例如20天或30天。由于我们的数据时间跨度较长，为让图表更清晰，我们将使用10天作为短期窗口，30天作为长期窗口。
3.  **进行计算**：使用Pandas的`.rolling()`和`.mean()`方法计算移动平均值。

![](img/ba83d90d95cca5cf6e96b63ab0caae20_13.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_15.png)

核心计算公式如下：
`移动平均值 = 股价序列.rolling(window=窗口大小).mean()`

![](img/ba83d90d95cca5cf6e96b63ab0caae20_17.png)

让我们开始编写代码。

![](img/ba83d90d95cca5cf6e96b63ab0caae20_19.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

![](img/ba83d90d95cca5cf6e96b63ab0caae20_21.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_23.png)

# 假设我们已经有了包含苹果股价的DataFrame，名为 `df`，其中‘AAPL’列为股价
# 计算短期移动平均线 (M1)，窗口为10天
df[‘M1’] = df[‘AAPL’].rolling(window=10).mean()

![](img/ba83d90d95cca5cf6e96b63ab0caae20_25.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_27.png)

# 计算长期移动平均线 (M2)，窗口为30天
df[‘M2’] = df[‘AAPL’].rolling(window=30).mean()

# 查看计算结果，选取股价和两个移动平均线
data_to_display = df[[‘AAPL‘， ’M1‘， ’M2‘]]
print(data_to_display.head())
```

## 可视化移动平均线与交叉点

![](img/ba83d90d95cca5cf6e96b63ab0caae20_29.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_31.png)

计算出指标后，最直观的方式是将其绘制在图表上，观察它们的交叉情况。

![](img/ba83d90d95cca5cf6e96b63ab0caae20_33.png)

以下是绘图步骤：

![](img/ba83d90d95cca5cf6e96b63ab0caae20_35.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_37.png)

```python
# 绘制股价及移动平均线
plt.figure(figsize=(12， 6))
plt.plot(df.index， df[‘AAPL’]， label=‘AAPL Price’， alpha=0.5)
plt.plot(df.index， df[‘M1’]， label=‘10-Day MA (Short)’)
plt.plot(df.index， df[‘M2’]， label=‘30-Day MA (Long)’)
plt.title(‘Apple Stock Price with Moving Averages’)
plt.xlabel(‘Date’)
plt.ylabel(‘Price’)
plt.legend()
plt.show()
```

![](img/ba83d90d95cca5cf6e96b63ab0caae20_39.png)

为了使趋势更清晰，我们可以只选取最近一段时间的数据进行绘图。

## 识别黄金交叉与死亡交叉

![](img/ba83d90d95cca5cf6e96b63ab0caae20_41.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_43.png)

从图表上，我们可以手动识别交叉点：
*   **黄金交叉**：短期均线从下向上穿越长期均线，通常被视为买入信号。
*   **死亡交叉**：短期均线从上向下穿越长期均线，通常被视为卖出信号。

![](img/ba83d90d95cca5cf6e96b63ab0caae20_45.png)

为了更精确、自动化地识别这些信号，我们可以创建一个“位置”指标。

![](img/ba83d90d95cca5cf6e96b63ab0caae20_47.png)

以下是创建信号指标的步骤：

![](img/ba83d90d95cca5cf6e96b63ab0caae20_49.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_51.png)

1.  **定义规则**：当短期均线（M1）高于长期均线（M2）时，标记为1（可能代表持有或看涨）；当低于时，标记为-1（可能代表卖出或看跌）。
2.  **使用`np.where`**：这个函数能根据条件，为数组中的每个元素分配不同的值。

![](img/ba83d90d95cca5cf6e96b63ab0caae20_53.png)

核心代码如下：
`df[‘position’] = np.where(df[‘M1’] > df[‘M2’]， 1， -1)`

![](img/ba83d90d95cca5cf6e96b63ab0caae20_55.png)

```python
# 生成交易信号位置列
df[‘position’] = np.where(df[‘M1’] > df[‘M2’]， 1， -1)

# 为了更清晰地展示信号，我们使用双Y轴进行绘图
fig， ax1 = plt.subplots(figsize=(14， 7))

![](img/ba83d90d95cca5cf6e96b63ab0caae20_57.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_59.png)

# 左Y轴：绘制股价和移动平均线
color = ‘tab:blue’
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’， color=color)
ax1.plot(df.index， df[‘AAPL’]， color=color， label=‘AAPL Price’， alpha=0.3)
ax1.plot(df.index， df[‘M1’]， color=‘green’， label=‘10-Day MA’)
ax1.plot(df.index， df[‘M2’]， color=‘red’， label=‘30-Day MA’)
ax1.tick_params(axis=’y’， labelcolor=color)
ax1.legend(loc=’upper left’)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_61.png)

# 右Y轴：绘制位置信号
ax2 = ax1.twinx()
color = ‘tab:purple’
ax2.set_ylabel(‘Signal Position’， color=color)
ax2.plot(df.index， df[‘position’]， color=color， label=‘Trading Signal’， linewidth=2)
ax2.tick_params(axis=’y’， labelcolor=color)
ax2.set_yticks([-1， 1])
ax2.set_yticklabels([‘Sell Signal (-1)’， ‘Buy Signal (1)’])

plt.title(‘Apple Stock Price， Moving Averages and Trading Signals’)
fig.tight_layout()
plt.show()
```

![](img/ba83d90d95cca5cf6e96b63ab0caae20_63.png)

在这张图中，紫色信号线清晰地展示了交叉点：
*   信号从 **-1 变为 1** 的点，对应**黄金交叉**（买入信号）。
*   信号从 **1 变为 -1** 的点，对应**死亡交叉**（卖出信号）。

![](img/ba83d90d95cca5cf6e96b63ab0caae20_65.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_67.png)

## 总结

![](img/ba83d90d95cca5cf6e96b63ab0caae20_69.png)

![](img/ba83d90d95cca5cf6e96b63ab0caae20_71.png)

本节课中我们一起学习了技术分析中的一个基础策略。我们首先计算了股票的短期和长期移动平均线，然后通过可视化图表观察了它们的走势。最后，我们利用`np.where`函数创建了一个交易信号指标，自动化地识别出代表买入机会的“黄金交叉”和代表卖出风险的“死亡交叉”。这个过程展示了如何将Pandas的数据处理能力与简单的金融逻辑结合，为量化交易策略打下基础。