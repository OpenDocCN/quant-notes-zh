# Python金融量化与股票交易：P9：9.9.3-短均与长均计算实例 📈

![](img/9ad99ddc0e89939c6f377ea786e373cc_1.png)

在本节课中，我们将学习一个基础的股票序列分析策略。我们将通过计算短期和长期移动平均线，来识别市场中的“黄金交叉”与“死亡交叉”信号，从而辅助判断买入和卖出的时机。

![](img/9ad99ddc0e89939c6f377ea786e373cc_3.png)

## 策略概述与核心概念

![](img/9ad99ddc0e89939c6f377ea786e373cc_5.png)

上一节我们介绍了数据分析的基础操作，本节中我们来看看如何应用这些操作进行技术指标分析。这个策略的核心是计算两个移动平均线：短期移动平均线（Short-term Moving Average）和长期移动平均线（Long-term Moving Average）。

![](img/9ad99ddc0e89939c6f377ea786e373cc_7.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_9.png)

*   **黄金交叉**：当短期均线从下方上穿长期均线时，通常被视为买入信号。
*   **死亡交叉**：当短期均线从上方下穿长期均线时，通常被视为卖出信号。

我们的目标是计算这两个指标，并将它们可视化，以便清晰地观察交叉点。

## 计算短期与长期移动平均线

![](img/9ad99ddc0e89939c6f377ea786e373cc_11.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_13.png)

首先，我们需要选定分析的股票数据。这里我们使用苹果公司（AAPL）的历史股价数据作为示例。

![](img/9ad99ddc0e89939c6f377ea786e373cc_15.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_17.png)

计算移动平均线在Pandas中非常简便，主要使用`.rolling()`方法配合`.mean()`函数。

![](img/9ad99ddc0e89939c6f377ea786e373cc_19.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_21.png)

以下是计算步骤：

1.  **计算短期移动平均线**：我们选择一个较小的窗口，例如10天（代表约两周的交易周期）。
    ```python
    # 假设 df 是包含苹果股价的DataFrame，其中 ‘AAPL’ 是股价列
    short_window = 10
    df[‘M1’] = df[‘AAPL’].rolling(window=short_window).mean()
    ```

![](img/9ad99ddc0e89939c6f377ea786e373cc_23.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_25.png)

2.  **计算长期移动平均线**：我们选择一个较大的窗口，例如30天（代表约一个半月的交易周期）。
    ```python
    long_window = 30
    df[‘M2’] = df[‘AAPL’].rolling(window=long_window).mean()
    ```

![](img/9ad99ddc0e89939c6f377ea786e373cc_27.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_29.png)

**注意**：在实际应用中，窗口大小（如5日、20日）可根据分析周期调整。由于示例数据时间跨度较长，我们使用了稍大的窗口以使图表更清晰。

![](img/9ad99ddc0e89939c6f377ea786e373cc_31.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_33.png)

## 可视化移动平均线与股价

计算完成后，我们将股价、短期均线和长期均线绘制在同一张图上进行观察。

```python
import matplotlib.pyplot as plt

![](img/9ad99ddc0e89939c6f377ea786e373cc_35.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_37.png)

# 绘制图表
plt.figure(figsize=(12, 6))
plt.plot(df[‘AAPL’], label=‘AAPL Price’, alpha=0.5)
plt.plot(df[‘M1’], label=‘10-Day MA’, color=‘green’)
plt.plot(df[‘M2’], label=‘30-Day MA’, color=‘red’)
plt.legend()
plt.show()
```

![](img/9ad99ddc0e89939c6f377ea786e373cc_39.png)

在生成的图表中：
*   蓝色（或原色）线代表苹果公司的实际股价。
*   绿色线代表**10日短期移动平均线（M1）**。
*   红色线代表**30日长期移动平均线（M2）**。

![](img/9ad99ddc0e89939c6f377ea786e373cc_41.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_43.png)

观察图表，我们可以手动寻找交叉点：
*   当绿色线（短期）从下方上穿红色线（长期）时，形成**黄金交叉**，可能预示上涨趋势。
*   当绿色线（短期）从上方下穿红色线（长期）时，形成**死亡交叉**，可能预示下跌趋势。

![](img/9ad99ddc0e89939c6f377ea786e373cc_45.png)

## 量化交叉信号

手动观察交叉点不够精确，我们可以利用NumPy来量化这个信号，生成明确的交易位置指标。

![](img/9ad99ddc0e89939c6f377ea786e373cc_47.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_49.png)

我们创建一个新列 `Position`，其规则是：
*   当短期均线 **大于** 长期均线时，标记为 **1**（代表持有或买入信号）。
*   当短期均线 **小于** 长期均线时，标记为 **-1**（代表卖出或空仓信号）。

```python
import numpy as np

![](img/9ad99ddc0e89939c6f377ea786e373cc_51.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_53.png)

df[‘Position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
```

![](img/9ad99ddc0e89939c6f377ea786e373cc_55.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_57.png)

为了更直观地展示这个信号，我们可以在股价图的右侧添加一个Y轴来专门绘制 `Position` 信号线。

![](img/9ad99ddc0e89939c6f377ea786e373cc_59.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_61.png)

```python
fig, ax1 = plt.subplots(figsize=(12, 6))

![](img/9ad99ddc0e89939c6f377ea786e373cc_63.png)

# 左侧Y轴：绘制股价和均线
ax1.plot(df[‘AAPL’], label=‘Price’, color=‘blue’, alpha=0.3)
ax1.plot(df[‘M1’], label=‘10-Day MA’, color=‘green’)
ax1.plot(df[‘M2’], label=‘30-Day MA’, color=‘red’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

# 右侧Y轴：绘制交易信号
ax2 = ax1.twinx()
ax2.plot(df[‘Position’], label=‘Trading Signal’, color=‘orange’, linestyle=‘--’)
ax2.set_ylabel(‘Signal (1/-1)’)
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc=‘upper right’)

![](img/9ad99ddc0e89939c6f377ea786e373cc_65.png)

plt.show()
```

![](img/9ad99ddc0e89939c6f377ea786e373cc_67.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_69.png)

在这张双轴图中：
*   左侧主图展示了股价与均线的走势。
*   右侧的橙色虚线展示了交易信号 `Position`。
*   当信号线从 **-1** 跃升到 **1** 时，对应图表中的**黄金交叉**点。
*   当信号线从 **1** 下降到 **-1** 时，对应图表中的**死亡交叉**点。

这使得买卖信号的识别变得一目了然。

![](img/9ad99ddc0e89939c6f377ea786e373cc_71.png)

## 总结

![](img/9ad99ddc0e89939c6f377ea786e373cc_73.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_75.png)

本节课中我们一起学习了如何利用Python进行简单的量化分析。我们掌握了以下内容：

![](img/9ad99ddc0e89939c6f377ea786e373cc_77.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_79.png)

1.  **概念理解**：理解了**黄金交叉**和**死亡交叉**这两个常见的技术分析概念。
2.  **指标计算**：使用Pandas的 `.rolling().mean()` 方法计算了**短期**和**长期移动平均线**。
3.  **数据可视化**：通过Matplotlib将股价与均线绘制在同一图表中，进行直观分析。
4.  **信号量化**：利用NumPy的 `np.where` 函数将均线交叉关系量化为明确的交易信号（1或-1），并通过双轴图进行清晰展示。

![](img/9ad99ddc0e89939c6f377ea786e373cc_81.png)

![](img/9ad99ddc0e89939c6f377ea786e373cc_83.png)

这个实例展示了如何将基础的金融概念转化为可执行的代码，是构建更复杂量化交易策略的重要基石。通过计算和可视化移动平均线，我们可以开始让程序辅助我们识别潜在的市场趋势转折点。