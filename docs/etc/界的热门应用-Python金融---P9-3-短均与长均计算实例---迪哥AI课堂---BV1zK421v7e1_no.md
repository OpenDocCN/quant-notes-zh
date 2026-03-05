# Python金融分析量化交易：P9：3-短均与长均计算实例 📈

![](img/44e8e259468e99288b55ecdd4bfd6566_1.png)

在本节课中，我们将学习如何计算股票的短期移动平均线和长期移动平均线，并利用它们来识别“黄金交叉”和“死亡交叉”这两种重要的交易信号。我们将通过Python代码，以苹果公司的股票数据为例，一步步实现这个分析策略。

![](img/44e8e259468e99288b55ecdd4bfd6566_3.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_5.png)

## 概述

移动平均线是技术分析中常用的工具，用于平滑股价波动，识别趋势。当短期移动平均线从下方上穿长期移动平均线时，形成“黄金交叉”，通常被视为买入信号。反之，当短期移动平均线从上方下穿长期移动平均线时，形成“死亡交叉”，通常被视为卖出信号。本节我们将学习如何计算并可视化这两种交叉。

![](img/44e8e259468e99288b55ecdd4bfd6566_7.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_9.png)

## 计算短期与长期移动平均线

首先，我们需要计算两个指标：短期移动平均线和长期移动平均线。我们以苹果公司（AAPL）的股票数据为例。

以下是计算步骤：
1.  加载苹果公司的股价数据。
2.  计算一个短期窗口（例如10天）的移动平均值。
3.  计算一个长期窗口（例如30天）的移动平均值。

![](img/44e8e259468e99288b55ecdd4bfd6566_11.png)

```python
# 假设 df 是包含苹果股价的DataFrame，其中‘AAPL’是股价列
# 计算短期移动平均线（例如10天窗口）
df[‘M1’] = df[‘AAPL’].rolling(window=10).mean()

![](img/44e8e259468e99288b55ecdd4bfd6566_13.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_15.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_17.png)

# 计算长期移动平均线（例如30天窗口）
df[‘M2’] = df[‘AAPL’].rolling(window=30).mean()
```

**代码解释**：
*   `df[‘AAPL’].rolling(window=10)` 创建了一个窗口大小为10的滚动窗口对象。
*   `.mean()` 对这个滚动窗口内的数据计算平均值，从而得到移动平均线。

![](img/44e8e259468e99288b55ecdd4bfd6566_19.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_21.png)

## 可视化移动平均线与交叉信号

![](img/44e8e259468e99288b55ecdd4bfd6566_23.png)

计算完移动平均线后，我们可以将它们与原始股价一起绘制在图表上，直观地观察交叉点。

![](img/44e8e259468e99288b55ecdd4bfd6566_25.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_27.png)

以下是绘图步骤：
1.  绘制原始股价曲线。
2.  绘制短期移动平均线（M1）。
3.  绘制长期移动平均线（M2）。

```python
import matplotlib.pyplot as plt

# 绘制图表
plt.figure(figsize=(12, 6))
plt.plot(df[‘AAPL’], label=‘Apple Stock Price’, alpha=0.5)
plt.plot(df[‘M1’], label=‘Short-term MA (10 days)’, color=‘green’)
plt.plot(df[‘M2’], label=‘Long-term MA (30 days)’, color=‘red’)
plt.title(‘Apple Stock Price with Moving Averages’)
plt.xlabel(‘Date’)
plt.ylabel(‘Price’)
plt.legend()
plt.show()
```

![](img/44e8e259468e99288b55ecdd4bfd6566_29.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_31.png)

为了更清晰地观察交叉信号，我们可以截取一段时间的数据进行绘图。例如，只显示最近250个交易日（大约一年）的数据。

![](img/44e8e259468e99288b55ecdd4bfd6566_33.png)

```python
# 绘制最近250个交易日的数据
recent_data = df[[‘AAPL’, ‘M1’, ‘M2’]].tail(250)
plt.figure(figsize=(12, 6))
plt.plot(recent_data[‘AAPL’], label=‘Apple Stock Price’)
plt.plot(recent_data[‘M1’], label=‘Short-term MA (10 days)’, color=‘green’)
plt.plot(recent_data[‘M2’], label=‘Long-term MA (30 days)’, color=‘red’)
plt.title(‘Recent Apple Stock Price with Moving Averages’)
plt.legend()
plt.show()
```

![](img/44e8e259468e99288b55ecdd4bfd6566_35.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_37.png)

在生成的图表中，我们可以手动识别交叉点：
*   **死亡交叉**：当绿色的短期均线（M1）从上方向下穿过红色的长期均线（M2）时，表明趋势可能转跌，是卖出信号。
*   **黄金交叉**：当绿色的短期均线（M1）从下方向上穿过红色的长期均线（M2）时，表明趋势可能转涨，是买入信号。

![](img/44e8e259468e99288b55ecdd4bfd6566_39.png)

## 自动化标记交叉信号

手动识别交叉点虽然直观，但我们可以通过编程自动标记这些信号，使分析更高效。

![](img/44e8e259468e99288b55ecdd4bfd6566_41.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_43.png)

我们可以创建一个新的列 `Position`，用来标记短期均线相对于长期均线的位置：
*   当 `M1 > M2` 时，标记为 `1`（代表短期趋势强于长期趋势）。
*   当 `M1 < M2` 时，标记为 `-1`（代表短期趋势弱于长期趋势）。

这样，`Position` 值从 `-1` 变为 `1` 的点就是黄金交叉点，从 `1` 变为 `-1` 的点就是死亡交叉点。

![](img/44e8e259468e99288b55ecdd4bfd6566_45.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_47.png)

```python
import numpy as np

![](img/44e8e259468e99288b55ecdd4bfd6566_49.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_51.png)

# 使用np.where创建位置信号
df[‘Position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
```

![](img/44e8e259468e99288b55ecdd4bfd6566_53.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_55.png)

为了更清晰地展示这个信号，我们可以将其绘制在股价图的右侧Y轴上。

![](img/44e8e259468e99288b55ecdd4bfd6566_57.png)

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

# 在左侧Y轴绘制股价和均线
ax1.plot(df[‘AAPL’], label=‘Price’, color=‘blue’, alpha=0.5)
ax1.plot(df[‘M1’], label=‘M1 (10-day)’, color=‘green’)
ax1.plot(df[‘M2’], label=‘M2 (30-day)’, color=‘red’)
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’, color=‘black’)
ax1.tick_params(axis=‘y’, labelcolor=‘black’)
ax1.legend(loc=‘upper left’)

![](img/44e8e259468e99288b55ecdd4bfd6566_59.png)

# 创建右侧Y轴，用于绘制位置信号
ax2 = ax1.twinx()
ax2.plot(df[‘Position’], label=‘Position Signal’, color=‘purple’, linestyle=‘--’)
ax2.set_ylabel(‘Position (1/-1)’, color=‘purple’)
ax2.tick_params(axis=‘y’, labelcolor=‘purple’)
ax2.set_yticks([-1, 1]) # 只显示-1和1两个刻度
ax2.legend(loc=‘upper right’)

![](img/44e8e259468e99288b55ecdd4bfd6566_61.png)

plt.title(‘Apple Stock with Moving Averages and Position Signal’)
plt.show()
```

![](img/44e8e259468e99288b55ecdd4bfd6566_63.png)

在这张图中，紫色的虚线（`Position`）在 `-1` 和 `1` 之间切换。当它从 `-1` 上升到 `1` 时，对应着黄金交叉（买入信号）。当它从 `1` 下降到 `-1` 时，对应着死亡交叉（卖出信号）。

## 总结

![](img/44e8e259468e99288b55ecdd4bfd6566_65.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_67.png)

本节课中我们一起学习了移动平均线在股票分析中的应用。我们首先介绍了如何计算短期和长期移动平均线，然后通过可视化图表识别了“黄金交叉”和“死亡交叉”这两种关键的交易信号。最后，我们通过编写代码自动化生成了位置信号，使得交叉点的识别更加清晰和高效。

![](img/44e8e259468e99288b55ecdd4bfd6566_69.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_71.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_73.png)

核心要点回顾：
1.  **短期移动平均线**：反映近期价格趋势，常用5日或10日窗口。计算公式为 `df[‘Close’].rolling(window=10).mean()`。
2.  **长期移动平均线**：反映长期价格趋势，常用20日或30日窗口。计算公式为 `df[‘Close’].rolling(window=30).mean()`。
3.  **黄金交叉**：短期均线上穿长期均线，视为**买入信号**。
4.  **死亡交叉**：短期均线下穿长期均线，视为**卖出信号**。
5.  **信号自动化**：使用 `np.where` 函数可以自动生成代表多空位置的信号列，公式为 `df[‘Signal’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)`。

![](img/44e8e259468e99288b55ecdd4bfd6566_75.png)

![](img/44e8e259468e99288b55ecdd4bfd6566_77.png)

通过本节的实践，你已经掌握了利用移动平均线进行基础趋势判断和信号识别的方法，这是构建更复杂量化交易策略的重要基石。