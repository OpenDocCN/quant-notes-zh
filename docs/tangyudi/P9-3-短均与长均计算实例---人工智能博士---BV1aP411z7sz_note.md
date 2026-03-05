# 股票分析策略：P9：3-短均与长均计算实例 📈

![](img/1ad2cda3ab1ab0b4aa9617882c325246_1.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的分析策略。我们将重点理解“黄金交叉”和“死亡交叉”这两个核心概念，并通过代码实现和可视化来直观地展示它们。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_3.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_5.png)

## 概述

移动平均线是分析股票趋势的常用工具。通过比较短期和长期移动平均线，我们可以判断市场的潜在买入或卖出信号。具体来说，当短期均线从下方上穿长期均线时，形成“黄金交叉”，通常被视为买入信号；反之，当短期均线从上方下穿长期均线时，形成“死亡交叉”，通常被视为卖出信号。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_7.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_9.png)

## 计算短期与长期移动平均线

要识别交叉点，我们首先需要计算两条移动平均线：一条代表短期趋势，另一条代表长期趋势。

在金融分析中，常见的短期窗口是5天（代表一周的交易日），长期窗口是20天或30天（代表一个月左右的交易日）。由于我们的示例数据时间跨度较长，为了更清晰地展示，我们将适当放大窗口。

以下是计算步骤。我们以苹果公司（AAPL）的股票数据为例。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_11.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_13.png)

首先，我们计算短期移动平均线（例如10天窗口）和长期移动平均线（例如30天窗口）。在Pandas中，这可以通过`.rolling()`和`.mean()`方法轻松实现。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_15.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_17.png)

```python
# 假设 df 是包含苹果股价的DataFrame，其中‘Close’是收盘价列
# 计算短期移动平均线 (M1)
df[‘M1’] = df[‘Close’].rolling(window=10).mean()

# 计算长期移动平均线 (M2)
df[‘M2’] = df[‘Close’].rolling(window=30).mean()
```

![](img/1ad2cda3ab1ab0b4aa9617882c325246_19.png)

## 可视化移动平均线与股价

![](img/1ad2cda3ab1ab0b4aa9617882c325246_21.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_23.png)

计算完成后，我们可以将股价与两条移动平均线一同绘制出来，以便直观观察它们的走势关系。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_25.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_27.png)

为了使图表更清晰，我们可以只选取一段时间内的数据进行绘图。

```python
import matplotlib.pyplot as plt

# 选取最近一年的数据用于绘图（假设一年有约252个交易日）
df_recent = df.tail(252)

plt.figure(figsize=(12, 6))
plt.plot(df_recent[‘Close’], label=‘Apple Stock Price’, alpha=0.5)
plt.plot(df_recent[‘M1’], label=‘Short-term MA (10 days)’, color=‘green’)
plt.plot(df_recent[‘M2’], label=‘Long-term MA (30 days)’, color=‘red’)
plt.legend()
plt.show()
```

![](img/1ad2cda3ab1ab0b4aa9617882c325246_29.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_31.png)

在生成的图表中，蓝色线（或原色线）代表股价，绿线代表短期均线（M1），红线代表长期均线（M2）。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_33.png)

## 识别黄金交叉与死亡交叉

![](img/1ad2cda3ab1ab0b4aa9617882c325246_35.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_37.png)

现在，我们来观察图表中的交叉点。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_39.png)

*   **死亡交叉**：当短期均线（绿色）从上方向下穿越长期均线（红色）时，表明短期趋势转弱，可能预示着下跌行情，此时不适合买入，应考虑卖出。
*   **黄金交叉**：当短期均线（绿色）从下方向上穿越长期均线（红色）时，表明短期趋势走强，可能预示着上涨行情，此时可以考虑买入。

通过观察图表，你可以清晰地找到这些交叉点，并理解其市场含义。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_41.png)

## 使用信号指标进行增强分析

![](img/1ad2cda3ab1ab0b4aa9617882c325246_43.png)

为了更精确地定位交叉点，我们可以创建一个信号指标。这个指标明确标出短期均线高于或低于长期均线的区域。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_45.png)

我们可以使用`np.where`函数来创建这个位置信号：当短期均线大于长期均线时，标记为1；否则标记为-1。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_47.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_49.png)

```python
import numpy as np

![](img/1ad2cda3ab1ab0b4aa9617882c325246_51.png)

# 生成位置信号
df[‘Position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
```

![](img/1ad2cda3ab1ab0b4aa9617882c325246_53.png)

为了在图表中同时展示股价和这个信号指标，我们需要使用双Y轴。左边Y轴显示股价和移动平均线，右边Y轴显示我们的位置信号（1或-1）。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_55.png)

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

# 第一个Y轴：绘制股价和均线
ax1.plot(df_recent[‘Close’], label=‘Price’, color=‘blue’, alpha=0.5)
ax1.plot(df_recent[‘M1’], label=‘M1 (10-day)’, color=‘green’)
ax1.plot(df_recent[‘M2’], label=‘M2 (30-day)’, color=‘red’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_57.png)

# 第二个Y轴：绘制位置信号
ax2 = ax1.twinx()
ax2.plot(df_recent[‘Position’], label=‘Position Signal’, color=‘purple’, linestyle=‘--’)
ax2.set_ylabel(‘Signal (1 or -1)’)
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc=‘upper right’)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_59.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_61.png)

plt.title(‘Stock Price with Moving Averages and Position Signal’)
plt.show()
```

在这张增强的图表中，紫色虚线代表我们的位置信号。当信号从-1变为1时，对应着“黄金交叉”；当信号从1变为-1时，对应着“死亡交叉”。这使得交叉点的识别变得更加直接和自动化。

## 总结

![](img/1ad2cda3ab1ab0b4aa9617882c325246_63.png)

本节课我们一起学习了如何利用移动平均线进行简单的股票趋势分析。我们掌握了以下核心技能：

![](img/1ad2cda3ab1ab0b4aa9617882c325246_65.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_67.png)

1.  **计算移动平均线**：使用`.rolling().mean()`计算指定窗口的短期和长期移动平均线。
2.  **核心概念**：理解了**黄金交叉**（买入信号）和**死亡交叉**（卖出信号）的市场含义。
3.  **数据可视化**：绘制股价与移动平均线图表，直观识别交叉点。
4.  **信号生成**：利用`np.where`创建位置信号指标，并通过双Y轴图表进行增强展示。

![](img/1ad2cda3ab1ab0b4aa9617882c325246_69.png)

![](img/1ad2cda3ab1ab0b4aa9617882c325246_71.png)

通过这个实例，你可以看到，借助Pandas和Matplotlib等工具，常见的金融数据分析可以变得非常直观和易于实现。你可以尝试调整不同的时间窗口，或将其应用于其他股票数据，来验证策略的有效性。