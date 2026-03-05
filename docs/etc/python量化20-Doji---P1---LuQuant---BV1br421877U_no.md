# Python量化20：P1：十字星与吞没形态结合布林带的策略回测 📊

![](img/0ff43535dc1073d6f5d296b28b5ff14f_1.png)

![](img/0ff43535dc1073d6f5d296b28b5ff14f_2.png)

在本节课中，我们将学习如何识别和利用两种经典的K线形态——十字星和吞没形态，并结合布林带指标，构建一个简单的技术交易策略。我们将使用Python对过去一年的欧元/美元小时数据进行回测，以评估策略的有效性。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_4.png)

## 概述

![](img/0ff43535dc1073d6f5d296b28b5ff14f_5.png)

![](img/0ff43535dc1073d6f5d296b28b5ff14f_6.png)

我们将首先介绍十字星和吞没形态的基本概念，然后讲解如何将它们与布林带结合生成交易信号。接着，我们会用Python实现信号检测、可视化以及完整的策略回测流程。最后，我们将对比不同信号组合的回测结果，并讨论策略的优化方向。

---

![](img/0ff43535dc1073d6f5d296b28b5ff14f_8.png)

## 1️⃣：核心概念与策略逻辑

上一节我们概述了课程内容，本节中我们来看看策略所依赖的核心概念和基本逻辑。

### 十字星形态
十字星是一种特殊的K线形态，其开盘价与收盘价非常接近或相等，实体部分极短，形似“十字”。它通常出现在趋势的转折点，表示市场犹豫不决。
*   **看涨十字星模式**：通常出现在下跌趋势中，形态序列为：一根**看跌蜡烛** -> 一根**十字星蜡烛** -> 一根**看涨蜡烛**。这预示着趋势可能由跌转升。
*   **看跌十字星模式**：通常出现在上涨趋势中，形态序列为：一根**看涨蜡烛** -> 一根**十字星蜡烛** -> 一根**看跌蜡烛**。这预示着趋势可能由升转跌。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_10.png)

### 吞没形态
吞没形态由两根连续的K线组成，后一根K线的实体完全“吞没”前一根K线的实体，不考虑影线部分。
*   **看涨吞没**：出现在下跌趋势末端。一根**看跌蜡烛**之后，出现一根实体更大的**看涨蜡烛**，且后者的开盘价低于前者的收盘价，收盘价高于前者的开盘价。
*   **看跌吞没**：出现在上涨趋势末端。一根**看涨蜡烛**之后，出现一根实体更大的**看跌蜡烛**，且后者的开盘价高于前者的收盘价，收盘价低于前者的开盘价。

### 结合布林带的交易逻辑
布林带由中轨（移动平均线）、上轨和下轨组成，用于衡量价格波动性。我们的策略逻辑如下：
1.  等待价格在布林带特定区域形成上述形态。
2.  如果在**布林带下轨附近**形成**看涨形态**（十字星或吞没），则产生**买入信号**。
3.  如果在**布林带上轨附近**形成**看跌形态**（十字星或吞没），则产生**卖出信号**。
4.  根据信号入场，并设置固定的止损和止盈。

---

## 2️⃣：数据准备与指标计算

![](img/0ff43535dc1073d6f5d296b28b5ff14f_12.png)

上一节我们介绍了策略的核心逻辑，本节中我们来看看如何准备数据并计算所需的指标。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_14.png)

以下是数据获取与布林带计算的步骤：

1.  **导入库**：使用 `yfinance` 下载历史数据，使用 `pandas` 进行数据处理，使用 `ta` 库计算技术指标。
2.  **下载数据**：获取欧元/美元（EURUSD=X）在指定时间段内的小时级别K线数据。
3.  **计算布林带**：使用 `ta.volatility.BollingerBands` 函数，设置周期为30，标准差为1.5，计算出布林带的上轨、中轨和下轨。

```python
import yfinance as yf
import pandas as pd
import ta

# 下载数据
data = yf.download('EURUSD=X', start='2021-04-01', end='2023-03-19', interval='1h')

![](img/0ff43535dc1073d6f5d296b28b5ff14f_16.png)

# 计算布林带
bb_indicator = ta.volatility.BollingerBands(close=data['Close'], window=30, window_dev=1.5)
data['bb_upper'] = bb_indicator.bollinger_hband() # 上轨
data['bb_middle'] = bb_indicator.bollinger_mavg() # 中轨
data['bb_lower'] = bb_indicator.bollinger_lband() # 下轨
```

---

![](img/0ff43535dc1073d6f5d296b28b5ff14f_18.png)

## 3️⃣：信号生成函数

在准备好数据和基础指标后，我们需要编写函数来识别交易信号。本节将分别创建检测十字星信号和吞没信号的函数。

### 十字星信号函数
该函数检查每一根K线是否满足结合布林带的十字星形态条件。

```python
def bb_doji_signal(open, high, low, close, bb_lower, bb_middle, bb_upper):
    # 初始化信号为0（无信号）
    signal = 0

    # 条件1: 看涨十字星信号
    # 价格位于布林带下半部分（收盘价高于下轨但低于中轨）
    condition1_bull = (close > bb_lower) & (close < bb_middle)
    # 当前为看涨蜡烛
    condition2_bull = close > open
    # 前一根为十字星蜡烛（开盘价约等于收盘价）
    condition3_bull = abs(open.shift(1) - close.shift(1)) / (high.shift(1) - low.shift(1) + 1e-9) < 0.1
    # 再前一根为看跌蜡烛
    condition4_bull = close.shift(2) < open.shift(2)

    if condition1_bull and condition2_bull and condition3_bull and condition4_bull:
        signal = 2  # 买入信号

    # 条件2: 看跌十字星信号
    # 价格位于布林带上半部分（收盘价低于上轨但高于中轨）
    condition1_bear = (close < bb_upper) & (close > bb_middle)
    # 当前为看跌蜡烛
    condition2_bear = close < open
    # 前一根为十字星蜡烛
    condition3_bear = abs(open.shift(1) - close.shift(1)) / (high.shift(1) - low.shift(1) + 1e-9) < 0.1
    # 再前一根为看涨蜡烛
    condition4_bear = close.shift(2) > open.shift(2)

    if condition1_bear and condition2_bear and condition3_bear and condition4_bear:
        signal = 1  # 卖出信号

    return signal

![](img/0ff43535dc1073d6f5d296b28b5ff14f_20.png)

# 应用函数，生成信号列
data['bb_doji_signal'] = data.apply(lambda row: bb_doji_signal(row['Open'], row['High'], row['Low'], row['Close'],
                                                                row['bb_lower'], row['bb_middle'], row['bb_upper']), axis=1)
```

![](img/0ff43535dc1073d6f5d296b28b5ff14f_22.png)

### 吞没信号函数
该函数检查每一根K线是否满足结合布林带的吞没形态条件。

```python
def bb_engulfing_signal(open, high, low, close, bb_lower, bb_middle, bb_upper):
    signal = 0
    prev_open, prev_close = open.shift(1), close.shift(1)

    # 看涨吞没信号
    condition_loc_bull = (close > bb_lower) & (close < bb_middle)
    condition_bull_candle = close > open
    condition_prev_bear = prev_close < prev_open
    condition_engulf_bull = (open < prev_close) & (close > prev_open)

    if condition_loc_bull and condition_bull_candle and condition_prev_bear and condition_engulf_bull:
        signal = 2

    # 看跌吞没信号
    condition_loc_bear = (close < bb_upper) & (close > bb_middle)
    condition_bear_candle = close < open
    condition_prev_bull = prev_close > prev_open
    condition_engulf_bear = (open > prev_close) & (close < prev_open)

    if condition_loc_bear and condition_bear_candle and condition_prev_bull and condition_engulf_bear:
        signal = 1

    return signal

# 应用函数，生成信号列
data['bb_engulfing_signal'] = data.apply(lambda row: bb_engulfing_signal(row['Open'], row['High'], row['Low'], row['Close'],
                                                                          row['bb_lower'], row['bb_middle'], row['bb_upper']), axis=1)
```

![](img/0ff43535dc1073d6f5d296b28b5ff14f_24.png)

---

## 4️⃣：信号可视化与初步观察

![](img/0ff43535dc1073d6f5d296b28b5ff14f_26.png)

生成信号后，我们可以将其可视化在价格图表上，以直观地检查信号识别的准确性。

以下是创建可视化图表的步骤：

![](img/0ff43535dc1073d6f5d296b28b5ff14f_28.png)

1.  **计算信号标记位置**：为了在K线图上方或下方绘制信号点，我们根据信号类型（看涨/看跌）计算一个偏移位置。
2.  **绘制图表**：使用 `mplfinance` 或 `plotly` 库，将K线、布林带和信号点绘制在同一张图上。

```python
import mplfinance as mpf

# 为信号创建绘图用的“点”的位置
def point_position(x):
    if x == 1: # 看跌信号，画在K线上方
        return data.loc[x.name, 'High'] * 1.0005
    elif x == 2: # 看涨信号，画在K线下方
        return data.loc[x.name, 'Low'] * 0.9995
    else:
        return None

![](img/0ff43535dc1073d6f5d296b28b5ff14f_30.png)

data['point_pos'] = data['bb_doji_signal'].apply(point_position)

# 准备布林带数据用于绘图
apds = [
    mpf.make_addplot(data[['bb_upper', 'bb_middle', 'bb_lower']], panel=0, color='orange', alpha=0.7),
    mpf.make_addplot(data['point_pos'].dropna(), type='scatter', markersize=50, marker='.', color='purple', panel=0)
]

# 绘制K线图
mpf.plot(data, type='candle', addplot=apds, style='charles', volume=False, figsize=(20,10))
```
通过观察图表，我们可以验证信号是否出现在预期的形态和布林带位置附近。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_32.png)

---

![](img/0ff43535dc1073d6f5d296b28b5ff14f_34.png)

![](img/0ff43535dc1073d6f5d296b28b5ff14f_35.png)

## 5️⃣：策略回测与结果分析

在确认信号生成正确后，我们需要对策略进行回测，以量化其历史表现。本节将介绍回测的基本设置并分析结果。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_37.png)

我们将使用一个简单的回测框架，假设初始资金为50单位（如50,000美元），并遵循以下规则：
*   每次只持有一个仓位。
*   **买入信号**时做多，**卖出信号**时做空。
*   止损和止盈基于最近两根K线的极值点设置，止盈止损比为1.5。
*   考虑杠杆（例如10倍）和点差/佣金。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_39.png)

以下是回测核心逻辑的伪代码描述：

![](img/0ff43535dc1073d6f5d296b28b5ff14f_41.png)

```python
# 伪代码逻辑
初始资金 equity = 50
仓位 position = None

![](img/0ff43535dc1073d6f5d296b28b5ff14f_43.png)

for 每根K线 in 数据:
    if 有买入信号 and 没有持仓:
        止损价 = min(当前最低价, 前一根最低价)
        止盈价 = 入场价 + 1.5 * (入场价 - 止损价)
        开立多头仓位
    elif 有卖出信号 and 没有持仓:
        止损价 = max(当前最高价, 前一根最高价)
        止盈价 = 入场价 - 1.5 * (止损价 - 入场价)
        开立空头仓位

    if 有持仓:
        检查是否触发止损或止盈，更新权益equity
```

![](img/0ff43535dc1073d6f5d296b28b5ff14f_45.png)

![](img/0ff43535dc1073d6f5d296b28b5ff14f_47.png)

分别使用十字星信号和吞没信号进行回测后，我们得到了初步结果：
*   **原始极端条件**（收盘价在布林带之外）：两种策略的回报率均为负值（约-1.7%和-1.38%），权益曲线表现不佳。
*   **修正条件**（收盘价在布林带内部的中轨附近）：策略表现显著改善。
    *   **十字星策略**：回报率约为 **+17.9%**。
    *   **吞没策略**：回报率约为 **+26%**。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_49.png)

这表明，在布林带内部（代表市场处于相对常态或整理阶段）识别反转形态，比在布林带外部（代表极端趋势）识别，在本回测周期内更为有效。

---

![](img/0ff43535dc1073d6f5d296b28b5ff14f_51.png)

## 6️⃣：信号组合与策略优化

![](img/0ff43535dc1073d6f5d296b28b5ff14f_53.png)

![](img/0ff43535dc1073d6f5d296b28b5ff14f_54.png)

上一节我们看到单个形态策略已能取得正收益，本节中我们来看看如何组合信号并探讨进一步的优化方向。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_56.png)

### 组合信号
我们可以将十字星和吞没形态的信号结合起来，生成更密集的交易信号。逻辑是：只要出现任何一种形态的看涨信号，就执行买入；出现任何一种形态的看跌信号，就执行卖出。

```python
# 生成组合信号列
data['combined_signal'] = 0
data.loc[(data['bb_doji_signal'] == 2) | (data['bb_engulfing_signal'] == 2), 'combined_signal'] = 2
data.loc[(data['bb_doji_signal'] == 1) | (data['bb_engulfing_signal'] == 1), 'combined_signal'] = 1
```
使用组合信号进行回测，回报率提升至约 **+41%**，交易次数也增加到335次。这证明了结合多种确认信号可能提高策略的捕捉机会和能力。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_58.png)

![](img/0ff43535dc1073d6f5d296b28b5ff14f_59.png)

### 优化方向讨论
尽管回测结果看似积极，但必须注意：
1.  **参数静态化**：我们使用了固定的布林带参数(30,1.5)、固定的止损止盈比(1.5)和固定的杠杆。市场是动态变化的，这些参数可能需要自适应调整。
2.  **风险控制简单**：固定的止损止盈并非最优。可以考虑基于**平均真实波幅（ATR）** 的动态止损，或加入**移动止损**策略。
3.  **过拟合风险**：策略在特定时间段（2021-2023）表现良好，可能无法适应所有市场环境。需要进行多周期、多品种的回测。
4.  **交易成本**：实盘中需充分考虑点差、佣金和滑点的影响，这可能会显著侵蚀利润。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_61.png)

在实际应用中，建议不要依赖单一策略。构建一个包含多种不同逻辑策略的组合，可以在单一策略失效时提供保护，从而获得更稳定的整体收益。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_62.png)

---

## 总结

![](img/0ff43535dc1073d6f5d296b28b5ff14f_64.png)

本节课中我们一起学习了如何将十字星和吞没这两种K线反转形态与布林带指标相结合，构建自动化交易策略。我们从概念理解出发，逐步完成了数据获取、指标计算、信号函数编写、可视化以及回测分析的完整流程。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_66.png)

关键结论包括：
1.  在布林带**内部**（中轨附近）寻找反转形态，比在布林带**外部**寻找，在本案例的回测中表现更好。
2.  **吞没形态**策略的单独表现略优于十字星形态策略。
3.  **组合使用两种形态**的信号，可以增加交易机会，并可能提升策略的整体收益率（本案例中组合策略回报达41%）。
4.  本教程展示的策略是一个简单的起点，要用于实盘，必须考虑更动态的风险管理、参数优化以及严格的多环境回测。

![](img/0ff43535dc1073d6f5d296b28b5ff14f_68.png)

![](img/0ff43535dc1073d6f5d296b28b5ff14f_69.png)

希望本教程能帮助你理解如何将经典技术形态与指标结合，并用Python进行系统性的策略开发和验证。记住，回测是验证想法的工具，但绝非盈利的保证。持续学习、谨慎优化和严格风控才是交易的核心。