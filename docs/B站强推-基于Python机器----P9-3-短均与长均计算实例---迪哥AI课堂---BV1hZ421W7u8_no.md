# 金融量化分析实战：P9：短均与长均计算实例 📈

![](img/209ec92fdb080e78bb95ded4ecc21d70_1.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的交易策略。这个策略的核心是识别“黄金交叉”和“死亡交叉”，它们是判断买入和卖出时机的常用技术指标。

![](img/209ec92fdb080e78bb95ded4ecc21d70_3.png)

上一节我们介绍了数据处理的基础，本节中我们来看看如何具体计算移动平均线并应用于策略分析。

![](img/209ec92fdb080e78bb95ded4ecc21d70_5.png)

## 策略概述与核心概念

![](img/209ec92fdb080e78bb95ded4ecc21d70_7.png)

我们将分析股票价格序列，通过计算两个关键指标来判断交易时机：
*   **短期移动平均线 (Short-term Moving Average)**：通常取较短的时间窗口（如5天、10天）计算的平均价格，能快速反映近期价格变化。
*   **长期移动平均线 (Long-term Moving Average)**：通常取较长的时间窗口（如20天、30天）计算的平均价格，能平滑价格波动，反映长期趋势。

![](img/209ec92fdb080e78bb95ded4ecc21d70_9.png)

当短期均线从下方上穿长期均线时，形成 **“黄金交叉”** ，通常被视为买入信号。当短期均线从上方下穿长期均线时，形成 **“死亡交叉”** ，通常被视为卖出信号。

## 计算短期与长期移动平均线

以下是计算移动平均线的具体步骤。我们将以苹果公司（AAPL）的股票数据为例进行演示。

![](img/209ec92fdb080e78bb95ded4ecc21d70_11.png)

首先，我们需要导入数据并指定要分析的股票列。假设我们的数据已加载到名为 `data` 的 DataFrame 中，其中包含 `AAPL` 列。

![](img/209ec92fdb080e78bb95ded4ecc21d70_13.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_15.png)

**第一步：计算短期移动平均线**
我们使用 `rolling` 方法结合 `mean` 函数来计算指定窗口内的平均值。这里我们以10天作为短期窗口。

![](img/209ec92fdb080e78bb95ded4ecc21d70_17.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_19.png)

```python
# 计算10日移动平均线（短期）
data['M1_10day'] = data['AAPL'].rolling(window=10).mean()
```

![](img/209ec92fdb080e78bb95ded4ecc21d70_21.png)

**第二步：计算长期移动平均线**
同样使用 `rolling` 和 `mean` 方法，但窗口设置为30天。

```python
# 计算30日移动平均线（长期）
data['M2_30day'] = data['AAPL'].rolling(window=30).mean()
```

![](img/209ec92fdb080e78bb95ded4ecc21d70_23.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_25.png)

代码说明：
*   `data[‘AAPL’]`：指定我们要计算移动平均的股价数据列。
*   `.rolling(window=10)`：创建一个滚动窗口对象，窗口大小为10（即每次计算基于最近的10个数据点）。
*   `.mean()`：对窗口内的数据计算算术平均值。

![](img/209ec92fdb080e78bb95ded4ecc21d70_27.png)

执行上述代码后，我们的 DataFrame 中会新增两列：`M1_10day` 和 `M2_30day`，分别存储了短期和长期的移动平均值。

![](img/209ec92fdb080e78bb95ded4ecc21d70_29.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_31.png)

## 可视化移动平均线与股价

计算出指标后，最直观的方式是通过图表进行观察。我们将股价、短期均线和长期均线绘制在同一张图上。

以下是绘制折线图的代码：

```python
import matplotlib.pyplot as plt

![](img/209ec92fdb080e78bb95ded4ecc21d70_33.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_35.png)

# 设置图形大小
plt.figure(figsize=(12, 6))

![](img/209ec92fdb080e78bb95ded4ecc21d70_37.png)

# 绘制苹果股价
plt.plot(data['AAPL'], label='AAPL Price', alpha=0.5)

![](img/209ec92fdb080e78bb95ded4ecc21d70_39.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_41.png)

# 绘制10日移动平均线（短期）
plt.plot(data['M1_10day'], label='10-day MA (Short)', color='green')

![](img/209ec92fdb080e78bb95ded4ecc21d70_43.png)

# 绘制30日移动平均线（长期）
plt.plot(data['M2_30day'], label='30-day MA (Long)', color='red')

plt.title('AAPL Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

通过观察图表，我们可以手动寻找短期均线（绿线）与长期均线（红线）交叉的点。交叉点附近就是潜在的交易信号区域。

![](img/209ec92fdb080e78bb95ded4ecc21d70_45.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_47.png)

## 识别黄金交叉与死亡交叉

![](img/209ec92fdb080e78bb95ded4ecc21d70_49.png)

为了更精确、自动化地识别交叉信号，我们可以通过数值比较来生成一个交易信号序列。

![](img/209ec92fdb080e78bb95ded4ecc21d70_51.png)

**核心思路**：我们创建一个新的列（例如 `Position`），当短期均线大于长期均线时，标记为 `1`；当短期均线小于长期均线时，标记为 `-1`。`Position` 值从 `-1` 变为 `1` 的点，对应 **黄金交叉（买入信号）**；从 `1` 变为 `-1` 的点，对应 **死亡交叉（卖出信号）**。

![](img/209ec92fdb080e78bb95ded4ecc21d70_53.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_55.png)

以下是使用 `numpy.where` 函数实现该逻辑的代码：

![](img/209ec92fdb080e78bb95ded4ecc21d70_57.png)

```python
import numpy as np

![](img/209ec92fdb080e78bb95ded4ecc21d70_59.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_61.png)

# 生成交易信号：短期均线 > 长期均线时为1，否则为-1
data['Position'] = np.where(data['M1_10day'] > data['M2_30day'], 1, -1)
```

为了更清晰地展示信号与股价的关系，我们可以将 `Position` 序列绘制在次坐标轴上。

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/209ec92fdb080e78bb95ded4ecc21d70_63.png)

# 主坐标轴：绘制股价和移动平均线
ax1.plot(data['AAPL'], label='AAPL Price', color='blue', alpha=0.3)
ax1.plot(data['M1_10day'], label='10-day MA', color='green')
ax1.plot(data['M2_30day'], label='30-day MA', color='red')
ax1.set_xlabel('Date')
ax1.set_ylabel('Price')
ax1.legend(loc='upper left')

![](img/209ec92fdb080e78bb95ded4ecc21d70_65.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_67.png)

# 创建次坐标轴
ax2 = ax1.twinx()
# 次坐标轴：绘制交易信号 Position
ax2.plot(data['Position'], label='Trading Signal (Position)', color='purple', linestyle='--')
ax2.set_ylabel('Signal (1/-1)')
ax2.legend(loc='upper right')

plt.title('AAPL Price, Moving Averages and Trading Signals')
plt.show()
```

在这张图中，紫色虚线（对应右侧坐标轴）在 `1` 和 `-1` 之间切换。当它从 `-1` 向上突破到 `1` 时，对应图表中绿色短期均线上穿红色长期均线的点，即 **黄金交叉**。反之，从 `1` 向下跌破到 `-1` 时，对应 **死亡交叉**。

![](img/209ec92fdb080e78bb95ded4ecc21d70_69.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_71.png)

## 关键点总结

![](img/209ec92fdb080e78bb95ded4ecc21d70_73.png)

本节课中我们一起学习了如何构建一个基于移动平均线的简单量化交易策略：

![](img/209ec92fdb080e78bb95ded4ecc21d70_75.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_77.png)

1.  **计算指标**：使用 Pandas 的 `rolling().mean()` 方法，可以方便地计算任意窗口大小的移动平均线。短期窗口（如10日）反应灵敏，长期窗口（如30日）趋势稳定。
2.  **识别信号**：策略的核心是观察短期均线与长期均线的交叉点。
    *   **黄金交叉**：短期均线上穿长期均线，是潜在的 **买入信号**。
    *   **死亡交叉**：短期均线下穿长期均线，是潜在的 **卖出信号**。
3.  **自动化处理**：通过 `np.where` 进行条件判断，可以自动生成交易信号序列（`Position`），使交叉点的识别更加清晰和程序化。
4.  **可视化分析**：使用 Matplotlib 进行多序列绘图，特别是利用双坐标轴，可以有效地在同一张图上展示股价走势、技术指标和交易信号，便于直观分析。

![](img/209ec92fdb080e78bb95ded4ecc21d70_79.png)

![](img/209ec92fdb080e78bb95ded4ecc21d70_81.png)

这个实例展示了如何将基础的金融概念转化为可执行的 Python 代码，是量化分析入门的重要一步。在实际应用中，你可以调整移动平均线的窗口参数，或结合其他指标来优化策略。