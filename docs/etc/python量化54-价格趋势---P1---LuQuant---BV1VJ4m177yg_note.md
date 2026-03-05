# Python量化54：价格趋势 - P1 - LuQuant

## 概述
在本节课中，我们将学习如何使用Python构建一个算法，用于检测金融价格图表中的趋势通道。我们将从基础概念讲起，逐步实现一个动态识别价格通道的程序，并探讨其背后的逻辑和优化方法。

![](img/24a9c029b58966a2bf582520892ee1d6_1.png)

---

## 价格趋势通道算法：1：核心概念与挑战

![](img/24a9c029b58966a2bf582520892ee1d6_3.png)

![](img/24a9c029b58966a2bf582520892ee1d6_4.png)

上一节我们概述了课程目标，本节中我们来看看算法的核心思想以及在实际应用中面临的挑战。

该算法的目标是识别价格图表中由两条平行或近似平行的趋势线构成的通道。然而，金融市场是动态且非线性的，这意味着不存在一套固定的参数能永远完美预测趋势。趋势本身及其变化速度都可能随时改变。

因此，我们构建的算法基于一个简单的构想：通过分析历史价格数据，动态地拟合出描述价格波动上下边界的直线。

---

## 价格趋势通道算法：2：算法基本思路

上一节我们介绍了市场预测的复杂性，本节中我们来看看算法的具体实现思路。

![](img/24a9c029b58966a2bf582520892ee1d6_6.png)

算法的基本步骤如下：
1.  选择一个特定的时间点（例如某根K线）。
2.  回顾该点之前一定数量的K线（例如30根）。
3.  将这个“回顾窗口”平均分成若干个小段。
4.  在每个小段内，找出价格的最高点（`最大值`）和最低点（`最小值`）。
5.  对所有`最小值`点进行**一阶多项式拟合**（即线性拟合），得到一条描述价格底部趋势的直线。
6.  对所有`最大值`点进行同样的拟合，得到一条描述价格顶部趋势的直线。

用公式表示，拟合得到的直线为：
`y = slope * x + intercept`
其中，`slope`是斜率，`intercept`是截距。

此时得到的两条直线可能并未将价格“包裹”在通道内，因为拟合线会穿过这些点的中心。因此，我们需要对截距进行调整。

---

## 价格趋势通道算法：3：调整通道边界

上一节我们得到了初步的趋势线，本节中我们来看看如何调整它们以形成有效的价格通道。

调整的目标是让两条趋势线更好地界定价格的波动范围。以下是两种常见方法：

以下是两种调整截距的方法：

1.  **穿过极值点**：调整截距，使直线穿过整个回顾窗口内的**全局最低点**（对于底部线）和**全局最高点**（对于顶部线）。这确保了通道触及价格的极端位置。
2.  **包裹价格区间**：调整截距，使直线移动到恰好能“包裹”住所有价格点的位置。具体做法是计算价格点与初始拟合线之间的最大距离，然后将直线平移该距离，使其成为价格的支撑线或阻力线。

用代码概念描述第二种方法的调整过程：
```python
# 对于底部线 (min_slope 已通过拟合得到)
adjusted_min_intercept = min_intercept - max(min_low_values - (min_slope * corresponding_indices))
# 对于顶部线 (max_slope 已通过拟合得到)
adjusted_max_intercept = max_intercept + max((max_slope * corresponding_indices) - max_high_values)
```

另一个关键问题是“回顾窗口”的长度（即回顾多少根K线）会显著影响通道的形态。固定的窗口长度无法适应不同时间长度的趋势。

---

## 价格趋势通道算法：4：动态优化窗口长度

上一节我们讨论了固定窗口的局限性，本节中我们引入动态优化来选择最合适的回顾窗口长度。

为了解决窗口长度问题，我们让程序在一个指定范围内测试不同的回顾窗口值。例如，设置基准长度为30，浮动范围为±20，那么程序将测试从10到50之间不同长度的窗口。

程序的目标是寻找一个窗口长度，使得在该长度下计算出的顶部线和底部线**最接近平行**，因为平行的趋势线是通道的典型特征。

以下是实现动态优化的逻辑步骤：

1.  定义基准回顾长度（`base_lookback`）和浮动范围（`lookback_range`）。
2.  在 `[base_lookback - lookback_range, base_lookback + lookback_range]` 区间内循环测试每个可能的回顾长度。
3.  对每个长度，执行前述的找点、拟合、调整步骤，得到一对趋势线。
4.  计算这两条趋势线斜率的绝对差值 `slope_diff = abs(max_slope - min_slope)`。
5.  选择使 `slope_diff` 最小的那个回顾长度作为“最优长度”，并记录其对应的趋势线参数。

通过这种方式，算法能自适应地找到最可能形成价格通道的局部历史数据范围。

---

## 价格趋势通道算法：5：代码实现与解析

上一节我们理清了算法的逻辑流程，本节中我们结合代码片段来具体实现。

首先，我们需要导入必要的库并加载价格数据。
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 加载数据
df = pd.read_csv('eurusd_4h.csv')
df.columns = ['time', 'open', 'high', 'low', 'close', 'volume']
# 清理无效数据
df = df[df['volume'] != 0]
df.reset_index(drop=True, inplace=True)
```

接下来，我们实现核心函数，用于在指定蜡烛ID（`candle_id`）和回顾长度（`lookback`）下计算通道。
```python
def calculate_channel(candle_id, lookback, window_size=5):
    """
    计算在指定蜡烛和回顾长度下的趋势通道。
    candle_id: 当前分析的蜡烛索引
    lookback: 回顾的蜡烛数量
    window_size: 将回顾窗口分段的每段大小
    """
    start_idx = max(0, candle_id - lookback)
    indices = np.arange(start_idx, candle_id + 1)

    min_vals, max_vals = [], []
    min_idxs, max_idxs = [], []

    # 分段寻找局部最小值和最大值
    for i in range(start_idx, candle_id + 1, window_size):
        slice_end = min(i + window_size, candle_id + 1)
        if slice_end - i < 2:  # 段内数据不足
            continue
        slice_lows = df['low'].iloc[i:slice_end]
        slice_highs = df['high'].iloc[i:slice_end]

        min_idx = slice_lows.idxmin()
        max_idx = slice_highs.idxmax()

        min_vals.append(df.at[min_idx, 'low'])
        max_vals.append(df.at[max_idx, 'high'])
        min_idxs.append(min_idx)
        max_idxs.append(max_idx)

    # 转换为numpy数组以便拟合
    min_idxs = np.array(min_idxs)
    min_vals = np.array(min_vals)
    max_idxs = np.array(max_idxs)
    max_vals = np.array(max_vals)

    # 一阶多项式拟合 (直线)
    min_slope, min_intercept = np.polyfit(min_idxs, min_vals, 1)
    max_slope, max_intercept = np.polyfit(max_idxs, max_vals, 1)

    # 调整截距以包裹价格
    # 计算价格点与初始拟合线的最大距离
    min_distances = min_vals - (min_slope * min_idxs + min_intercept)
    max_distances = (max_slope * max_idxs + max_intercept) - max_vals

    adjusted_min_intercept = min_intercept + np.min(min_distances)
    adjusted_max_intercept = max_intercept - np.min(max_distances)

    return (min_slope, adjusted_min_intercept, max_slope, adjusted_max_intercept, min_idxs, max_idxs)
```

然后，我们实现优化函数，动态选择最佳回顾长度。
```python
def optimize_lookback(candle_id, base_lookback=30, lookback_range=20, window_size=5):
    """
    优化寻找最佳回顾长度。
    """
    best_slope_diff = float('inf')
    best_lookback = base_lookback
    best_channel_params = None

    for test_lookback in range(base_lookback - lookback_range, base_lookback + lookback_range + 1):
        if test_lookback < window_size * 2:  # 确保有足够数据分段
            continue

        params = calculate_channel(candle_id, test_lookback, window_size)
        min_slope, _, max_slope, _, _, _ = params

        slope_diff = abs(max_slope - min_slope)

        if slope_diff < best_slope_diff:
            best_slope_diff = slope_diff
            best_lookback = test_lookback
            best_channel_params = params

    print(f"最优回顾长度: {best_lookback}")
    return best_lookback, best_channel_params
```

最后，我们可以使用函数并可视化结果。
```python
# 选择要分析的蜡烛ID
candle_to_analyze = 3000
optimal_lookback, (min_s, min_i, max_s, max_i, min_idx, max_idx) = optimize_lookback(candle_to_analyze)

# 准备绘图数据
plot_indices = np.arange(candle_to_analyze - optimal_lookback, candle_to_analyze + 1)
min_line = min_s * plot_indices + min_i
max_line = max_s * plot_indices + max_i

# 绘图
plt.figure(figsize=(15, 7))
plt.plot(df['close'].iloc[plot_indices[0]:candle_to_analyze + 1].values, label='Close Price', alpha=0.5)
plt.plot(plot_indices - plot_indices[0], min_line, label='Min Slope (Support)', color='green', linewidth=2)
plt.plot(plot_indices - plot_indices[0], max_line, label='Max Slope (Resistance)', color='red', linewidth=2)
plt.fill_between(plot_indices - plot_indices[0], min_line, max_line, alpha=0.2, color='gray')
plt.title(f'Price Channel at Candle {candle_to_analyze}')
plt.legend()
plt.show()
```

---

## 价格趋势通道算法：6：策略应用与总结

上一节我们完成了算法的代码实现，本节中我们简要探讨其交易应用并总结全课。

识别出价格通道后，交易者可以基于此开发策略。以下是两种常见思路：

以下是基于价格通道的两种基本交易思路：

1.  **通道内交易**：当价格接近通道下轨（支撑线）并出现看涨反转信号（如锤子线、看涨吞没）时，考虑买入。止损设在下轨下方，止盈可以设在上轨附近或采用风险回报比（如1:2）。
2.  **通道突破交易**：等待价格显著突破通道上轨（可能预示上涨加速）或下轨（可能预示趋势反转），并顺着突破方向入场。这需要额外的确认过滤器来减少假突破。

![](img/24a9c029b58966a2bf582520892ee1d6_8.png)

![](img/24a9c029b58966a2bf582520892ee1d6_10.png)

本节课中我们一起学习了如何从零开始构建一个动态价格趋势通道检测算法。我们涵盖了从核心概念、拟合调整、动态优化窗口到代码实现的完整过程。记住，没有任何算法是万能的，本课提供的是一种分析思路和实现框架。你可以在此基础上，结合自己的交易逻辑，调整参数（如分段窗口大小、优化条件），或加入更多过滤器（如波动率、成交量），以构建更适应特定市场环境的交易系统。