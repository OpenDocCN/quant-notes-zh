# Python金融量化：P6：短均与长均计算实例 📈

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的分析策略。我们将识别“黄金交叉”和“死亡交叉”这两个关键信号，并通过代码和图表直观地展示它们。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_1.png)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_3.png)

## 概述

![](img/0a7f3dba80dd7aa4bb696212169e3a25_5.png)

我们将分析苹果公司的股票数据。核心任务是计算两个移动平均线：一个短期均线和一个长期均线。通过观察这两条线的交叉点，我们可以判断市场的潜在买入或卖出时机。短期均线向上穿越长期均线被称为“黄金交叉”，通常被视为买入信号；反之，短期均线向下穿越长期均线则被称为“死亡交叉”，通常被视为卖出信号。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_7.png)

上一节我们介绍了数据获取与处理的基础，本节中我们来看看如何应用这些数据计算技术指标。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_9.png)

## 计算短期与长期移动平均线

首先，我们需要从数据中计算出短期和长期的移动平均值。移动平均的计算公式可以表示为：

**MA(n) = (P1 + P2 + ... + Pn) / n**

其中，`P` 代表股价，`n` 代表计算平均值的窗口期（例如5天、20天）。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_11.png)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_13.png)

以下是计算步骤的代码实现。我们选择苹果公司（AAPL）的股票数据，并分别计算10天（短期）和30天（长期）的移动平均线。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_15.png)

```python
# 假设 df 是包含苹果股价的DataFrame，列名为 ‘AAPL’
# 计算短期移动平均线（例如10天窗口）
short_window = 10
df[‘M1’] = df[‘AAPL’].rolling(window=short_window).mean()

![](img/0a7f3dba80dd7aa4bb696212169e3a25_17.png)

# 计算长期移动平均线（例如30天窗口）
long_window = 30
df[‘M2’] = df[‘AAPL’].rolling(window=long_window).mean()
```

![](img/0a7f3dba80dd7aa4bb696212169e3a25_19.png)

执行上述代码后，我们的DataFrame中会增加两列：`M1`（短期均线）和`M2`（长期均线）。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_21.png)

## 可视化股价与移动平均线

![](img/0a7f3dba80dd7aa4bb696212169e3a25_23.png)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_25.png)

计算出指标后，最直观的方式就是将它们绘制在图表上。我们可以使用`matplotlib`库来绘制苹果股价、短期均线和长期均线的走势图。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_27.png)

```python
import matplotlib.pyplot as plt

# 选择要绘制的列
plot_columns = [‘AAPL‘， ’M1‘， ’M2‘]
df[plot_columns].plot(figsize=(12， 6))
plt.show()
```

由于原始数据时间跨度较长（2010-2018年），图表上的交叉点可能过多且不清晰。为了更好地观察局部趋势，我们可以截取其中一年的数据进行绘图，例如最近250个交易日（大约一年）。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_29.png)

```python
# 截取最近250个交易日的数据进行绘图
df_recent = df[plot_columns].tail(250)
df_recent.plot(figsize=(12， 6))
plt.show()
```

![](img/0a7f3dba80dd7aa4bb696212169e3a25_31.png)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_33.png)

在生成的图表中，蓝色线代表苹果股价，绿色线代表短期均线（M1），红色线代表长期均线（M2）。通过观察绿线（短期）与红线（长期）的相对位置和交叉情况，我们可以初步判断市场趋势。

## 识别黄金交叉与死亡交叉

![](img/0a7f3dba80dd7aa4bb696212169e3a25_35.png)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_37.png)

现在，我们来学习如何在图表上识别“黄金交叉”和“死亡交叉”。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_39.png)

以下是判断交叉点的核心逻辑：
*   **黄金交叉**：当短期均线从下方**上穿**长期均线时发生，预示着股价可能进入上涨趋势，是潜在的**买入信号**。
*   **死亡交叉**：当短期均线从上方**下穿**长期均线时发生，预示着股价可能进入下跌趋势，是潜在的**卖出信号**。

在刚才绘制的图表中，我们可以寻找这样的交叉点。例如，当绿色线（短期）从下方向上穿过红色线（长期）时，那个交叉点就是“黄金交叉”。反之，当绿色线从上方向下穿过红色线时，就是“死亡交叉”。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_41.png)

## 使用数值信号增强分析

![](img/0a7f3dba80dd7aa4bb696212169e3a25_43.png)

为了更精确、更自动化地识别交叉信号，我们可以创建一个数值信号列。这个信号列明确标记出短期均线是高于还是低于长期均线。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_45.png)

我们使用`numpy`的`where`函数来创建这个信号：
*   当短期均线 `M1` **大于** 长期均线 `M2` 时，标记为 **1**。
*   当短期均线 `M1` **小于** 长期均线 `M2` 时，标记为 **-1**。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_47.png)

```python
import numpy as np

![](img/0a7f3dba80dd7aa4bb696212169e3a25_49.png)

# 创建信号列
df[‘position’] = np.where(df[‘M1’] > df[‘M2’]， 1， -1)
```

![](img/0a7f3dba80dd7aa4bb696212169e3a25_51.png)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_53.png)

现在，`df[‘position’]` 列的值会在1和-1之间切换。数值从-1变为1的点，对应着“黄金交叉”；从1变为-1的点，对应着“死亡交叉”。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_55.png)

为了清晰地展示这个信号，我们可以将其绘制在股价图的右侧Y轴上。

```python
fig， ax1 = plt.subplots(figsize=(12，6))

# 在左侧Y轴绘制股价和均线
ax1.plot(df_recent.index， df_recent[‘AAPL’]， label=‘AAPL Price’， color=‘blue’)
ax1.plot(df_recent.index， df_recent[‘M1’]， label=‘Short MA (M1)’ ， color=‘green’)
ax1.plot(df_recent.index， df_recent[‘M2’]， label=‘Long MA (M2)’ ， color=‘red’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_57.png)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_59.png)

# 创建右侧Y轴，用于绘制信号
ax2 = ax1.twinx()
ax2.plot(df_recent.index， df_recent[‘position’]， label=‘Signal (Position)’ ， color=‘orange’， linestyle=‘--’)
ax2.set_ylabel(‘Signal (1 / -1)’)
ax2.set_ylim([-1.5， 1.5])
ax2.legend(loc=‘upper right’)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_61.png)

plt.title(‘AAPL Stock Price with Moving Averages and Trading Signal’)
plt.show()
```

在这张图中，橙色的虚线代表我们的交易信号。当信号线从-1跃升到1时，标志着“黄金交叉”的出现；当信号线从1下降到-1时，则标志着“死亡交叉”的出现。这样，买卖时机就变得一目了然。

## 总结

![](img/0a7f3dba80dd7aa4bb696212169e3a25_63.png)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_65.png)

本节课中我们一起学习了如何利用移动平均线进行简单的股票技术分析。我们首先计算了短期和长期的移动平均线，然后通过可视化图表观察它们的交叉情况，识别出“黄金交叉”和“死亡交叉”这两种重要的市场信号。最后，我们还通过创建数值信号列，使交叉点的识别更加精确和自动化。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_67.png)

核心要点包括：
1.  **移动平均线（MA）** 是平滑股价走势、识别趋势的基本工具。
2.  **黄金交叉**（短期MA上穿长期MA）通常被视为**买入信号**。
3.  **死亡交叉**（短期MA下穿长期MA）通常被视为**卖出信号**。
4.  使用 `DataFrame.rolling().mean()` 可以方便地计算移动平均。
5.  结合 `matplotlib` 绘图和 `numpy.where()` 创建信号，能够将分析策略清晰、直观地呈现出来。

![](img/0a7f3dba80dd7aa4bb696212169e3a25_69.png)

![](img/0a7f3dba80dd7aa4bb696212169e3a25_71.png)

通过本实例，你已经掌握了量化分析中的一个基础但非常实用的策略。在实际应用中，你可以尝试调整短期和长期窗口的大小（如5日与20日），或结合其他指标来优化策略。