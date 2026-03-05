# Python金融分析与量化交易实战教程：P6：5-短均与长均计算实例 📈

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_1.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的技术分析策略。我们将识别“黄金交叉”和“死亡交叉”这两种关键信号，并使用Python代码进行可视化展示。

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_3.png)

## 概述

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_5.png)

我们将分析股票序列，计算短期和长期移动平均线。通过观察这两条线的交叉点，可以判断市场的买入和卖出时机。核心概念是“黄金交叉”（短期均线上穿长期均线，看涨信号）和“死亡交叉”（短期均线下穿长期均线，看跌信号）。

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_7.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_9.png)

## 计算短期与长期移动平均线

首先，我们需要计算两个关键指标：短期移动平均线和长期移动平均线。移动平均线是过去一段时间内股价的平均值，能平滑价格波动，反映趋势。

以下是计算步骤：

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_11.png)

1.  **选择股票数据**：我们以苹果公司（AAPL）的股价数据为例。
2.  **定义窗口期**：
    *   短期窗口通常较小，例如5天（一周的交易日）或10天。
    *   长期窗口通常较大，例如20天或30天（约一个月）。
    *   由于我们的数据集时间跨度较长（2010-2018年），为了在图表中更清晰地展示交叉点，我们将使用稍大的窗口：短期为10天，长期为30天。
3.  **执行计算**：使用Pandas的`.rolling()`和`.mean()`方法计算移动平均值。

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_13.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_15.png)

以下是实现代码：

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_17.png)

```python
# 假设 df 是包含苹果股价的DataFrame，其中‘AAPL’是股价列
# 计算短期移动平均线 (10天窗口)
df[‘M1’] = df[‘AAPL’].rolling(window=10).mean()

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_19.png)

# 计算长期移动平均线 (30天窗口)
df[‘M2’] = df[‘AAPL’].rolling(window=30).mean()
```

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_21.png)

代码解释：
*   `df[‘AAPL’].rolling(window=10)` 创建了一个大小为10的滚动窗口。
*   `.mean()` 计算了每个窗口内数据的平均值，从而得到移动平均线。

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_23.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_25.png)

## 可视化股价与移动平均线

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_27.png)

计算完成后，我们将股价和两条移动平均线绘制在同一张图上进行观察。

以下是绘图代码：

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_29.png)

```python
import matplotlib.pyplot as plt

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_31.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_33.png)

# 绘制图表
plt.figure(figsize=(12, 6))
plt.plot(df[‘AAPL’], label=‘Apple Stock Price’, alpha=0.5)
plt.plot(df[‘M1’], label=‘10-Day MA (Short Term)’, color=‘green’)
plt.plot(df[‘M2’], label=‘30-Day MA (Long Term)’, color=‘red’)
plt.title(‘Apple Stock Price with Moving Averages’)
plt.xlabel(‘Date’)
plt.ylabel(‘Price’)
plt.legend()
plt.show()
```

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_35.png)

通过观察图表，我们可以寻找交叉点：
*   **死亡交叉**：当绿色的短期均线（M1）从上方向下穿过红色的长期均线（M2）时，通常被视为下跌信号，此时应考虑卖出或避免买入。
*   **黄金交叉**：当绿色的短期均线（M1）从下方向上穿过红色的长期均线（M2）时，通常被视为上涨信号，此时可能是一个买入机会。

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_37.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_39.png)

## 量化交叉信号

上一节我们通过看图识别了交叉点，本节中我们来看看如何用代码自动标记这些信号，使分析更精确。

我们可以创建一个新的列来明确标记当前是“黄金交叉”区域（短期均线 > 长期均线）还是“死亡交叉”区域（短期均线 < 长期均线）。这里使用`np.where`函数。

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_41.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_43.png)

```python
import numpy as np

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_45.png)

# 生成信号列：短期均线大于长期均线时标记为1，否则标记为-1
df[‘position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
```

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_47.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_49.png)

代码解释：
*   `np.where(condition, x, y)` 是一个条件赋值函数。
*   当条件 `df[‘M1’] > df[‘M2’]` 为真时，`position` 列赋值为 `1`（代表看涨区域）。
*   当条件为假时，赋值为 `-1`（代表看跌区域）。

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_51.png)

## 可视化量化信号

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_53.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_55.png)

为了更直观地展示，我们可以在股价主图的基础上，用右侧的Y轴来绘制这个信号线。

以下是改进后的绘图代码：

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_57.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_59.png)

# 左侧Y轴：绘制股价和移动平均线
ax1.plot(df[‘AAPL’], label=‘Stock Price’, color=‘blue’, alpha=0.3)
ax1.plot(df[‘M1’], label=‘10-Day MA’, color=‘green’)
ax1.plot(df[‘M2’], label=‘30-Day MA’, color=‘red’)
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_61.png)

# 创建右侧Y轴，绘制信号线
ax2 = ax1.twinx()
ax2.plot(df[‘position’], label=‘Signal (1=Golden, -1=Death)’, color=‘purple’, linestyle=‘--’)
ax2.set_ylabel(‘Signal’)
ax2.set_ylim([-1.5, 1.5]) # 限制信号轴范围，使图形更清晰
ax2.legend(loc=‘upper right’)

plt.title(‘Stock Price with Moving Averages and Crossover Signals’)
plt.show()
```

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_63.png)

图表解读：
*   紫色虚线（参考右侧Y轴）在 **+1** 水平时，表示处于“黄金交叉”后的看涨区域。
*   紫色虚线在 **-1** 水平时，表示处于“死亡交叉”后的看跌区域。
*   信号线从 **-1** 上穿到 **+1** 的转折点，对应图表中的“黄金交叉”。
*   信号线从 **+1** 下穿到 **-1** 的转折点，对应图表中的“死亡交叉”。

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_65.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_67.png)

## 总结

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_69.png)

![](img/4edceb241edbb96d2cf1c36fe1fea3d0_71.png)

本节课中我们一起学习了移动平均线交叉策略的核心实践。我们首先计算了股票的短期（10日）和长期（30日）移动平均线。然后，通过图表直观地识别了预示趋势变化的“黄金交叉”和“死亡交叉”。最后，我们使用`np.where`函数量化了交叉信号，并通过双Y轴图表将股价、均线以及买卖信号区域清晰地展示出来。这个方法为后续构建自动化交易策略奠定了基础。