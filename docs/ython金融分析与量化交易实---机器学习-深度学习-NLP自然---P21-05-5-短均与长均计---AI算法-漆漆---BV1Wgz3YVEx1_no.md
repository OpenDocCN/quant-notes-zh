# 金融分析与量化交易：05-5：短均与长均计算实例 📈

![](img/0ebf06b60e526857ac562c7f7262ac3c_1.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_3.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的分析策略。我们将识别“黄金交叉”和“死亡交叉”这两种常见的交易信号，并使用Python进行实现和可视化。

![](img/0ebf06b60e526857ac562c7f7262ac3c_5.png)

## 概述

![](img/0ebf06b60e526857ac562c7f7262ac3c_7.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_9.png)

移动平均线是分析股票趋势的常用技术指标。通过比较短期和长期移动平均线，我们可以判断市场的潜在买卖点。当短期均线从下方上穿长期均线时，形成“黄金交叉”，通常被视为买入信号。反之，当短期均线从上方下穿长期均线时，形成“死亡交叉”，通常被视为卖出信号。

## 计算短期与长期移动平均线

首先，我们需要计算两个移动平均线。通常，短期窗口可以选择5天（一周的交易日），长期窗口可以选择20天或30天。由于我们的示例数据时间跨度较长，为了更清晰地展示，我们将使用稍大的窗口。

![](img/0ebf06b60e526857ac562c7f7262ac3c_11.png)

以下是计算步骤：

![](img/0ebf06b60e526857ac562c7f7262ac3c_13.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_15.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_17.png)

1.  选择目标股票数据（例如苹果公司股票）。
2.  分别计算短期（例如10天）和长期（例如30天）的移动平均值。

```python
# 假设 df 是包含苹果股价的DataFrame，列名为 ‘Apple‘
# 计算短期移动平均（10天窗口）
df[‘M1‘] = df[‘Apple‘].rolling(window=10).mean()

![](img/0ebf06b60e526857ac562c7f7262ac3c_19.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_21.png)

# 计算长期移动平均（30天窗口）
df[‘M2‘] = df[‘Apple‘].rolling(window=30).mean()
```

![](img/0ebf06b60e526857ac562c7f7262ac3c_23.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_25.png)

## 可视化移动平均线

![](img/0ebf06b60e526857ac562c7f7262ac3c_27.png)

计算完成后，我们可以将股价、短期均线和长期均线绘制在同一张图上进行观察。

```python
# 绘制股价与移动平均线
df[[‘Apple‘, ‘M1‘, ‘M2‘]].plot(figsize=(10, 6))
```

![](img/0ebf06b60e526857ac562c7f7262ac3c_29.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_31.png)

为了更清晰地观察交叉点，我们可以只选取一段时间的数据（例如最近250个交易日）进行绘图。

![](img/0ebf06b60e526857ac562c7f7262ac3c_33.png)

## 识别黄金交叉与死亡交叉

![](img/0ebf06b60e526857ac562c7f7262ac3c_35.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_37.png)

在图表中，我们可以直观地寻找交叉点：
*   **死亡交叉**：当短期均线（绿色）从上方向下穿越长期均线（红色）时，预示着下跌趋势，应考虑卖出。
*   **黄金交叉**：当短期均线（绿色）从下方向上穿越长期均线（红色）时，预示着上涨趋势，可考虑买入。

![](img/0ebf06b60e526857ac562c7f7262ac3c_39.png)

## 使用数值信号增强分析

![](img/0ebf06b60e526857ac562c7f7262ac3c_41.png)

为了更精确地定位交叉点，我们可以创建一个信号列。当短期均线大于长期均线时，标记为 `1`；小于时，标记为 `-1`。这个信号清晰地展示了趋势的切换。

![](img/0ebf06b60e526857ac562c7f7262ac3c_43.png)

```python
import numpy as np

![](img/0ebf06b60e526857ac562c7f7262ac3c_45.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_47.png)

# 生成交易信号：短期均线 > 长期均线时为1，否则为-1
df[‘position‘] = np.where(df[‘M1‘] > df[‘M2‘], 1, -1)
```

![](img/0ebf06b60e526857ac562c7f7262ac3c_49.png)

由于信号值（1和-1）与股价数值范围差异巨大，为了在同一图表中清晰展示，我们可以使用双Y轴进行绘图。

![](img/0ebf06b60e526857ac562c7f7262ac3c_51.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_53.png)

```python
import matplotlib.pyplot as plt

![](img/0ebf06b60e526857ac562c7f7262ac3c_55.png)

fig, ax1 = plt.subplots(figsize=(12, 6))

# 左Y轴：绘制股价和移动平均线
ax1.plot(df[‘Apple‘], label=‘Price‘, color=‘blue‘, alpha=0.5)
ax1.plot(df[‘M1‘], label=‘Short MA (M1)‘, color=‘green‘)
ax1.plot(df[‘M2‘], label=‘Long MA (M2)‘, color=‘red‘)
ax1.set_ylabel(‘Price‘)
ax1.legend(loc=‘upper left‘)

![](img/0ebf06b60e526857ac562c7f7262ac3c_57.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_59.png)

# 右Y轴：绘制交易信号
ax2 = ax1.twinx()
ax2.plot(df[‘position‘], label=‘Signal‘, color=‘purple‘, linestyle=‘--‘)
ax2.set_ylabel(‘Signal (1/-1)‘)
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc=‘upper right‘)

![](img/0ebf06b60e526857ac562c7f7262ac3c_61.png)

plt.title(‘Stock Price with Moving Averages and Trading Signals‘)
plt.show()
```

在这张图中，紫色虚线（信号线）在 `1` 和 `-1` 之间切换。信号从 `-1` 变为 `1` 的点对应**黄金交叉**，从 `1` 变为 `-1` 的点对应**死亡交叉**，这使得买卖信号的识别变得一目了然。

![](img/0ebf06b60e526857ac562c7f7262ac3c_63.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_65.png)

## 总结

![](img/0ebf06b60e526857ac562c7f7262ac3c_67.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_69.png)

![](img/0ebf06b60e526857ac562c7f7262ac3c_71.png)

本节课我们一起学习了如何利用Pandas计算股票的短期和长期移动平均线。我们通过可视化图表直观地观察了“黄金交叉”和“死亡交叉”这两种重要的技术信号。此外，我们还通过创建数值信号并使用双Y轴绘图，进一步精确和清晰地标识出了这些关键的交易时机。这个方法为后续构建自动化交易策略奠定了基础。