# Python量化50：P1：基于移动平均线的简单交易策略 📈

在本节课中，我们将学习一个基于移动平均线的简单交易策略。这个策略仅遵循两个核心规则，却能产生正向回报。我们将学习如何用Python实现它，并探讨如何通过调整参数和结合交易管理方法来优化策略表现。

## 概述

我们将构建一个基于指数移动平均线的趋势跟踪策略。策略的核心逻辑是：当价格处于明确的上升趋势时，寻找买入机会；当价格处于明确的下降趋势时，寻找卖出机会。我们将使用Python进行数据加载、指标计算、信号生成和简单的回测。

---

## 数据准备与指标计算 📊

首先，我们需要加载历史价格数据并计算必要的技术指标。

我们使用2003年至2021年之间的欧元/美元4小时图数据。加载数据后，我们计算两个关键指标：
1.  **指数移动平均线**：用于判断市场趋势。
2.  **平均真实波幅**：用于后续定义止损和止盈。

以下是计算指标的代码：

```python
import pandas as pd
import pandas_ta as ta

# 假设 df 是包含‘open‘, ‘high‘, ‘low‘, ‘close‘, ‘volume‘列的DataFrame
# 计算指数移动平均线，长度设为100
df[‘ema‘] = ta.ema(df[‘close‘], length=100)

# 计算平均真实波幅，长度默认为14
df[‘atr‘] = ta.atr(df[‘high‘], df[‘low‘], df[‘close‘], length=14)
```

![](img/fc3eea15802ddf2619d0eb7647d54214_1.png)

上一节我们介绍了数据准备，本节中我们来看看如何定义趋势信号。

---

## 定义趋势信号 🧭

趋势信号由`ema_signal`函数生成。该函数检查当前蜡烛之前的一定数量的蜡烛，是否全部位于EMA曲线上方或下方，以此来判断是否存在明确的上升或下降趋势。

函数定义如下：

```python
def ema_signal(dataframe, current_candle, lookback_candles):
    """
    判断趋势方向。
    参数:
        dataframe: 包含‘ema‘列的数据框。
        current_candle: 当前蜡烛的索引。
        lookback_candles: 需要回顾的蜡烛数量。
    返回:
        1: 上升趋势 (所有回顾蜡烛在EMA之上)
        2: 下降趋势 (所有回顾蜡烛在EMA之下)
        0: 无明确趋势
    """
    if current_candle < lookback_candles:
        return 0

    # 获取回顾区间内的收盘价和EMA值
    lookback_slice = dataframe.iloc[current_candle - lookback_candles : current_candle]
    closes = lookback_slice[‘close‘].values
    emas = lookback_slice[‘ema‘].values

    # 检查是否所有收盘价都在EMA之上（上升趋势）
    if all(c > e for c, e in zip(closes, emas)):
        return 1
    # 检查是否所有收盘价都在EMA之下（下降趋势）
    elif all(c < e for c, e in zip(closes, emas)):
        return 2
    else:
        return 0
```

我们将这个函数应用到整个数据框，为每一行（蜡烛）生成一个初步的趋势信号。

```python
lookback_for_trend = 40 # 参数：用于判断趋势的回顾蜡烛数
df[‘ema_signal‘] = [ema_signal(df, i, lookback_for_trend) for i in range(len(df))]
```

---

## 生成入场信号 🎯

在确定了趋势方向后，我们需要一个具体的规则来寻找入场点。我们使用“高低突破”方法。

以下是入场信号的生成步骤：

1.  首先，为每一根蜡烛计算其之前一定数量蜡烛的最高价和最低价。
2.  然后，定义`hl_signal`函数。在下降趋势中，如果当前蜡烛的最高价突破了之前一段时期内的最高价，则产生卖出信号。在上升趋势中，如果当前蜡烛的最低价跌破了之前一段时期内的最低价，则产生买入信号。

相关代码如下：

```python
# 参数：用于寻找突破的回顾蜡烛数
lookback_for_break = 8

# 计算每根蜡烛之前 lookback_for_break 根蜡烛的最高价和最低价
df[‘prior_high‘] = df[‘high‘].rolling(window=lookback_for_break).max().shift(1)
df[‘prior_low‘] = df[‘low‘].rolling(window=lookback_for_break).min().shift(1)

def hl_signal(row):
    """
    根据趋势和价格突破生成最终交易信号。
    """
    # 如果处于下降趋势，且当前最高价突破前期高点，则为卖出信号(2)
    if row[‘ema_signal‘] == 2 and row[‘high‘] > row[‘prior_high‘]:
        return 2
    # 如果处于上升趋势，且当前最低价突破前期低点，则为买入信号(1)
    elif row[‘ema_signal‘] == 1 and row[‘low‘] < row[‘prior_low‘]:
        return 1
    else:
        return 0

df[‘final_signal‘] = df.apply(hl_signal, axis=1)
```

此时，`df[‘final_signal‘]`列中的1代表买入信号，2代表卖出信号，0代表无信号。我们可以通过图表直观地验证这些信号是否出现在趋势的关键位置。

---

## 策略回测与交易管理 💹

生成信号只是第一步。要使策略可行，必须与合理的交易管理方法相结合，即定义止损和止盈。

我们主要探讨两种方法：

1.  **基于ATR的追踪止损**：止损位随着价格向有利方向移动而调整，通常与ATR值挂钩。
2.  **固定比率止损止盈**：设置固定的止损距离和止盈距离，例如止盈距离是止损距离的1.5倍。

以下是两种方法的简单代码框架：

```python
# 方法1: 基于ATR的追踪止损 (以卖出信号为例)
atr_factor = 1.0 # 参数：ATR乘数
for i, row in df.iterrows():
    if row[‘final_signal‘] == 2: # 卖出信号
        entry_price = row[‘close‘]
        stop_loss = entry_price + (row[‘atr‘] * atr_factor)
        # ... 后续价格更新和止损逻辑

# 方法2: 固定比率止损止盈 (以买入信号为例)
stop_loss_ratio = 1.0 # 参数：止损距离相对于ATR的比率
take_profit_ratio = 1.5 # 参数：止盈距离相对于止损距离的比率
for i, row in df.iterrows():
    if row[‘final_signal‘] == 1: # 买入信号
        entry_price = row[‘close‘]
        stop_distance = row[‘atr‘] * stop_loss_ratio
        take_profit_distance = stop_distance * take_profit_ratio
        stop_loss_price = entry_price - stop_distance
        take_profit_price = entry_price + take_profit_distance
        # ... 后续逻辑
```

通过回测不同的参数组合（如`lookback_for_trend`, `lookback_for_break`, `atr_factor`, `take_profit_ratio`等），我们可以评估策略的盈利能力，并找到适合特定市场（如外汇、加密货币）的参数集。

---

## 参数影响与策略优化 ⚙️

理解每个参数如何影响策略行为至关重要：

*   **移动平均线长度**：影响趋势判断的灵敏度。较长的EMA（如200）趋势更稳定但信号更滞后；较短的EMA（如50）更灵敏但可能产生更多假信号。
*   **趋势回顾蜡烛数**：即`lookback_for_trend`。该值越大，对趋势的定义越严格，交易信号越少；值越小，模型越敏感，交易更频繁。
*   **突破回顾蜡烛数**：即`lookback_for_break`。影响入场点的选择。较小的值可能导致在小幅波动中过早入场。
*   **止损止盈参数**：直接关系到风险回报比。过紧的止损可能被市场噪音触发，过宽的止损则可能使单笔亏损过大。

优化策略是一个迭代过程，需要在历史数据上进行大量回测，并在不同市场环境中验证其稳健性。

---

## 总结

![](img/fc3eea15802ddf2619d0eb7647d54214_3.png)

本节课中我们一起学习并实现了一个基于移动平均线的简单趋势跟踪策略。我们掌握了以下核心内容：

1.  **策略逻辑**：利用EMA判断市场趋势，结合价格突破前高/前低来生成具体的买卖信号。
2.  **Python实现**：使用`pandas`和`pandas_ta`库计算技术指标，并编写函数生成交易信号。
3.  **交易管理**：认识到止损和止盈设置与信号生成同等重要，并了解了基于ATR和固定比率两种管理方法。
4.  **参数优化**：明白了策略中各个参数（如回顾周期、ATR乘数）的作用，以及如何通过回测来调整它们。

![](img/fc3eea15802ddf2619d0eb7647d54214_5.png)

这个策略的优势在于逻辑简单清晰，易于实现和自动化。然而，它可能在趋势明显的市场中表现良好，在震荡市中效果会打折扣。记住，没有任何策略是万能的，本策略提供的信号应作为交易决策的辅助工具，最终决策仍需交易者审慎判断。你可以下载提供的代码，尝试不同的参数和数据，进一步探索和完善这个策略。