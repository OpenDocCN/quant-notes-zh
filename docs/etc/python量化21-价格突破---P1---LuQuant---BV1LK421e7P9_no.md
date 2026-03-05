# Python量化21：价格突破回测 - P1 - LuQuant

![](img/42c0b68ada7ad0c323115db7d025d28b_1.png)

在本教程中，我们将学习如何开发和回测一个基于价格通道突破的交易策略。我们将从介绍核心概念开始，逐步讲解代码实现，并进行初步的回测分析。

## 概述

我们将专注于开发和回测一个基于价格通道突破的策略。首先，我们会回顾如何检测价格通道，然后实现一个完整的回测流程来评估该策略的长期表现。本教程将使用Python，并提供代码供您下载和实验。

![](img/42c0b68ada7ad0c323115db7d025d28b_3.png)

## 检测价格通道

上一节我们介绍了价格通道的基本概念。本节中，我们来看看如何用代码实现通道的检测。

价格通道的构建基于“分型点”。分型点是指一根蜡烛线的高点同时高于其左右相邻蜡烛线的高点，或者其低点同时低于其左右相邻蜡烛线的低点。

以下是检测分型点的函数 `pivot`：

```python
def pivot(candle_index, window, df):
    """
    检测给定索引的蜡烛是否为分型点。
    参数:
        candle_index: 当前蜡烛的索引
        window: 检查的相邻蜡烛数量
        df: 包含‘High’和‘Low’列的DataFrame
    返回:
        1: 高分型点
        -1: 低分型点
        2: 同时是高分型和低分型点
        0: 不是分型点
    """
    if candle_index < window or candle_index >= len(df) - window:
        return 0

    high = df.loc[candle_index, ‘High’]
    low = df.loc[candle_index, ‘Low’]

    left_highs = df.loc[candle_index-window:candle_index-1, ‘High’].values
    right_highs = df.loc[candle_index+1:candle_index+window, ‘High’].values
    left_lows = df.loc[candle_index-window:candle_index-1, ‘Low’].values
    right_lows = df.loc[candle_index+1:candle_index+window, ‘Low’].values

    is_pivot_high = (high > max(left_highs)) and (high > max(right_highs))
    is_pivot_low = (low < min(left_lows)) and (low < min(right_lows))

    if is_pivot_high and is_pivot_low:
        return 2
    elif is_pivot_high:
        return 1
    elif is_pivot_low:
        return -1
    else:
        return 0
```

我们使用一个滑动窗口（例如45根蜡烛）来寻找足够数量的分型点（例如至少3个高点和3个低点）。然后，对这些点进行线性回归，得到代表通道上下沿的斜率线。

```python
def collect_channels(df, lookback=45, min_pivots=3):
    """
    在数据框的每个窗口内收集通道参数。
    参数:
        df: 包含‘pivot’列的数据框
        lookback: 回看窗口大小
        min_pivots: 需要的最少分型点数量
    返回:
        一个列表，包含每个窗口的通道参数（高点斜率、截距、R²，低点斜率、截距、R²）
    """
    channels = []
    for i in range(lookback, len(df)):
        window_df = df.iloc[i-lookback:i]
        high_pivots = window_df[window_df[‘pivot’] == 1]
        low_pivots = window_df[window_df[‘pivot’] == -1]

        if len(high_pivots) >= min_pivots and len(low_pivots) >= min_pivots:
            # 对高点和低点进行线性回归，计算斜率和R²
            # ... 回归计算代码 ...
            channels.append((high_slope, high_intercept, high_r2, low_slope, low_intercept, low_r2))
        else:
            channels.append(None)
    return channels
```

## 检测突破信号

在定义了价格通道之后，下一步是检测价格何时突破通道。我们的假设是，当价格向上突破上通道时，可能开启上升趋势；当价格向下跌破下通道时，可能开启下降趋势。

以下是突破检测函数 `breakout` 的逻辑：

1.  检查前一根蜡烛：其高点应在通道内，且收盘价应低于通道下沿（对于向下突破）或高于通道上沿（对于向上突破）。
2.  检查当前蜡烛：其开盘价和收盘价都应完全处于通道之外（对于向下突破，都在下沿下方；对于向上突破，都在上沿上方）。

满足以上条件时，函数返回信号：`1` 代表卖出（向下突破），`2` 代表买入（向上突破），否则返回 `0`。

```python
def breakout_signal(row, df):
    """
    检测突破信号。
    参数:
        row: 当前行的数据
        df: 包含‘channel_params’列的数据框
    返回:
        突破信号 (0, 1, 2)
    """
    idx = row.name
    if idx == 0:
        return 0
    prev_row = df.iloc[idx-1]
    channel_params = row[‘channel_params’]

    if channel_params is None:
        return 0

    high_slope, high_intercept, _, low_slope, low_intercept, _ = channel_params

    # 计算前一根蜡烛和当前蜡烛在通道线上的理论价格
    prev_high_line = high_slope * (idx-1) + high_intercept
    prev_low_line = low_slope * (idx-1) + low_intercept
    curr_high_line = high_slope * idx + high_intercept
    curr_low_line = low_slope * idx + low_intercept

    # 向下突破条件
    if (prev_row[‘High’] < prev_high_line and prev_row[‘Close’] < prev_low_line and
        row[‘Open’] < curr_low_line and row[‘Close’] < curr_low_line):
        return 1
    # 向上突破条件
    elif (prev_row[‘Low’] > prev_low_line and prev_row[‘Close’] > prev_high_line and
          row[‘Open’] > curr_high_line and row[‘Close’] > curr_high_line):
        return 2
    else:
        return 0
```

## 执行回测

![](img/42c0b68ada7ad0c323115db7d025d28b_5.png)

现在，我们将突破信号整合到一个简单的回测框架中。我们使用一个初始资金为1000美元、杠杆为50:1的账户，并设置止损和止盈。

以下是策略的核心逻辑：

```python
def strategy_signal(df):
    """从数据框中返回突破信号列"""
    return df[‘breakout’]

# 回测参数
initial_capital = 1000
leverage = 50
stop_loss_ratio = 1.0  # 止损设为信号触发前一根蜡烛的极值
take_profit_ratio = 1.2 # 止盈距离是止损距离的1.2倍

# 简化的交易逻辑（概念性代码）
for i, row in df.iterrows():
    signal = row[‘breakout’]
    if signal == 2 and no_open_position: # 买入信号
        stop_loss_price = df.iloc[i-1][‘Low’]
        take_profit_price = row[‘Close’] + (row[‘Close’] - stop_loss_price) * take_profit_ratio
        # 开立多头仓位
    elif signal == 1 and no_open_position: # 卖出信号
        stop_loss_price = df.iloc[i-1][‘High’]
        take_profit_price = row[‘Close’] - (stop_loss_price - row[‘Close’]) * take_profit_ratio
        # 开立空头仓位
```

## 初步结果与问题分析

![](img/42c0b68ada7ad0c323115db7d025d28b_7.png)

运行上述策略在2003年至2023年的数据上进行回测，初步结果如下：
*   策略总回报：**140%**
*   买入持有回报：**-3.22%**
*   最大回撤：**-77%**
*   胜率：**47%**

![](img/42c0b68ada7ad0c323115db7d025d28b_9.png)

虽然策略在最近几年表现尚可，但巨大的回撤和长期的平庸表现表明其存在问题。

分析可能的原因：
1.  **通道检测的静态性**：我们使用固定的45根蜡烛窗口来检测通道。市场结构是变化的，固定的窗口可能无法适应不同的波动周期。
2.  **回归拟合质量**：我们未对线性回归的拟合优度（R²值）设置阈值。低质量的拟合会导致通道线失真，产生错误信号。
3.  **交易管理简单**：策略使用了固定的止损止盈比例，没有更精细的仓位管理。

![](img/42c0b68ada7ad0c323115db7d025d28b_11.png)

## 策略优化方向

为了提高策略性能，我们可以从以下几个方面进行优化：

以下是几个关键的优化参数：
*   **`lookback`（回看窗口）**：尝试30、35、60等不同值，或使其动态适应市场波动率。
*   **`window`（分型检测邻域）**：在日线图上，可以尝试从3增加到5。
*   **`min_pivots`（最少分型点数）**：尝试不同的高低点组合，如(3,2), (4,3)等。
*   **回归拟合R²阈值**：为高点和低点的回归线设置最低R²要求（例如0.8），以过滤掉拟合效果差的通道。

此外，改进交易管理能显著提升效果。例如，一个简单的改进是使用分步止盈：

```python
# 概念性代码：分步止盈
# 假设总仓位为2个单位
take_profit_1_distance = (take_profit_price - entry_price) / 3
take_profit_1_price = entry_price + take_profit_1_distance # 对于多头
# 当价格达到 take_profit_1_price 时，平仓1个单位
# 剩余1个单位的仓位保持原止盈目标
```

在初步测试中，仅加入这个简单的两步止盈管理，就将总回报从140%提升到了200%。

## 总结

![](img/42c0b68ada7ad0c323115db7d025d28b_13.png)

![](img/42c0b68ada7ad0c323115db7d025d28b_15.png)

本节课中我们一起学习了如何构建和回测一个基于价格通道突破的交易策略。我们实现了分型点检测、通道线拟合、突破信号生成以及基本的回测流程。初步结果显示该策略原始版本表现不佳，存在回撤大、信号质量不稳定等问题。我们分析了可能的原因，包括通道检测的静态性和交易管理的缺失，并探讨了多个优化方向，如参数调整、增加拟合质量过滤以及实施更先进的仓位管理技术。记住，一个成功的量化策略不仅依赖于精准的信号，也离不开严谨的风险与资金管理。