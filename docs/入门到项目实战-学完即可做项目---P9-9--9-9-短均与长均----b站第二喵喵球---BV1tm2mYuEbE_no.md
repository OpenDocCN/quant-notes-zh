# 量化交易实战：P9：短均与长均计算实例 📈

在本节课中，我们将学习一个基础的股票序列分析策略。我们将通过计算短期和长期移动平均线，并识别它们之间的“黄金交叉”与“死亡交叉”，来初步判断股票的买卖时机。

![](img/45de05e8e0834e54bb969b171e923dd4_1.png)

上一节我们介绍了如何获取和处理金融数据，本节中我们来看看如何利用这些数据计算技术指标并进行可视化分析。

![](img/45de05e8e0834e54bb969b171e923dd4_3.png)

## 策略核心：移动平均线交叉

![](img/45de05e8e0834e54bb969b171e923dd4_5.png)

该策略的核心是计算两条移动平均线：一条短期均线（如5日、10日），一条长期均线（如20日、30日）。通过观察它们的交叉点来产生交易信号：
*   **黄金交叉**：短期均线从下方向上穿越长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上方向下穿越长期均线，通常被视为**卖出信号**。

![](img/45de05e8e0834e54bb969b171e923dd4_7.png)

## 计算短期与长期移动平均线

![](img/45de05e8e0834e54bb969b171e923dd4_9.png)

以下是计算移动平均线的具体步骤。我们将以苹果公司（AAPL）的股价数据为例。

首先，我们需要导入必要的库并加载数据（假设数据已加载到变量 `df` 中）。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 假设苹果公司的股价数据已加载到 df 中，其中 ‘AAPL’ 是收盘价列
# df = pd.read_csv(‘your_stock_data.csv’)
```

接下来，我们开始计算移动平均线。

![](img/45de05e8e0834e54bb969b171e923dd4_11.png)

![](img/45de05e8e0834e54bb969b171e923dd4_13.png)

### 1. 计算短期移动平均线

![](img/45de05e8e0834e54bb969b171e923dd4_15.png)

![](img/45de05e8e0834e54bb969b171e923dd4_17.png)

短期均线通常反映近期价格趋势。我们以10个交易日为窗口进行计算。

```python
# 计算10日移动平均线（短期均线）
short_window = 10
df[‘M1_short’] = df[‘AAPL’].rolling(window=short_window).mean()
```

![](img/45de05e8e0834e54bb969b171e923dd4_19.png)

**代码解释**：
*   `df[‘AAPL’].rolling(window=short_window)`：对 ‘AAPL’ 列创建一个滚动窗口对象，窗口大小为 `short_window`（此处为10）。
*   `.mean()`：计算每个窗口内数据的**平均值**，即移动平均线。

![](img/45de05e8e0834e54bb969b171e923dd4_21.png)

### 2. 计算长期移动平均线

![](img/45de05e8e0834e54bb969b171e923dd4_23.png)

长期均线用于反映更长期的价格趋势。我们以30个交易日为窗口进行计算。

![](img/45de05e8e0834e54bb969b171e923dd4_25.png)

![](img/45de05e8e0834e54bb969b171e923dd4_27.png)

```python
# 计算30日移动平均线（长期均线）
long_window = 30
df[‘M2_long’] = df[‘AAPL’].rolling(window=long_window).mean()
```

计算完成后，我们可以查看包含原始价格和两条均线的数据。

```python
# 查看包含计算结果的列
print(df[[‘AAPL’， ‘M1_short’， ‘M2_long’]].head())
```

## 可视化股价与移动平均线

![](img/45de05e8e0834e54bb969b171e923dd4_29.png)

计算出指标后，最直观的方式是将其绘制在图表上进行观察。

![](img/45de05e8e0834e54bb969b171e923dd4_31.png)

以下是绘制股价与两条移动平均线的代码：

![](img/45de05e8e0834e54bb969b171e923dd4_33.png)

```python
# 绘制股价与移动平均线
plt.figure(figsize=(12， 6))
plt.plot(df.index， df[‘AAPL’]， label=‘AAPL Price’， alpha=0.5)
plt.plot(df.index， df[‘M1_short’]， label=f‘{short_window}-Day MA’， color=‘green’)
plt.plot(df.index， df[‘M2_long’]， label=f‘{long_window}-Day MA’， color=‘red’)
plt.title(‘AAPL Stock Price with Moving Averages’)
plt.xlabel(‘Date’)
plt.ylabel(‘Price’)
plt.legend()
plt.grid(True)
plt.show()
```

![](img/45de05e8e0834e54bb969b171e923dd4_35.png)

**图表分析**：
在生成的图表中，我们可以寻找交叉点：
*   当**绿色**的短期均线（M1）从下方上穿**红色**的长期均线（M2）时，形成**黄金交叉**，可能预示上涨趋势开始。
*   当**绿色**的短期均线（M1）从上方下穿**红色**的长期均线（M2）时，形成**死亡交叉**，可能预示下跌趋势开始。

![](img/45de05e8e0834e54bb969b171e923dd4_37.png)

![](img/45de05e8e0834e54bb969b171e923dd4_39.png)

## 量化交叉信号

为了更精确地定位交叉点，我们可以创建一个信号列来量化短期均线与长期均线的关系。

以下是使用 `np.where` 函数创建信号列的步骤：

![](img/45de05e8e0834e54bb969b171e923dd4_41.png)

```python
# 创建信号列：短期均线 > 长期均线时为1，否则为-1
df[‘position’] = np.where(df[‘M1_short’] > df[‘M2_long’]， 1， -1)

![](img/45de05e8e0834e54bb969b171e923dd4_43.png)

# 查看信号列
print(df[[‘position’]].head())
```

![](img/45de05e8e0834e54bb969b171e923dd4_45.png)

**信号解释**：
*   `position = 1`：表示短期均线位于长期均线**之上**，处于“看涨”区域。
*   `position = -1`：表示短期均线位于长期均线**之下**，处于“看跌”区域。
*   当 `position` 的值从 **-1 变为 1** 时，对应**黄金交叉**。
*   当 `position` 的值从 **1 变为 -1** 时，对应**死亡交叉**。

![](img/45de05e8e0834e54bb969b171e923dd4_47.png)

## 可视化交叉信号

![](img/45de05e8e0834e54bb969b171e923dd4_49.png)

为了更清晰地观察信号变化，我们可以将信号线绘制在股价图表的次坐标轴上。

![](img/45de05e8e0834e54bb969b171e923dd4_51.png)

以下是绘制带信号线的复合图表代码：

![](img/45de05e8e0834e54bb969b171e923dd4_53.png)

![](img/45de05e8e0834e54bb969b171e923dd4_55.png)

```python
# 创建图形和主坐标轴
fig， ax1 = plt.subplots(figsize=(14， 7))

![](img/45de05e8e0834e54bb969b171e923dd4_57.png)

# 在主坐标轴绘制股价和均线
ax1.plot(df.index， df[‘AAPL’]， label=‘AAPL Price’， color=‘blue’， alpha=0.3)
ax1.plot(df.index， df[‘M1_short’]， label=f‘{short_window}-Day MA’， color=‘green’， linewidth=1.5)
ax1.plot(df.index， df[‘M2_long’]， label=f‘{long_window}-Day MA’， color=‘red’， linewidth=1.5)
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’， color=‘black’)
ax1.tick_params(axis=‘y’， labelcolor=‘black’)
ax1.legend(loc=‘upper left’)
ax1.grid(True)

# 创建次坐标轴
ax2 = ax1.twinx()
# 在次坐标轴绘制信号线
ax2.plot(df.index， df[‘position’]， label=‘Signal (1/-1)’， color=‘purple’， linestyle=‘--’， alpha=0.7)
ax2.set_ylabel(‘Signal’， color=‘purple’)
ax2.tick_params(axis=‘y’， labelcolor=‘purple’)
ax2.set_ylim(-1.5， 1.5) # 固定信号轴范围以便观察
ax2.legend(loc=‘upper right’)

plt.title(‘AAPL Price， Moving Averages， and Crossover Signals’)
plt.show()
```

![](img/45de05e8e0834e54bb969b171e923dd4_59.png)

**图表解读**：
*   紫色虚线（信号线）在 **+1** 和 **-1** 之间切换。
*   信号线从 **-1 上穿到 +1** 的瞬间（紫色虚线上升），对应图表中绿色短期均线上穿红色长期均线的点，即**黄金交叉**。
*   信号线从 **+1 下穿到 -1** 的瞬间（紫色虚线下降），对应图表中绿色短期均线下穿红色长期均线的点，即**死亡交叉**。

![](img/45de05e8e0834e54bb969b171e923dd4_61.png)

## 关键参数与注意事项

![](img/45de05e8e0834e54bb969b171e923dd4_63.png)

在实际应用中，有几点需要注意：

以下是移动平均线策略中常见的参数选择：
*   **短期窗口**：常用 **5日**（一周交易日）或 **10日**。对于时间跨度很长的历史数据，可以适当增大窗口（如20日）以使图表更清晰。
*   **长期窗口**：常用 **20日**（约一个月交易日）或 **30日**、**60日**。
*   **计算方法**：本教程使用的是**简单移动平均（SMA）**，即 `rolling().mean()`。这是最基础的形式，你还可以尝试指数移动平均（EMA）等变体。

**核心公式**：
简单移动平均（SMA）的计算公式为：
**SMA = (P1 + P2 + … + Pn) / n**
其中 `P` 代表每个交易日的价格，`n` 代表移动平均的周期（窗口大小）。

![](img/45de05e8e0834e54bb969b171e923dd4_65.png)

## 总结

![](img/45de05e8e0834e54bb969b171e923dd4_67.png)

![](img/45de05e8e0834e54bb969b171e923dd4_69.png)

本节课中我们一起学习了量化交易中的一个基础分析策略——移动平均线交叉策略。

![](img/45de05e8e0834e54bb969b171e923dd4_71.png)

![](img/45de05e8e0834e54bb969b171e923dd4_73.png)

我们首先介绍了**黄金交叉**和**死亡交叉**的概念，然后使用 `pandas` 的 `rolling().mean()` 方法计算了**短期**和**长期**移动平均线。接着，我们通过 `matplotlib` 将股价与均线可视化，直观地识别交叉点。为了更精确地量化信号，我们使用 `np.where` 创建了**信号列**，并最终在一个复合图表中同时展示了价格走势和买卖信号。

![](img/45de05e8e0834e54bb969b171e923dd4_75.png)

这个实例展示了如何将金融概念转化为可计算的指标和可视化的图表，是构建更复杂量化模型的重要基础。记住，移动平均线策略是一个滞后指标，在实际应用中需要结合其他分析方法和风险管理策略。