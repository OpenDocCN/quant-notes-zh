# Python金融量化与股票交易：P8：9.9.3：短均与长均计算实例 📈

在本节课中，我们将学习一个基础的股票分析策略。我们将通过计算短期和长期移动平均线，来识别市场中的“黄金交叉”和“死亡交叉”信号，并学习如何用代码实现和可视化这些指标。

![](img/2f1fc1b6237382725e397af031dcf7fd_1.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_2.png)

上一节我们介绍了数据获取与处理的基础知识，本节中我们来看看如何应用这些数据构建一个简单的技术分析策略。

![](img/2f1fc1b6237382725e397af031dcf7fd_3.png)

## 策略概述：移动平均线交叉

![](img/2f1fc1b6237382725e397af031dcf7fd_5.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_6.png)

这个分析策略的核心是计算两个关键指标：短期移动平均线和长期移动平均线。通过观察这两条线的交叉点，我们可以判断市场的潜在趋势变化。
*   **黄金交叉**：短期均线从下向上穿越长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿越长期均线，通常被视为**卖出信号**。

## 第一步：计算短期与长期移动平均线

首先，我们需要从数据中计算出短期和长期的移动平均值。我们以苹果公司（AAPL）的股票数据为例。

![](img/2f1fc1b6237382725e397af031dcf7fd_8.png)

以下是计算步骤：
1.  选择目标股票数据列。
2.  使用 `rolling()` 方法指定一个时间窗口。
3.  使用 `mean()` 方法计算该窗口内数据的平均值。

![](img/2f1fc1b6237382725e397af031dcf7fd_10.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_11.png)

```python
# 假设 `data` 是一个包含苹果股价的DataFrame，列名为 ‘AAPL‘
# 计算短期移动平均线（例如：10天窗口）
data[‘M1‘] = data[‘AAPL‘].rolling(window=10).mean()

# 计算长期移动平均线（例如：30天窗口）
data[‘M2‘] = data[‘AAPL‘].rolling(window=30).mean()

![](img/2f1fc1b6237382725e397af031dcf7fd_13.png)

# 查看包含原始股价和两条均线的数据前几行
print(data[[‘AAPL‘, ‘M1‘, ‘M2‘]].head())
```

![](img/2f1fc1b6237382725e397af031dcf7fd_15.png)

**代码说明**：
*   `window=10`：表示计算最近10个交易日的移动平均值。
*   `data[‘M1‘]`：将计算出的短期均线结果存储在新列 ‘M1‘ 中。
*   实际应用中，短期窗口常设为5或10天，长期窗口常设为20或30天，代表一个月左右的交易日。

![](img/2f1fc1b6237382725e397af031dcf7fd_16.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_17.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_18.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_19.png)

## 第二步：可视化股价与移动平均线

计算出指标后，最直观的方法是将其绘制成图表进行观察。

```python
import matplotlib.pyplot as plt

# 绘制股价与两条移动平均线
data[[‘AAPL‘, ‘M1‘, ‘M2‘]].plot(figsize=(12, 6))
plt.title(‘Apple Stock Price with Moving Averages‘)
plt.show()
```

![](img/2f1fc1b6237382725e397af031dcf7fd_21.png)

**图表解读**：
在生成的图表中，通常：
*   蓝色线代表原始股价。
*   绿色线代表短期移动平均线（M1）。
*   红色线代表长期移动平均线（M2）。
观察绿色线与红色线的交叉点，可以初步判断“黄金交叉”和“死亡交叉”的位置。

![](img/2f1fc1b6237382725e397af031dcf7fd_22.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_23.png)

## 第三步：量化交叉信号

![](img/2f1fc1b6237382725e397af031dcf7fd_25.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_26.png)

为了更精确地定位交叉点，我们可以创建一个信号列。当短期均线高于长期均线时，标记为 `1`；当低于时，标记为 `-1`。这能清晰显示出趋势的切换。

![](img/2f1fc1b6237382725e397af031dcf7fd_27.png)

以下是创建信号列的步骤：
1.  使用 `np.where` 函数进行比较。
2.  将结果存储为新列。

```python
import numpy as np

![](img/2f1fc1b6237382725e397af031dcf7fd_29.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_30.png)

# 生成交易信号：短期均线 > 长期均线时为1，否则为-1
data[‘position‘] = np.where(data[‘M1‘] > data[‘M2‘], 1, -1)

![](img/2f1fc1b6237382725e397af031dcf7fd_31.png)

# 查看信号
print(data[‘position‘].value_counts())
```

![](img/2f1fc1b6237382725e397af031dcf7fd_33.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_34.png)

## 第四步：高级可视化（双Y轴）

![](img/2f1fc1b6237382725e397af031dcf7fd_36.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_38.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_39.png)

由于股价和信号值（1或-1）的量纲差异巨大，直接绘制在同一坐标轴上会导致信号线难以观察。我们可以使用双Y轴来解决这个问题。

![](img/2f1fc1b6237382725e397af031dcf7fd_41.png)

```python
# 创建图形和主Y轴
fig, ax1 = plt.subplots(figsize=(14, 7))

# 在主Y轴上绘制股价和均线
ax1.plot(data.index, data[‘AAPL‘], label=‘AAPL Price‘, alpha=0.5)
ax1.plot(data.index, data[‘M1‘], label=‘Short MA (10)‘, color=‘green‘)
ax1.plot(data.index, data[‘M2‘], label=‘Long MA (30)‘, color=‘red‘)
ax1.set_ylabel(‘Price‘)
ax1.legend(loc=‘upper left‘)

# 创建次Y轴，用于绘制信号
ax2 = ax1.twinx()
ax2.plot(data.index, data[‘position‘], label=‘Signal (1/-1)‘, color=‘purple‘, linestyle=‘--‘)
ax2.set_ylabel(‘Signal‘)
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc=‘upper right‘)

![](img/2f1fc1b6237382725e397af031dcf7fd_43.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_44.png)

plt.title(‘Apple Stock with Moving Averages and Trading Signals‘)
plt.show()
```

![](img/2f1fc1b6237382725e397af031dcf7fd_46.png)

**图表解读**：
*   **主Y轴（左侧）**：显示股价和两条移动平均线。
*   **次Y轴（右侧）**：显示信号线，其值在 `1` 和 `-1` 之间跳动。
*   **信号线从 -1 变为 1**：对应**黄金交叉**（买入信号）。
*   **信号线从 1 变为 -1**：对应**死亡交叉**（卖出信号）。

通过这张图，我们可以非常清晰地看到所有交叉信号发生的时间点。

## 总结

![](img/2f1fc1b6237382725e397af031dcf7fd_48.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_49.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_50.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_51.png)

本节课中我们一起学习了如何构建一个基于移动平均线交叉的简单交易策略。我们掌握了以下核心技能：
1.  **计算移动平均线**：使用 `DataFrame.rolling().mean()` 方法计算短期和长期均值。
2.  **理解交叉信号**：“黄金交叉”是潜在的买入机会，“死亡交叉”是潜在的卖出警示。
3.  **可视化分析**：绘制股价与均线图进行直观分析。
4.  **量化信号**：利用 `np.where` 将交叉逻辑转化为明确的 `1` / `-1` 信号。
5.  **高级图表绘制**：使用双Y轴在同一张图上清晰展示价格走势和交易信号。

![](img/2f1fc1b6237382725e397af031dcf7fd_52.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_53.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_54.png)

这个策略是技术分析的基石之一。请注意，这是一个简化示例，实际交易中需要结合更多指标和风险管理措施。