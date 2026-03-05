# Python机器学习与量化交易：P8：9.9.3-短均与长均计算实例 📈

![](img/4e5d96c01714cb65afb6a11a61c8b765_1.png)

在本节课中，我们将学习如何对股票序列进行分析，通过计算短期和长期移动平均线，并识别“黄金交叉”与“死亡交叉”来构建一个简单的交易策略。我们将使用Python的Pandas库进行计算，并用Matplotlib进行可视化。

![](img/4e5d96c01714cb65afb6a11a61c8b765_3.png)

## 概述

![](img/4e5d96c01714cb65afb6a11a61c8b765_5.png)

我们将分析苹果公司的股票数据。核心策略是计算两个关键指标：短期移动平均线（例如10天）和长期移动平均线（例如30天）。通过观察这两条线的交叉点，我们可以判断潜在的买入或卖出时机。

![](img/4e5d96c01714cb65afb6a11a61c8b765_7.png)

## 计算短期与长期移动平均线

![](img/4e5d96c01714cb65afb6a11a61c8b765_9.png)

上一节我们介绍了移动平均线的概念，本节中我们来看看如何用代码具体计算。

首先，我们需要加载股票数据。这里我们以苹果公司（AAPL）的股票为例。

```python
# 假设 df 是包含苹果股票价格（例如‘Close’列）的DataFrame
# 计算10日短期移动平均线
short_window = 10
df[‘M1’] = df[‘AAPL’].rolling(window=short_window).mean()

![](img/4e5d96c01714cb65afb6a11a61c8b765_11.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_13.png)

# 计算30日长期移动平均线
long_window = 30
df[‘M2’] = df[‘AAPL’].rolling(window=long_window).mean()
```

![](img/4e5d96c01714cb65afb6a11a61c8b765_15.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_17.png)

代码解释：
*   `df[‘AAPL’]` 代表苹果股票的收盘价序列。
*   `.rolling(window=10)` 创建一个滑动窗口对象，窗口大小为10天。
*   `.mean()` 计算窗口内数据的平均值，即移动平均线。

![](img/4e5d96c01714cb65afb6a11a61c8b765_19.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_21.png)

## 可视化股价与移动平均线

计算出指标后，我们可以将它们与原始股价一起绘制出来，以便直观观察。

![](img/4e5d96c01714cb65afb6a11a61c8b765_23.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_25.png)

以下是绘制折线图的代码：

![](img/4e5d96c01714cb65afb6a11a61c8b765_27.png)

```python
import matplotlib.pyplot as plt

![](img/4e5d96c01714cb65afb6a11a61c8b765_29.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_31.png)

# 选择要绘制的列
plot_data = df[[‘AAPL‘， ‘M1‘， ‘M2‘]]

# 设置图形大小
plt.figure(figsize=(12， 6))

# 绘制折线图
plot_data.plot()

![](img/4e5d96c01714cb65afb6a11a61c8b765_33.png)

# 显示图形
plt.show()
```

![](img/4e5d96c01714cb65afb6a11a61c8b765_35.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_37.png)

为了使交叉点更清晰，我们可以只选取一段时间的数据进行绘图，例如最近250个交易日（大约一年）。

![](img/4e5d96c01714cb65afb6a11a61c8b765_39.png)

## 识别黄金交叉与死亡交叉

![](img/4e5d96c01714cb65afb6a11a61c8b765_41.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_43.png)

观察生成的图表，我们可以手动识别两种重要的交叉形态：

*   **黄金交叉**：当短期移动平均线（M1）从下方向上穿越长期移动平均线（M2）时，通常被视为**买入信号**，预示着上涨趋势可能开始。
*   **死亡交叉**：当短期移动平均线（M1）从上方向下穿越长期移动平均线（M2）时，通常被视为**卖出信号**，预示着下跌趋势可能开始。

## 使用信号指标进行增强分析

![](img/4e5d96c01714cb65afb6a11a61c8b765_45.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_47.png)

为了更自动化地识别交叉点，我们可以创建一个信号指标。

![](img/4e5d96c01714cb65afb6a11a61c8b765_49.png)

以下是创建信号指标的代码：

![](img/4e5d96c01714cb65afb6a11a61c8b765_51.png)

```python
import numpy as np

![](img/4e5d96c01714cb65afb6a11a61c8b765_53.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_55.png)

# 当短期均线大于长期均线时，标记为1；否则标记为-1
df[‘position’] = np.where(df[‘M1’] > df[‘M2’]， 1， -1)

![](img/4e5d96c01714cb65afb6a11a61c8b765_57.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_59.png)

# 查看信号数据
print(df[[‘position’]].head())
```

![](img/4e5d96c01714cb65afb6a11a61c8b765_61.png)

为了在图表中清晰展示这个只取值为1或-1的信号，我们需要使用双Y轴进行绘图。

以下是使用双Y轴绘图的代码：

![](img/4e5d96c01714cb65afb6a11a61c8b765_63.png)

```python
fig， ax1 = plt.subplots(figsize=(12， 6))

![](img/4e5d96c01714cb65afb6a11a61c8b765_65.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_67.png)

# 第一个Y轴：绘制股价和移动平均线
ax1.plot(df[‘AAPL’]， label=‘AAPL Price’， color=‘blue’， alpha=0.5)
ax1.plot(df[‘M1’]， label=‘Short MA (10)’， color=‘green’)
ax1.plot(df[‘M2’]， label=‘Long MA (30)’， color=‘red’)
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

![](img/4e5d96c01714cb65afb6a11a61c8b765_69.png)

# 第二个Y轴：绘制信号指标
ax2 = ax1.twinx()
ax2.plot(df[‘position’]， label=‘Signal (1/-1)’， color=‘purple’， linestyle=‘--’)
ax2.set_ylabel(‘Signal’)
ax2.legend(loc=‘upper right’)

plt.title(‘AAPL Stock Price with Moving Averages and Trading Signal’)
plt.show()
```

在最终的图表中：
*   紫色虚线（对应右侧Y轴）在 **+1** 区域时，表示短期均线高于长期均线（潜在看涨期）。
*   紫色虚线在 **-1** 区域时，表示短期均线低于长期均线（潜在看跌期）。
*   信号线从 **-1 上穿到 +1** 的点，对应图表中的**黄金交叉**。
*   信号线从 **+1 下穿到 -1** 的点，对应图表中的**死亡交叉**。

![](img/4e5d96c01714cb65afb6a11a61c8b765_71.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_73.png)

## 总结

![](img/4e5d96c01714cb65afb6a11a61c8b765_75.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_77.png)

本节课中我们一起学习了如何利用移动平均线进行简单的股票技术分析。我们掌握了：
1.  使用 `DataFrame.rolling().mean()` **公式**计算短期和长期移动平均线。
2.  通过可视化图表，识别**黄金交叉**（买入信号）和**死亡交叉**（卖出信号）。
3.  使用 `np.where` **函数**创建交易信号指标，并利用双Y轴在同一张图上清晰展示价格走势与交易信号。

![](img/4e5d96c01714cb65afb6a11a61c8b765_79.png)

![](img/4e5d96c01714cb65afb6a11a61c8b765_81.png)

这是一个基础但广泛使用的分析框架。在实际应用中，可以调整窗口参数、结合其他指标，并加入回测机制来验证策略的有效性。