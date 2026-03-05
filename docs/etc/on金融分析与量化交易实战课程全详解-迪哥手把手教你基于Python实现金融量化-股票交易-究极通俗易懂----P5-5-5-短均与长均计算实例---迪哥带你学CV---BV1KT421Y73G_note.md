# Python金融分析与量化交易实战课程：P5：5.5-短均与长均计算实例 📈

在本节课中，我们将学习如何计算股票的短期移动平均线和长期移动平均线，并利用它们识别“黄金交叉”与“死亡交叉”这两种常见的交易信号。我们将通过Python代码实现计算与可视化，让分析过程一目了然。

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_1.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_3.png)

## 概述

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_5.png)

移动平均线是分析股票趋势的基础工具。通过计算不同时间窗口内的平均价格，我们可以平滑价格波动，观察更长期的趋势。当短期均线从下方上穿长期均线时，形成“黄金交叉”，通常被视为买入信号；反之，当短期均线从上方下穿长期均线时，形成“死亡交叉”，通常被视为卖出信号。

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_7.png)

上一节我们介绍了如何获取和处理金融数据，本节中我们来看看如何基于这些数据计算关键的技术指标。

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_9.png)

## 计算短期与长期移动平均线

首先，我们需要为股票价格序列计算两个移动平均线：一个短期窗口（例如10天）和一个长期窗口（例如30天）。在Pandas中，我们可以使用`.rolling()`和`.mean()`方法轻松实现。

以下是计算移动平均线的核心代码步骤：

```python
# 假设 df 是包含苹果股票（‘AAPL’）价格的数据框
# 计算短期移动平均线（例如10天窗口）
short_window = 10
df[‘M1’] = df[‘AAPL’].rolling(window=short_window).mean()

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_11.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_13.png)

# 计算长期移动平均线（例如30天窗口）
long_window = 30
df[‘M2’] = df[‘AAPL’].rolling(window=long_window).mean()
```

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_15.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_17.png)

在这段代码中：
*   `df[‘AAPL’]` 代表苹果股票的每日价格序列。
*   `.rolling(window=10)` 创建了一个大小为10的滚动窗口。
*   `.mean()` 计算了每个窗口内价格的平均值，从而得到移动平均线。

## 可视化移动平均线与股价

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_19.png)

计算出指标后，最直观的方式就是将它们与原始股价一起绘制在图表上。这样我们可以清晰地观察它们的相对位置和交叉情况。

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_21.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_23.png)

以下是绘制图表的关键代码：

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_25.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_27.png)

```python
import matplotlib.pyplot as plt

# 选择要绘制的列：股价、短期均线、长期均线
plot_columns = [‘AAPL’, ‘M1’, ‘M2’]
df[plot_columns].plot(figsize=(12, 6))
plt.show()
```

为了更清晰地观察交叉点，我们可能需要对图表进行一些调整，例如只选取特定时间段的数据进行绘制。

## 识别黄金交叉与死亡交叉

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_29.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_31.png)

通过观察图表，我们可以手动定位交叉点。但更高效的方法是让程序自动标记这些信号。我们可以通过比较短期均线（M1）和长期均线（M2）的数值来实现。

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_33.png)

以下是自动生成交易信号的逻辑：

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_35.png)

1.  当 `M1 > M2` 时，意味着短期趋势强于长期趋势。
2.  当 `M1 < M2` 时，意味着短期趋势弱于长期趋势。

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_37.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_39.png)

我们可以使用NumPy的 `np.where` 函数来创建一个信号列，直观地展示这种关系：

```python
import numpy as np

# 生成交易信号：短期均线上穿长期均线为1（黄金交叉），下穿为-1（死亡交叉）
df[‘position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
```

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_41.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_43.png)

## 高级可视化：双坐标轴图表

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_45.png)

由于股价和移动平均线的数值范围与信号值（1和-1）相差很大，直接绘制在同一坐标轴上会导致信号线几乎看不见。为了解决这个问题，我们可以使用双坐标轴进行绘图。

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_47.png)

以下是创建双坐标轴图表的代码：

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_49.png)

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_51.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_53.png)

# 在第一个Y轴（左侧）绘制股价和均线
color = ‘tab:blue’
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’, color=color)
ax1.plot(df.index, df[‘AAPL’], color=color, label=‘Stock Price’)
ax1.plot(df.index, df[‘M1’], color=‘green’, label=‘Short MA (M1)’)
ax1.plot(df.index, df[‘M2’], color=‘red’, label=‘Long MA (M2)’)
ax1.tick_params(axis=‘y’, labelcolor=color)
ax1.legend(loc=‘upper left’)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_55.png)

# 创建第二个Y轴（右侧）绘制交易信号
ax2 = ax1.twinx()
color = ‘tab:purple’
ax2.set_ylabel(‘Signal’, color=color)
ax2.plot(df.index, df[‘position’], color=color, linestyle=‘--’, label=‘Trading Signal’)
ax2.tick_params(axis=‘y’, labelcolor=color)
ax2.legend(loc=‘upper right’)

fig.tight_layout()
plt.title(‘Stock Price with Moving Averages and Trading Signals’)
plt.show()
```

在这张图上，右侧Y轴的信号线在+1和-1之间切换。当信号线从-1跃升至+1时，对应图表左侧短期均线上穿长期均线的点，即**黄金交叉**。当信号线从+1下降至-1时，对应**死亡交叉**。

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_57.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_59.png)

## 关键概念与参数选择

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_61.png)

在实践中有几个要点需要注意：

*   **窗口选择**：短期和长期窗口的长度不是固定的。常见的组合有（5， 20）、（10， 30）、（20， 60）等，分别对应一周/一月、两周/一个半月、一个月/一个季度（以交易日计）。选择取决于你的交易策略和分析周期。
*   **数据量**：如果分析的数据时间跨度很长（如数年），使用较小的窗口（如5天）会产生大量波动，图表可能显得杂乱。此时可以适当增大窗口值，或聚焦于特定时间段进行分析。
*   **均值计算**：本例中使用的是简单移动平均（SMA），即算术平均值。这是最基础的形式，此外还有指数移动平均（EMA）等更复杂的算法，它们对近期价格赋予更高权重。

## 总结

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_63.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_65.png)

本节课中我们一起学习了金融数据分析中的一个核心实践：计算并应用移动平均线。

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_67.png)

我们首先使用Pandas的滚动窗口功能计算了短期和长期移动平均线。接着，通过Matplotlib将股价与均线可视化，直观地观察市场趋势。然后，我们引入了“黄金交叉”和“死亡交叉”的概念，并利用NumPy自动生成了对应的交易信号。最后，为了清晰展示，我们绘制了包含双坐标轴的高级图表，使价格走势与交易信号一目了然。

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_69.png)

![](img/ec9952daaaf3393f03bf7f8d21ec53c9_71.png)

这个过程展示了如何将基础的金融概念通过Python代码转化为可执行、可验证的分析策略，是构建更复杂量化交易模型的重要一步。