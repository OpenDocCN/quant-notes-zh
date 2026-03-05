# Python金融分析与量化交易实战：P9：9.3-短均与长均计算实例 📈

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_1.png)

在本节课中，我们将学习如何计算股票的短期移动平均线和长期移动平均线，并利用它们识别“黄金交叉”与“死亡交叉”这两种常见的交易信号。我们将通过具体的代码实例，展示如何从数据计算到可视化分析的完整流程。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_3.png)

## 概述：移动平均线与交叉信号

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_5.png)

上一节我们介绍了金融数据分析的基础。本节中，我们来看看如何应用移动平均线进行策略分析。核心思想是计算股价在不同时间窗口内的平均值，通过观察短期均线与长期均线的相对位置变化，来判断市场趋势的潜在转折点。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_7.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_9.png)

## 计算短期与长期移动平均线

首先，我们需要为股价序列计算两个关键指标：短期移动平均线（Short-term Moving Average）和长期移动平均线（Long-term Moving Average）。移动平均线能有效平滑股价波动，反映出趋势方向。

以下是计算步骤：

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_11.png)

1.  **选择目标股票**：我们以苹果公司（AAPL）的股价数据为例。
2.  **定义时间窗口**：
    *   短期窗口通常较短，例如5天（对应一周的交易日）。鉴于我们的数据集时间跨度较长，为让图表更清晰，此处示例使用10天。
    *   长期窗口通常较长，例如20天或30天（对应约一个月交易日）。此处示例使用30天。
3.  **执行计算**：使用Pandas库的`.rolling()`方法结合`.mean()`函数计算指定窗口内的股价平均值。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_13.png)

相关核心代码如下：

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_15.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_17.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_19.png)

```python
import pandas as pd
import numpy as np

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_21.png)

# 假设 `df` 是包含苹果股价的DataFrame，其中‘Close’为收盘价列
# 计算10日短期移动平均线
short_window = 10
df[‘M1’] = df[‘Close’].rolling(window=short_window).mean()

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_23.png)

# 计算30日长期移动平均线
long_window = 30
df[‘M2’] = df[‘Close’].rolling(window=long_window).mean()
```

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_25.png)

## 可视化移动平均线与股价

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_27.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_29.png)

计算出移动平均线后，最直观的方式是将其与原始股价一同绘制在图表上，以便观察它们之间的互动关系。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_31.png)

以下是绘图步骤：

```python
import matplotlib.pyplot as plt

# 选择要展示的列：收盘价、短期均线、长期均线
plot_columns = [‘Close‘， ‘M1’， ‘M2’]
df[plot_columns].plot(figsize=(12， 6))
plt.title(‘Apple Stock Price with Moving Averages‘)
plt.ylabel(‘Price‘)
plt.xlabel(‘Date‘)
plt.show()
```

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_33.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_35.png)

在生成的图表中，我们通常可以看到：
*   **蓝色线**代表原始股价。
*   **绿色线**代表短期移动平均线（M1）。
*   **红色线**代表长期移动平均线（M2）。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_37.png)

通过观察短期均线（绿线）与长期均线（红线）的相对位置和交叉情况，我们可以进行趋势分析。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_39.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_41.png)

## 识别黄金交叉与死亡交叉

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_43.png)

上一节我们绘制了均线，本节中我们来看看如何解读图表中的关键信号。交叉信号是移动平均线策略的核心。

以下是两种关键交叉信号的定义：

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_45.png)

*   **黄金交叉 (Golden Cross)**：当短期移动平均线从下方**上穿**长期移动平均线时，通常被视为**买入信号**，预示着上涨趋势可能开始。
*   **死亡交叉 (Death Cross)**：当短期移动平均线从上方**下穿**长期移动平均线时，通常被视为**卖出信号**，预示着下跌趋势可能开始。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_47.png)

在图表中，我们可以手动定位这些交叉点。例如，当绿线（短期）上穿红线（长期）时，对应区域可能是一个潜在的“黄金交叉”机会点；反之，则是“死亡交叉”的警示点。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_49.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_51.png)

## 使用数值标记交叉信号

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_53.png)

为了更精确、自动化地识别交叉点，我们可以创建一个信号列，用数值明确标记短期均线与长期均线的相对位置。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_55.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_57.png)

以下是创建信号标记的步骤：

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_59.png)

1.  **比较均线**：使用`np.where`函数判断短期均线（M1）是否大于长期均线（M2）。
2.  **赋值标记**：如果M1 > M2，标记为`1`（代表短期趋势强于长期）；如果M1 <= M2，标记为`-1`（代表短期趋势弱于长期）。
3.  **可视化信号**：将标记序列在图表中用单独的Y轴绘制出来，可以清晰看到信号切换的点，即交叉发生的位置。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_61.png)

相关核心代码如下：

```python
# 创建位置信号列
df[‘Position’] = np.where(df[‘M1’] > df[‘M2’]， 1， -1)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_63.png)

# 绘制股价、均线及信号标记
fig， ax1 = plt.subplots(figsize=(14， 7))

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_65.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_67.png)

# 主Y轴（左侧）：绘制股价和均线
ax1.plot(df[‘Close’]， label=‘Close Price’， alpha=0.5)
ax1.plot(df[‘M1’]， label=f‘{short_window}-Day MA’， color=‘green’)
ax1.plot(df[‘M2’]， label=f‘{long_window}-Day MA’， color=‘red’)
ax1.set_ylabel(‘Price‘)
ax1.legend(loc=‘upper left‘)
ax1.set_title(‘Apple Stock Price， Moving Averages， and Crossover Signals‘)

# 次Y轴（右侧）：绘制信号标记
ax2 = ax1.twinx()
ax2.plot(df[‘Position’]， label=‘Crossover Signal’， color=‘blue’， linestyle=‘--’， alpha=0.7)
ax2.set_ylabel(‘Signal (1/-1)‘)
ax2.set_ylim([-1.5， 1.5])
ax2.legend(loc=‘upper right‘)

plt.show()
```

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_69.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_71.png)

在这张图中，右侧Y轴的蓝色虚线在`1`和`-1`之间切换。**从`-1`变为`1`的转折点对应“黄金交叉”**，**从`1`变为`-1`的转折点对应“死亡交叉”**。这使得交叉信号的识别变得一目了然。

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_73.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_75.png)

## 总结

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_77.png)

![](img/90fe3e337e17dc39f4faa1cc50c1bfac_79.png)

本节课中我们一起学习了移动平均线在股票分析中的应用。我们首先介绍了如何计算短期和长期移动平均线，然后通过可视化方法观察它们与股价的关系。接着，我们重点讲解了如何利用这两条均线的交叉——即“黄金交叉”和“死亡交叉”——来识别潜在的市场趋势转折点。最后，我们还通过`np.where`函数创建了数值化的信号标记，使得交叉点的识别更加清晰和自动化。掌握这些基础是构建更复杂量化交易策略的重要第一步。