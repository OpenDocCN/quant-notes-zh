# 量化交易教程：P9：短均与长均计算实例 📈

![](img/f8b55c98ecbbdd546e3c11226d32a698_1.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来识别“黄金交叉”与“死亡交叉”这两种常见的交易信号。我们将使用Python的Pandas库进行数据处理和计算，并通过Matplotlib进行可视化展示。

![](img/f8b55c98ecbbdd546e3c11226d32a698_3.png)

## 概述

![](img/f8b55c98ecbbdd546e3c11226d32a698_5.png)

移动平均线是技术分析中常用的指标，用于平滑价格数据并识别趋势。当短期移动平均线从下方上穿长期移动平均线时，形成“黄金交叉”，通常被视为买入信号。反之，当短期移动平均线从上方下穿长期移动平均线时，形成“死亡交叉”，通常被视为卖出信号。本节我们将通过一个实例来计算并可视化这些交叉点。

![](img/f8b55c98ecbbdd546e3c11226d32a698_7.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_9.png)

---

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何具体计算移动平均线并识别交易信号。

## 计算短期与长期移动平均线

![](img/f8b55c98ecbbdd546e3c11226d32a698_11.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_13.png)

首先，我们需要计算两个指标：短期移动平均线和长期移动平均线。通常，短期窗口可以设为5天（代表一个交易周），长期窗口可以设为20天或30天（代表一个月左右的交易日）。由于我们的示例数据时间跨度较长，为了更清晰地展示交叉点，我们将适当增大窗口值。

![](img/f8b55c98ecbbdd546e3c11226d32a698_15.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_17.png)

以下是计算步骤的核心代码：

![](img/f8b55c98ecbbdd546e3c11226d32a698_19.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_21.png)

```python
# 假设 df 是包含苹果股票价格的数据框，列名为 ‘AAPL’
# 计算短期移动平均线（例如10天窗口）
df[‘M1’] = df[‘AAPL’].rolling(window=10).mean()

![](img/f8b55c98ecbbdd546e3c11226d32a698_23.png)

# 计算长期移动平均线（例如30天窗口）
df[‘M2’] = df[‘AAPL’].rolling(window=30).mean()
```

![](img/f8b55c98ecbbdd546e3c11226d32a698_25.png)

在这段代码中：
*   `df[‘AAPL’]` 代表苹果股票的价格序列。
*   `.rolling(window=10)` 创建了一个大小为10的滚动窗口。
*   `.mean()` 计算了该窗口内数据的平均值，即移动平均线。

![](img/f8b55c98ecbbdd546e3c11226d32a698_27.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_29.png)

## 可视化价格与移动平均线

![](img/f8b55c98ecbbdd546e3c11226d32a698_31.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_33.png)

计算完成后，我们可以将股票价格、短期均线和长期均线绘制在同一张图上进行观察。

以下是绘图的核心代码：

```python
import matplotlib.pyplot as plt

![](img/f8b55c98ecbbdd546e3c11226d32a698_35.png)

# 绘制图形
plt.figure(figsize=(12, 6))
plt.plot(df[‘AAPL’], label=‘Apple Stock Price’, alpha=0.5)
plt.plot(df[‘M1’], label=‘Short-term MA (10-day)’, color=‘green’)
plt.plot(df[‘M2’], label=‘Long-term MA (30-day)’, color=‘red’)
plt.legend()
plt.show()
```

![](img/f8b55c98ecbbdd546e3c11226d32a698_37.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_39.png)

通过观察图表，我们可以手动寻找交叉点：
*   **死亡交叉**：当绿色短期均线下穿红色长期均线时，预示下跌趋势，应考虑卖出。
*   **黄金交叉**：当绿色短期均线上穿红色长期均线时，预示上涨趋势，可考虑买入。

![](img/f8b55c98ecbbdd546e3c11226d32a698_41.png)

---

![](img/f8b55c98ecbbdd546e3c11226d32a698_43.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_45.png)

上一节我们绘制了基本的均线图，本节中我们来看看如何通过程序更精确地标识出交叉点。

## 标识黄金交叉与死亡交叉

![](img/f8b55c98ecbbdd546e3c11226d32a698_47.png)

为了更直观地展示交叉信号，我们可以创建一个新的指标 `Position`。当短期均线大于长期均线时，标记为 `1`；当短期均线小于长期均线时，标记为 `-1`。这个指标的转折点就对应着交叉信号。

![](img/f8b55c98ecbbdd546e3c11226d32a698_49.png)

以下是计算和绘制 `Position` 指标的核心代码：

![](img/f8b55c98ecbbdd546e3c11226d32a698_51.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_53.png)

```python
import numpy as np

![](img/f8b55c98ecbbdd546e3c11226d32a698_55.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_57.png)

# 计算位置信号：短期均线 > 长期均线时为1，否则为-1
df[‘Position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)

![](img/f8b55c98ecbbdd546e3c11226d32a698_59.png)

# 创建带有双Y轴的图形
fig, ax1 = plt.subplots(figsize=(12, 6))

![](img/f8b55c98ecbbdd546e3c11226d32a698_61.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_63.png)

# 第一个Y轴：绘制价格和均线
ax1.plot(df[‘AAPL’], label=‘Price’, color=‘blue’, alpha=0.5)
ax1.plot(df[‘M1’], label=‘MA Short’, color=‘green’)
ax1.plot(df[‘M2’], label=‘MA Long’, color=‘red’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

# 第二个Y轴：绘制位置信号
ax2 = ax1.twinx()
ax2.plot(df[‘Position’], label=‘Signal’, color=‘purple’, linestyle=‘--’)
ax2.set_ylabel(‘Signal (1/-1)’)
ax2.set_yticks([-1, 1])
ax2.legend(loc=‘upper right’)

plt.show()
```

![](img/f8b55c98ecbbdd546e3c11226d32a698_65.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_67.png)

在这张图中：
*   紫色虚线在 `1` 和 `-1` 之间切换。
*   当信号从 `-1` 变为 `1` 时，对应**黄金交叉**（买入信号）。
*   当信号从 `1` 变为 `-1` 时，对应**死亡交叉**（卖出信号）。

![](img/f8b55c98ecbbdd546e3c11226d32a698_69.png)

---

## 总结

![](img/f8b55c98ecbbdd546e3c11226d32a698_71.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_73.png)

本节课中我们一起学习了移动平均线在量化交易中的应用。我们掌握了以下核心技能：

![](img/f8b55c98ecbbdd546e3c11226d32a698_75.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_77.png)

1.  **计算移动平均线**：使用Pandas的 `.rolling().mean()` 方法计算指定窗口的短期和长期移动平均线。
2.  **识别交易信号**：理解了“黄金交叉”（短期均线上穿长期均线，买入信号）和“死亡交叉”（短期均线下穿长期均线，卖出信号）的概念。
3.  **数据可视化**：使用Matplotlib绘制股票价格和移动平均线，并通过创建双Y轴图表来清晰展示交叉信号。

![](img/f8b55c98ecbbdd546e3c11226d32a698_79.png)

![](img/f8b55c98ecbbdd546e3c11226d32a698_81.png)

通过这个实例，我们看到了如何将基础的金融概念转化为具体的代码和分析图表，这是构建更复杂量化交易策略的重要一步。