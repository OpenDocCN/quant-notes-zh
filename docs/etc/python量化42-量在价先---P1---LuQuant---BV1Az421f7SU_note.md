# Python量化42：P1 - 成交量交易策略回测教程 📊

在本教程中，我们将学习如何为一个基于成交量与价格关系的交易策略编写自动化回测代码。我们将使用长达18年的历史数据来测试该策略的有效性，并分析其风险与回报。

![](img/443ebbd606252aa87c3e75aed0190944_1.png)

## 策略概述

![](img/443ebbd606252aa87c3e75aed0190944_3.png)

![](img/443ebbd606252aa87c3e75aed0190944_4.png)

该策略的核心思想是“量在价先”，结合趋势判断来寻找交易信号。它主要使用三个指标：**200日指数移动平均线（EMA200）** 判断趋势，**成交量**确认信号强度，以及特定形态的**价格蜡烛**作为入场触发器。

![](img/443ebbd606252aa87c3e75aed0190944_6.png)

## 数据准备与指标计算

首先，我们需要导入必要的库并加载历史价格数据。

```python
import pandas as pd
import pandas_ta as ta

# 加载数据，假设CSV文件包含‘Open’, ‘High’, ‘Low’, ‘Close’, ‘Volume’列
df = pd.read_csv(‘your_data.csv’)
# 重命名列以确保一致性
df.columns = [‘Open’, ‘High’, ‘Low’, ‘Close’, ‘Volume’]
# 清除成交量为0的行（如周末、假日）
df = df[df[‘Volume’] > 0].reset_index(drop=True)
```

接下来，我们计算技术指标：200日指数移动平均线（EMA）和平均真实波幅（ATR）。

```python
# 计算200日指数移动平均线
df[‘EMA’] = ta.ema(df[‘Close’], length=200)
# 计算ATR，用于后续止损止盈设置
df[‘ATR’] = ta.atr(df[‘High’], df[‘Low’], df[‘Close’], length=14)
```

![](img/443ebbd606252aa87c3e75aed0190944_8.png)

## 定义趋势信号

上一节我们导入了数据并计算了基础指标，本节中我们来看看如何定义市场的趋势方向。趋势由价格相对于EMA200的位置决定，但为了过滤噪音，我们要求价格在均线一侧持续一段时间。

```python
# 定义判断趋势持续性的参数
trend_confirmation_period = 4

def get_ema_signal(row_index, df, period=trend_confirmation_period):
    """
    判断给定蜡烛的趋势。
    规则：如果当前及前period-1根蜡烛都交易在EMA上方，则为上升趋势（信号2）。
          如果都交易在EMA下方，则为下降趋势（信号1）。
          否则为无明确趋势（信号0）。
    """
    if row_index < period:
        return 0
    current_slice = df.iloc[row_index - period + 1: row_index + 1]
    above_ema = all(current_slice[‘Close’] > current_slice[‘EMA’])
    below_ema = all(current_slice[‘Close’] < current_slice[‘EMA’])
    if above_ema:
        return 2  # 上升趋势，寻找买入信号
    elif below_ema:
        return 1  # 下降趋势，寻找卖出信号
    else:
        return 0

# 应用函数，为每一行生成趋势信号
df[‘EMA_Signal’] = [get_ema_signal(i, df) for i in range(len(df))]
```

## 定义成交量信号

趋势信号帮助我们确定交易的方向。接下来，我们需要一个确认信号强度的指标，即成交量。策略要求入场蜡烛的成交量必须大于前一根蜡烛的成交量。

以下是生成成交量信号的逻辑：

```python
def get_volume_signal(row_index, df, lookback=1):
    """
    判断成交量是否确认。
    规则：如果当前蜡烛的成交量大于前lookback根蜡烛的成交量，则返回1，否则返回0。
    """
    if row_index < lookback:
        return 0
    current_volume = df.iloc[row_index][‘Volume’]
    previous_volume = df.iloc[row_index - lookback][‘Volume’]
    return 1 if current_volume > previous_volume else 0

# 应用函数，本例中lookback=1
df[‘Volume_Signal’] = [get_volume_signal(i, df, lookback=1) for i in range(len(df))]
```

## 定义价格形态信号

成交量信号提供了动能确认。现在，我们需要定义具体的价格入场形态。策略要求出现与趋势相反的连续数根蜡烛后，再出现一根顺趋势的蜡烛。

以下是生成价格形态信号的步骤：

```python
# 定义反向蜡烛的数量参数
reverse_candles = 4

def get_price_signal(row_index, df, reverse_count=reverse_candles):
    """
    判断价格形态是否满足入场条件。
    规则：
        - 下降趋势中：寻找连续reverse_count根“看涨”蜡烛（收盘价高于开盘价），随后出现一根“看跌”蜡烛。
        - 上升趋势中：寻找连续reverse_count根“看跌”蜡烛（收盘价低于开盘价），随后出现一根“看涨”蜡烛。
    满足条件则返回与趋势相同的信号（1代表卖出，2代表买入），否则返回0。
    """
    if row_index < reverse_count:
        return 0
    trend = df.iloc[row_index][‘EMA_Signal’]
    # 检查前reverse_count根蜡烛是否与趋势相反
    lookback_slice = df.iloc[row_index - reverse_count: row_index]
    if trend == 1:  # 下降趋势，需要前reverse_count根为看涨蜡烛
        opposite_candles = all(lookback_slice[‘Close’] > lookback_slice[‘Open’])
        current_candle_bearish = df.iloc[row_index][‘Close’] < df.iloc[row_index][‘Open’]
        if opposite_candles and current_candle_bearish:
            return 1  # 卖出信号
    elif trend == 2:  # 上升趋势，需要前reverse_count根为看跌蜡烛
        opposite_candles = all(lookback_slice[‘Close’] < lookback_slice[‘Open’])
        current_candle_bullish = df.iloc[row_index][‘Close’] > df.iloc[row_index][‘Open’]
        if opposite_candles and current_candle_bullish:
            return 2  # 买入信号
    return 0

df[‘Price_Signal’] = [get_price_signal(i, df) for i in range(len(df))]
```

## 生成综合交易信号

现在我们已经有了趋势、成交量和价格形态三个独立的信号。本节我们将把它们组合起来，生成最终的买入或卖出信号。

综合信号的生成规则如下：

```python
def get_total_signal(row):
    """
    综合三个条件生成最终交易信号。
    规则：
        - 卖出信号：趋势为下降(1) & 成交量确认(1) & 价格形态为卖出(1)
        - 买入信号：趋势为上升(2) & 成交量确认(1) & 价格形态为买入(2)
    """
    if row[‘EMA_Signal’] == 1 and row[‘Volume_Signal’] == 1 and row[‘Price_Signal’] == 1:
        return ‘Sell’
    elif row[‘EMA_Signal’] == 2 and row[‘Volume_Signal’] == 1 and row[‘Price_Signal’] == 2:
        return ‘Buy’
    else:
        return ‘Hold’

df[‘Total_Signal’] = df.apply(get_total_signal, axis=1)
```

## 设置止损与止盈

有了交易信号后，风险管理至关重要。我们将测试两种设置止损的方法。

**方法一：基于ATR的固定距离止损**
止损距离设置为ATR值的倍数（如`4 * ATR`），止盈距离为止损距离的固定倍数（如`1.5`倍）。

```python
atr_multiplier_stop = 4
risk_reward_ratio = 1.5

def set_stop_target_atr(row, atr_multiplier=atr_multiplier_stop, rr_ratio=risk_reward_ratio):
    if row[‘Total_Signal’] == ‘Buy’:
        stop_loss = row[‘Close’] - (row[‘ATR’] * atr_multiplier)
        take_profit = row[‘Close’] + (row[‘ATR’] * atr_multiplier * rr_ratio)
    elif row[‘Total_Signal’] == ‘Sell’:
        stop_loss = row[‘Close’] + (row[‘ATR’] * atr_multiplier)
        take_profit = row[‘Close’] - (row[‘ATR’] * atr_multiplier * rr_ratio)
    else:
        stop_loss, take_profit = None, None
    return stop_loss, take_profit

df[[‘Stop_Loss_ATR’, ‘Take_Profit_ATR’]] = df.apply(lambda x: pd.Series(set_stop_target_atr(x)), axis=1)
```

**方法二：基于近期价格极点的止损**
对于买入信号，止损设在信号蜡烛及前`reverse_candles`根蜡烛的最低点下方。对于卖出信号，则设在最高点上方。

```python
def set_stop_target_swing(row_index, df, lookback=reverse_candles):
    signal = df.iloc[row_index][‘Total_Signal’]
    if signal == ‘Hold’:
        return None, None
    # 查看当前及前lookback根蜡烛
    start_idx = max(0, row_index - lookback)
    swing_slice = df.iloc[start_idx: row_index + 1]
    if signal == ‘Buy’:
        stop_loss = swing_slice[‘Low’].min()
        # 止盈可基于止损距离计算，此处沿用ATR比率作为示例
        take_profit = df.iloc[row_index][‘Close’] + (df.iloc[row_index][‘Close’] - stop_loss) * risk_reward_ratio
    elif signal == ‘Sell’:
        stop_loss = swing_slice[‘High’].max()
        take_profit = df.iloc[row_index][‘Close’] - (stop_loss - df.iloc[row_index][‘Close’]) * risk_reward_ratio
    return stop_loss, take_profit

# 应用函数
swing_stops = [set_stop_target_swing(i, df) for i in range(len(df))]
df[[‘Stop_Loss_Swing’, ‘Take_Profit_Swing’]] = pd.DataFrame(swing_stops, columns=[‘SL’, ‘TP’])
```

## 策略回测与结果分析

在定义了所有信号和风控规则后，我们可以进行回测。回测逻辑是：在出现信号的下一根蜡烛开盘时入场，设置好止损和止盈，直到触及任一价格后平仓。

以下是简化的回测循环框架：

```python
# 初始化回测变量
initial_capital = 10000
capital = initial_capital
position = None
entry_price = 0
stop_loss = 0
take_profit = 0
trade_log = []

for i in range(1, len(df)):
    current_signal = df.iloc[i][‘Total_Signal’]
    # 检查是否有未平仓头寸被触发止损止盈
    if position == ‘Buy’ and df.iloc[i][‘Low’] <= stop_loss:
        # 处理买入头寸止损
        pass
    elif position == ‘Sell’ and df.iloc[i][‘High’] >= stop_loss:
        # 处理卖出头寸止损
        pass
    # ... 类似逻辑处理止盈

    # 开新仓逻辑：在信号出现的下一根蜡烛开盘入场
    if position is None and df.iloc[i-1][‘Total_Signal’] in [‘Buy’, ‘Sell’]:
        position = df.iloc[i-1][‘Total_Signal’]
        entry_price = df.iloc[i][‘Open’]
        # 根据选择的方法设置止损止盈
        stop_loss = df.iloc[i-1][‘Stop_Loss_ATR’] # 或 ‘Stop_Loss_Swing’
        take_profit = df.iloc[i-1][‘Take_Profit_ATR’] # 或 ‘Take_Profit_Swing’
        # 记录交易
        trade_log.append({‘Entry_Index’: i, ‘Position’: position, ‘Entry_Price’: entry_price, ‘Stop_Loss’: stop_loss, ‘Take_Profit’: take_profit})
```

回测完成后，我们可以计算总收益率、胜率、夏普比率等关键指标，并绘制权益曲线来评估策略性能。

## 总结

本节课中我们一起学习了如何为一个“量在价先”的交易策略构建完整的自动化回测流程。我们从数据准备开始，逐步定义了**趋势判断**、**成交量确认**和**价格形态识别**三个核心条件，并将其组合成交易信号。随后，我们探讨了两种设置**止损止盈**的方法：基于ATR的固定距离法和基于近期价格极点的摆动法。最后，我们概述了回测的基本逻辑。

![](img/443ebbd606252aa87c3e75aed0190944_10.png)

需要注意的是，该策略在长期日线数据上产生的交易信号较少，这意味着它可能更适合作为低频交易系统的一部分。要将其应用于其他时间框架（如小时图）或市场，必须对相关参数进行细致的调整和优化。成功的量化交易依赖于严谨的回测、参数优化以及对市场环境的持续适应。