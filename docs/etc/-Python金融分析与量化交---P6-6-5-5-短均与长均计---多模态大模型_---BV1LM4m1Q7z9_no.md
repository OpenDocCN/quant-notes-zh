# Python金融分析与量化交易实战：6.5.5：短均与长均计算实例P5 📈

![](img/d6228f22295783d290cb105ed634c3af_1.png)

在本节课中，我们将学习如何计算股票的短期和长期移动平均线，并利用它们识别“黄金交叉”与“死亡交叉”这两种常见的交易信号。我们将通过Python代码实现计算与可视化，让初学者也能清晰地理解这一策略的核心逻辑。

![](img/d6228f22295783d290cb105ed634c3af_3.png)

![](img/d6228f22295783d290cb105ed634c3af_5.png)

## 概述：移动平均线与交叉信号

上一节我们介绍了数据获取与基础处理。本节中，我们来看看如何利用移动平均线构建一个简单的分析策略。该策略的核心是计算两个不同周期的移动平均线：短期均线和长期均线。通过观察它们的交叉点，我们可以判断市场的潜在买入或卖出时机。

![](img/d6228f22295783d290cb105ed634c3af_7.png)

![](img/d6228f22295783d290cb105ed634c3af_9.png)

## 计算短期移动平均线

首先，我们需要计算短期移动平均线。通常，短期窗口可以设置为5天（代表一周的交易日）。但由于我们的示例数据时间跨度较长，为了更清晰地展示效果，我们将短期窗口设置为10天。

以下是计算短期移动平均线的步骤：

![](img/d6228f22295783d290cb105ed634c3af_11.png)

![](img/d6228f22295783d290cb105ed634c3af_13.png)

1.  选择目标股票数据，这里我们使用苹果公司的股票数据。
2.  使用Pandas的滚动窗口函数计算指定窗口内的平均值。

![](img/d6228f22295783d290cb105ed634c3af_15.png)

![](img/d6228f22295783d290cb105ed634c3af_17.png)

核心代码如下：
```python
# 假设 df 是包含苹果股价的DataFrame，列名为 ‘Close’
short_window = 10
df[‘M1’] = df[‘Close’].rolling(window=short_window).mean()
```
这段代码创建了一个名为 `M1` 的新列，它存储了以10天为窗口计算的收盘价移动平均值。

## 计算长期移动平均线

![](img/d6228f22295783d290cb105ed634c3af_19.png)

![](img/d6228f22295783d290cb105ed634c3af_21.png)

接下来，我们计算长期移动平均线。常见的长期窗口是20天或30天（约一个月的交易日）。这里我们选择30天作为长期窗口。

![](img/d6228f22295783d290cb105ed634c3af_23.png)

计算长期移动平均线的方法与短期类似：

![](img/d6228f22295783d290cb105ed634c3af_25.png)

![](img/d6228f22295783d290cb105ed634c3af_27.png)

1.  使用相同的股价数据。
2.  指定一个更大的窗口进行计算。

核心代码如下：
```python
long_window = 30
df[‘M2’] = df[‘Close’].rolling(window=long_window).mean()
```
这段代码创建了名为 `M2` 的新列，存储了30天移动平均值。

## 可视化股价与移动平均线

![](img/d6228f22295783d290cb105ed634c3af_29.png)

![](img/d6228f22295783d290cb105ed634c3af_31.png)

计算完成后，我们将股价、短期均线和长期均线绘制在同一张图上进行观察。这能帮助我们直观地看到它们的走势关系。

![](img/d6228f22295783d290cb105ed634c3af_33.png)

以下是绘图的核心代码：
```python
import matplotlib.pyplot as plt

![](img/d6228f22295783d290cb105ed634c3af_35.png)

![](img/d6228f22295783d290cb105ed634c3af_37.png)

plt.figure(figsize=(12, 6))
plt.plot(df[‘Close’], label=‘Apple Stock Price’, alpha=0.5)
plt.plot(df[‘M1’], label=‘Short-term MA (10 days)’, color=‘green’)
plt.plot(df[‘M2’], label=‘Long-term MA (30 days)’, color=‘red’)
plt.legend()
plt.show()
```
为了在长时间序列中更清晰地观察交叉点，我们可以截取其中一段数据（例如250个交易日）进行绘图。

![](img/d6228f22295783d290cb105ed634c3af_39.png)

![](img/d6228f22295783d290cb105ed634c3af_41.png)

![](img/d6228f22295783d290cb105ed634c3af_43.png)

## 识别黄金交叉与死亡交叉

在图表中，我们可以识别两种关键信号：

*   **死亡交叉**：当短期移动平均线从上方向下穿越长期移动平均线时，通常被视为下跌信号，提示可能不适合买入或应考虑卖出。
*   **黄金交叉**：当短期移动平均线从下方向上穿越长期移动平均线时，通常被视为上涨信号，提示可能是买入时机。

![](img/d6228f22295783d290cb105ed634c3af_45.png)

![](img/d6228f22295783d290cb105ed634c3af_47.png)

通过观察图表中绿色（短期）和红色（长期）线的交叉点，我们可以定位这些信号。

![](img/d6228f22295783d290cb105ed634c3af_49.png)

## 进阶：量化交叉信号

![](img/d6228f22295783d290cb105ed634c3af_51.png)

为了更精确地定位交叉点，我们可以创建一个信号指标。该指标在短期均线高于长期均线时标记为1，低于时标记为-1。

![](img/d6228f22295783d290cb105ed634c3af_53.png)

![](img/d6228f22295783d290cb105ed634c3af_55.png)

以下是创建信号指标的步骤：

![](img/d6228f22295783d290cb105ed634c3af_57.png)

![](img/d6228f22295783d290cb105ed634c3af_59.png)

1.  使用NumPy的 `where` 函数进行比较。
2.  将结果存储为新列。

核心代码如下：
```python
import numpy as np
df[‘position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
```
这个 `position` 列明确地标出了短期均线相对于长期均线的位置状态。

为了在图中清晰展示这个信号，我们可以使用双Y轴进行绘图，左边主Y轴显示股价和均线，右边次Y轴显示信号指标（1或-1）。

![](img/d6228f22295783d290cb105ed634c3af_61.png)

绘图代码如下：
```python
fig, ax1 = plt.subplots(figsize=(12, 6))

![](img/d6228f22295783d290cb105ed634c3af_63.png)

ax1.plot(df[‘Close’], label=‘Price’, color=‘blue’, alpha=0.5)
ax1.plot(df[‘M1’], label=‘MA Short’, color=‘green’)
ax1.plot(df[‘M2’], label=‘MA Long’, color=‘red’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

ax2 = ax1.twinx()
ax2.plot(df[‘position’], label=‘Signal’, color=‘purple’, linestyle=‘—’)
ax2.set_ylabel(‘Signal (1/-1)’)
ax2.legend(loc=‘upper right’)

![](img/d6228f22295783d290cb105ed634c3af_65.png)

![](img/d6228f22295783d290cb105ed634c3af_67.png)

plt.show()
```
在生成的图表中，紫色虚线（信号线）从-1变为1的点对应“黄金交叉”，从1变为-1的点对应“死亡交叉”，使得买卖信号一目了然。

![](img/d6228f22295783d290cb105ed634c3af_69.png)

![](img/d6228f22295783d290cb105ed634c3af_71.png)

## 总结

![](img/d6228f22295783d290cb105ed634c3af_73.png)

![](img/d6228f22295783d290cb105ed634c3af_75.png)

本节课中我们一起学习了移动平均线策略的基础应用。我们掌握了如何计算短期和长期移动平均线，并通过可视化方法识别了“黄金交叉”和“死亡交叉”这两种重要的交易信号。此外，我们还通过创建信号指标和双轴图表，实现了对交叉信号的量化与清晰展示。这个过程展示了如何利用Pandas进行金融数据计算，并结合Matplotlib将分析结果直观呈现出来。