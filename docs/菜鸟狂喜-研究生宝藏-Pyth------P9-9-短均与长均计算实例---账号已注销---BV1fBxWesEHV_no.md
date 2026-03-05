# Python金融量化分析：P9：短均与长均计算实例 📈

![](img/b9d86bfa1737a0b0ca12dffee106410a_1.png)

在本节课中，我们将学习一个基础的股票序列分析策略。我们将通过计算短期移动平均线和长期移动平均线，来识别市场中的“黄金交叉”和“死亡交叉”信号，从而辅助判断买入和卖出的时机。

![](img/b9d86bfa1737a0b0ca12dffee106410a_3.png)

## 策略概述

![](img/b9d86bfa1737a0b0ca12dffee106410a_5.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_7.png)

上一节我们介绍了数据获取与处理的基础知识。本节中，我们来看看如何应用移动平均线进行简单的趋势分析。该策略的核心是计算两个不同时间窗口的股价平均值，并通过观察它们的交叉点来产生交易信号。

![](img/b9d86bfa1737a0b0ca12dffee106410a_9.png)

## 核心概念与计算

![](img/b9d86bfa1737a0b0ca12dffee106410a_11.png)

移动平均线（Moving Average， MA）是技术分析中常用的指标，用于平滑股价数据，识别趋势方向。其基本公式为：

**MA(n) = (P1 + P2 + ... + Pn) / n**

其中，`P` 代表每日收盘价，`n` 代表计算平均值的窗口天数。

![](img/b9d86bfa1737a0b0ca12dffee106410a_13.png)

我们将计算两个移动平均线：
*   **短期移动平均线（Short-term MA）**：通常使用较短的时间窗口（如5天、10天），对价格变化更敏感。
*   **长期移动平均线（Long-term MA）**：通常使用较长的时间窗口（如20天、30天），反映更长期的价格趋势。

![](img/b9d86bfa1737a0b0ca12dffee106410a_15.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_17.png)

## 实战步骤

![](img/b9d86bfa1737a0b0ca12dffee106410a_19.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_21.png)

以下是使用Python和pandas库计算并绘制移动平均线的具体步骤。

![](img/b9d86bfa1737a0b0ca12dffee106410a_23.png)

### 1. 数据准备

![](img/b9d86bfa1737a0b0ca12dffee106410a_25.png)

首先，我们需要选定分析的股票数据。这里我们以苹果公司（AAPL）的历史股价数据为例。

![](img/b9d86bfa1737a0b0ca12dffee106410a_27.png)

```python
# 假设我们已经有一个包含苹果股价的DataFrame，名为 `df`，其中有一列 ‘AAPL‘ 代表收盘价。
# 本节示例将基于此数据进行。
stock_data = df[‘AAPL‘]
```

![](img/b9d86bfa1737a0b0ca12dffee106410a_29.png)

### 2. 计算短期与长期移动平均线

![](img/b9d86bfa1737a0b0ca12dffee106410a_31.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_33.png)

接下来，我们分别计算10天（短期）和30天（长期）的简单移动平均线。

```python
# 计算短期移动平均线（10天窗口）
short_window = 10
df[‘M1‘] = stock_data.rolling(window=short_window).mean()

# 计算长期移动平均线（30天窗口）
long_window = 30
df[‘M2‘] = stock_data.rolling(window=long_window).mean()
```

![](img/b9d86bfa1737a0b0ca12dffee106410a_35.png)

代码解释：
*   `.rolling(window=n)`：创建一个滚动窗口对象，窗口大小为 `n`。
*   `.mean()`：对窗口内的数据计算算术平均值。

![](img/b9d86bfa1737a0b0ca12dffee106410a_37.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_39.png)

### 3. 可视化移动平均线与股价

计算出指标后，我们可以将它们与原始股价一同绘制在图表上，以便直观观察。

![](img/b9d86bfa1737a0b0ca12dffee106410a_41.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_43.png)

```python
import matplotlib.pyplot as plt

![](img/b9d86bfa1737a0b0ca12dffee106410a_45.png)

# 绘制图表
plt.figure(figsize=(12, 6))
plt.plot(df.index, df[‘AAPL‘], label=‘Apple Stock Price‘, alpha=0.5)
plt.plot(df.index, df[‘M1‘], label=‘10-Day MA (Short)‘, color=‘green‘)
plt.plot(df.index, df[‘M2‘], label=‘30-Day MA (Long)‘, color=‘red‘)

plt.title(‘Apple Stock Price with Moving Averages‘)
plt.xlabel(‘Date‘)
plt.ylabel(‘Price‘)
plt.legend()
plt.show()
```

![](img/b9d86bfa1737a0b0ca12dffee106410a_47.png)

由于原始数据时间跨度较长，图表可能显得拥挤。我们可以截取其中一段时间（例如一年）的数据进行绘图，这样交叉信号会更清晰。

![](img/b9d86bfa1737a0b0ca12dffee106410a_49.png)

### 4. 识别交叉信号

![](img/b9d86bfa1737a0b0ca12dffee106410a_51.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_53.png)

在图表上，我们可以手动识别两种重要的交叉信号：

![](img/b9d86bfa1737a0b0ca12dffee106410a_55.png)

*   **黄金交叉（Golden Cross）**：当短期移动平均线从下方**上穿**长期移动平均线时，通常被视为**买入信号**，预示着可能开启上涨趋势。
*   **死亡交叉（Death Cross）**：当短期移动平均线从上方**下穿**长期移动平均线时，通常被视为**卖出信号**，预示着可能开启下跌趋势。

![](img/b9d86bfa1737a0b0ca12dffee106410a_57.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_59.png)

观察生成的图表，找到短期均线（绿线）与长期均线（红线）发生交叉的位置，并对照上述定义进行判断。

![](img/b9d86bfa1737a0b0ca12dffee106410a_61.png)

### 5. 使用数值方法标记交叉区域

![](img/b9d86bfa1737a0b0ca12dffee106410a_63.png)

为了让信号更清晰，我们可以用数值方法自动标记出短期均线高于或低于长期均线的区域。

```python
import numpy as np

![](img/b9d86bfa1737a0b0ca12dffee106410a_65.png)

# 生成位置信号：短期均线大于长期均线时为1，否则为-1
df[‘position‘] = np.where(df[‘M1‘] > df[‘M2‘], 1, -1)

![](img/b9d86bfa1737a0b0ca12dffee106410a_67.png)

# 创建带有双Y轴的图表进行可视化
fig, ax1 = plt.subplots(figsize=(12, 6))

![](img/b9d86bfa1737a0b0ca12dffee106410a_69.png)

# 第一个Y轴：绘制股价和移动平均线
ax1.plot(df.index, df[‘AAPL‘], label=‘Price‘, color=‘blue‘, alpha=0.3)
ax1.plot(df.index, df[‘M1‘], label=‘10-Day MA‘, color=‘green‘)
ax1.plot(df.index, df[‘M2‘], label=‘30-Day MA‘, color=‘red‘)
ax1.set_xlabel(‘Date‘)
ax1.set_ylabel(‘Price‘)
ax1.legend(loc=‘upper left‘)

# 第二个Y轴：绘制位置信号
ax2 = ax1.twinx()
ax2.plot(df.index, df[‘position‘], label=‘Position Signal‘, color=‘purple‘, linestyle=‘--‘)
ax2.set_ylabel(‘Signal (1/-1)‘)
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc=‘upper right‘)

![](img/b9d86bfa1737a0b0ca12dffee106410a_71.png)

plt.title(‘Stock Price with MAs and Position Signal‘)
plt.show()
```

![](img/b9d86bfa1737a0b0ca12dffee106410a_73.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_75.png)

代码解释：
*   `np.where(condition, x, y)`：是NumPy的条件赋值函数，满足条件时输出`x`，否则输出`y`。
*   当 `position` 信号线从 **-1 上升至 1** 时，对应图表中的**黄金交叉**点。
*   当 `position` 信号线从 **1 下降至 -1** 时，对应图表中的**死亡交叉**点。

![](img/b9d86bfa1737a0b0ca12dffee106410a_77.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_79.png)

## 总结

![](img/b9d86bfa1737a0b0ca12dffee106410a_81.png)

![](img/b9d86bfa1737a0b0ca12dffee106410a_83.png)

本节课中我们一起学习了移动平均线交叉策略的基础应用。我们掌握了如何使用pandas计算短期和长期移动平均线，并通过matplotlib将结果可视化。核心是理解了**黄金交叉**和**死亡交叉**这两个技术分析中的常见概念，它们分别提供了潜在的买入和卖出参考信号。通过数值化标记交叉区域，我们可以更直观地观察趋势的强弱转换。这是一个入门级的趋势跟踪策略，在实际应用中需要结合其他指标和市场情况综合判断。