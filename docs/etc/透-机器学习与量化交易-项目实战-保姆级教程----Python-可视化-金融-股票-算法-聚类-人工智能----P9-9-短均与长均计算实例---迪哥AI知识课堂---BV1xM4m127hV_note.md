# 机器学习与量化交易：P9：短均与长均计算实例 📈

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的技术分析策略。我们将识别“黄金交叉”和“死亡交叉”这两种关键信号，并使用Python进行数据计算与可视化。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_1.png)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_3.png)

上一节我们介绍了数据处理的基础，本节中我们来看看如何应用这些技术进行具体的指标计算。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_5.png)

## 策略概述：黄金交叉与死亡交叉 💡

该分析策略的核心是计算两个指标：短期移动平均线和长期移动平均线。通过观察这两条线的交叉点，我们可以判断市场的潜在买卖时机。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_7.png)

*   **黄金交叉**：当短期均线从下方上穿长期均线时，通常被视为**买入信号**，预示着价格可能开始上涨。
*   **死亡交叉**：当短期均线从上方下穿长期均线时，通常被视为**卖出信号**，预示着价格可能开始下跌。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_9.png)

接下来，我们将按照步骤实现这个策略。

## 第一步：计算短期移动平均线 📊

首先，我们需要计算短期移动平均线。在股市中，5日（一周交易日）均线较为常见。但由于我们的数据集时间跨度较长（2010年至2018年），为了更清晰地展示交叉现象，我们将短期窗口设置为10天。

以下是计算短期均线的代码：

![](img/3ee056fb9d1339f48b1dcbe3beacde89_11.png)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_13.png)

```python
# 假设 df 是包含苹果股票（‘AAPL’）价格的数据框
short_window = 10
df[‘M1’] = df[‘AAPL’].rolling(window=short_window).mean()
```

![](img/3ee056fb9d1339f48b1dcbe3beacde89_15.png)

这段代码的含义是：以10天为一个窗口，计算苹果股票收盘价的滚动平均值，并将结果作为新列‘M1’（代表短期均线）添加到数据框中。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_17.png)

## 第二步：计算长期移动平均线 📈

![](img/3ee056fb9d1339f48b1dcbe3beacde89_19.png)

接着，我们计算长期移动平均线。常见的长期窗口是20或30天（约一个月交易日）。这里我们选择30天作为长期窗口。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_21.png)

以下是计算长期均线的代码：

![](img/3ee056fb9d1339f48b1dcbe3beacde89_23.png)

```python
long_window = 30
df[‘M2’] = df[‘AAPL’].rolling(window=long_window).mean()
```

![](img/3ee056fb9d1339f48b1dcbe3beacde89_25.png)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_27.png)

这段代码以30天为窗口计算滚动平均值，并将结果存储为新列‘M2’（代表长期均线）。

## 第三步：可视化均线与股价 🎨

计算完指标后，我们将股价、短期均线和长期均线绘制在同一张图上进行观察。

以下是绘图代码：

![](img/3ee056fb9d1339f48b1dcbe3beacde89_29.png)

```python
import matplotlib.pyplot as plt

![](img/3ee056fb9d1339f48b1dcbe3beacde89_31.png)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_33.png)

# 选择要绘制的列
plot_columns = [‘AAPL’， ‘M1’， ‘M2’]
df[plot_columns].plot(figsize=(12， 6))
plt.show()
```

为了在长时间序列中更清晰地观察交叉点，我们可以只绘制数据的一部分（例如最后250个交易日，约一年）。调整后的绘图代码如下：

![](img/3ee056fb9d1339f48b1dcbe3beacde89_35.png)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_37.png)

```python
df[plot_columns].iloc[-250:].plot(figsize=(12， 6))
plt.show()
```

![](img/3ee056fb9d1339f48b1dcbe3beacde89_39.png)

在生成的图表中：
*   **蓝色线**代表苹果股票的实际价格。
*   **绿色线**代表10日短期均线（M1）。
*   **红色线**代表30日长期均线（M2）。

观察图表，我们可以找到交叉点：
1.  当**绿色线（短期）向下穿过红色线（长期）**时，形成**死亡交叉**，提示可能下跌，应考虑卖出或观望。
2.  当**绿色线（短期）向上穿过红色线（长期）**时，形成**黄金交叉**，提示可能上涨，可以考虑买入。

## 第四步：量化交叉信号 🔢

![](img/3ee056fb9d1339f48b1dcbe3beacde89_41.png)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_43.png)

为了更精确地定位交叉点，我们可以创建一个信号列来量化短期均线与长期均线的关系。

我们使用`np.where`函数来创建这个信号：
*   当短期均线 **M1 >** 长期均线 **M2** 时，标记为 **1**。
*   当短期均线 **M1 <** 长期均线 **M2** 时，标记为 **-1**。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_45.png)

以下是实现代码：

![](img/3ee056fb9d1339f48b1dcbe3beacde89_47.png)

```python
import numpy as np
df[‘position’] = np.where(df[‘M1’] > df[‘M2’]， 1， -1)
```

![](img/3ee056fb9d1339f48b1dcbe3beacde89_49.png)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_51.png)

这个`position`列清晰地标明了每个时间点上，短期均线相对于长期均线的位置状态。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_53.png)

## 第五步：可视化交叉信号 📉

![](img/3ee056fb9d1339f48b1dcbe3beacde89_55.png)

最后，我们将股价、均线以及新生成的信号线一起绘制出来。由于信号值（1和-1）与股价数值尺度差异巨大，我们使用双Y轴来清晰展示。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_57.png)

以下是最终的绘图代码：

```python
fig， ax1 = plt.subplots(figsize=(14， 7))

# 在第一个Y轴（左侧）绘制股价和均线
ax1.plot(df.index， df[‘AAPL’]， label=‘AAPL Price’， alpha=0.5)
ax1.plot(df.index， df[‘M1’]， label=‘10-day MA’， linewidth=2)
ax1.plot(df.index， df[‘M2’]， label=‘30-day MA’， linewidth=2)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_59.png)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_61.png)

# 创建第二个Y轴（右侧）来绘制信号线
ax2 = ax1.twinx()
ax2.plot(df.index， df[‘position’]， label=‘Signal (1/-1)’， color=‘purple’， linestyle=‘--’)
ax2.set_ylabel(‘Signal’)
ax2.set_ylim([-1.5， 1.5])
ax2.legend(loc=‘upper right’)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_63.png)

plt.title(‘AAPL Stock Price with Moving Averages and Trading Signal’)
plt.show()
```

在这张图中：
*   左侧Y轴显示价格。
*   右侧Y轴显示信号（1或-1）。
*   **紫色虚线**代表信号线。
*   当信号线从 **-1 变为 1** 时，对应图表中的**黄金交叉**点。
*   当信号线从 **1 变为 -1** 时，对应图表中的**死亡交叉**点。

这样，我们就能非常直观地看到所有潜在的买卖信号位置。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_65.png)

---

![](img/3ee056fb9d1339f48b1dcbe3beacde89_67.png)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_69.png)

本节课中我们一起学习了如何利用移动平均线构建一个简单的量化交易策略。我们掌握了以下核心技能：

![](img/3ee056fb9d1339f48b1dcbe3beacde89_71.png)

1.  使用Pandas的`.rolling().mean()`方法计算**短期**和**长期移动平均线**。
2.  理解了**黄金交叉**（买入信号）和**死亡交叉**（卖出信号）的市场含义。
3.  使用Matplotlib对股价、均线进行**可视化分析**。
4.  利用`np.where`函数**量化生成交易信号**，并通过双Y轴图表清晰展示。

![](img/3ee056fb9d1339f48b1dcbe3beacde89_73.png)

![](img/3ee056fb9d1339f48b1dcbe3beacde89_75.png)

这个策略是许多复杂交易系统的基础。在实际应用中，你可以尝试调整均线的周期（如5日与20日），或结合其他指标（如成交量）来优化策略效果。