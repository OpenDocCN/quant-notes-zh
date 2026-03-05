# Python量化37：1：霍夫曼回撤柱策略概述与实现

在本节课中，我们将学习如何利用霍夫曼回撤柱（Hoffman Retracement Bar）形态来构建一个简单的交易策略。我们将介绍该形态的核心定义，展示如何在Python中检测它，并最终通过历史数据进行策略回测，以评估其潜在表现。

## 核心概念：霍夫曼回撤柱

霍夫曼回撤柱是一种特定的K线形态，用于识别趋势中的短暂回调。其核心定义包含两个部分：

![](img/c6fae663e637ce1df208a600f18a54cf_1.png)

1.  **趋势判定**：价格需处于明确的上升或下降趋势中。这通常通过移动平均线的斜率或价格与移动平均线的相对位置来判断。
2.  **K线形态**：在趋势中，出现一根具有长影线的K线。具体来说，其影线（上影线在上升趋势中，下影线在下降趋势中）长度至少占整根K线实体（最高价与最低价之差）的**40%**。

![](img/c6fae663e637ce1df208a600f18a54cf_3.png)

在上升趋势中，这种长上影线K线表明卖家试图打压价格，但若后续K线能**收盘突破**该回撤柱的最高价，则意味着买家力量依然强劲，趋势可能延续。下降趋势则反之。

## 策略逻辑

基于以上形态，我们的交易策略逻辑如下：
*   **买入信号**：在**上升趋势**中，检测到霍夫曼回撤柱后，若下一根K线的收盘价**高于**该回撤柱的最高价，则产生买入信号。
*   **卖出信号**：在**下降趋势**中，检测到霍夫曼回撤柱后，若下一根K线的收盘价**低于**该回撤柱的最低价，则产生卖出信号。

---

## Python量化37：2：代码实现详解

上一节我们介绍了霍夫曼回撤柱的策略逻辑，本节中我们来看看如何在Python中具体实现它。我们将分步完成数据准备、形态检测和信号生成。

### 步骤一：数据准备与基础指标计算

首先，我们需要获取价格数据并计算一些基础指标，包括指数移动平均线（EMA）和平均真实波幅（ATR），后者将用于后续的止损止盈设置。

![](img/c6fae663e637ce1df208a600f18a54cf_5.png)

```python
import pandas as pd
import yfinance as yf

# 1. 获取数据
symbol = ‘AAPL’
data = yf.download(symbol, period=’3mo’, interval=’15m’)
data.reset_index(inplace=True)

# 2. 计算指数移动平均线 (例如20周期)
data[‘EMA_20’] = data[‘Close’].ewm(span=20, adjust=False).mean()

# 3. 计算平均真实波幅 (ATR)
def calculate_atr(data, period=14):
    high_low = data[‘High’] - data[‘Low’]
    high_close = abs(data[‘High’] - data[‘Close’].shift())
    low_close = abs(data[‘Low’] - data[‘Close’].shift())
    true_range = pd.concat([high_low, high_close, low_close], axis=1).max(axis=1)
    atr = true_range.rolling(window=period).mean()
    return atr

data[‘ATR’] = calculate_atr(data)
```

### 步骤二：检测霍夫曼回撤柱

接下来，我们定义一个函数来识别霍夫曼回撤柱。该函数需要判断趋势方向并检查K线形态。

```python
def is_hoffman_bar(row, trend_direction, min_shadow_ratio=0.4):
    “””
    判断单根K线是否为霍夫曼回撤柱。
    :param row: DataFrame的一行，代表一根K线
    :param trend_direction: 趋势方向，’up’ 或 ‘down’
    :param min_shadow_ratio: 影线最小占比阈值
    :return: Boolean
    “””
    body = abs(row[‘Close’] - row[‘Open’])
    total_range = row[‘High’] - row[‘Low’]
    
    if total_range == 0:
        return False
    
    if trend_direction == ‘up’:
        # 上升趋势中，寻找长上影线
        upper_shadow = row[‘High’] - max(row[‘Open’], row[‘Close’])
        shadow_ratio = upper_shadow / total_range
        return shadow_ratio >= min_shadow_ratio
    elif trend_direction == ‘down’:
        # 下降趋势中，寻找长下影线
        lower_shadow = min(row[‘Open’], row[‘Close’]) - row[‘Low’]
        shadow_ratio = lower_shadow / total_range
        return shadow_ratio >= min_shadow_ratio
    else:
        return False
```

### 步骤三：生成交易信号

现在，我们结合趋势判断和霍夫曼柱检测来生成买卖信号。这里引入一个关键参数 `lookback_candles`，它定义了我们在当前K线之前回溯多少根K线来寻找霍夫曼回撤柱。

```python
def generate_hoffman_signal(data, current_index, lookback_candles=6):
    “””
    为指定索引的K线生成霍夫曼突破信号。
    :param data: 包含价格和指标的DataFrame
    :param current_index: 当前K线的索引
    :param lookback_candles: 回溯寻找霍夫曼柱的K线数量
    :return: ‘buy’, ‘sell’, 或 None
    “””
    if current_index < lookback_candles:
        return None
    
    # 1. 判断趋势 (简化：用价格与EMA的位置判断)
    # 也可以使用EMA斜率进行更精确的判断
    current_price = data.loc[current_index, ‘Close’]
    current_ema = data.loc[current_index, ‘EMA_20’]
    trend = ‘up’ if current_price > current_ema else ‘down’
    
    # 2. 在回溯区间内寻找霍夫曼回撤柱
    hoffman_found = False
    hoffman_high = None
    hoffman_low = None
    
    for i in range(current_index - lookback_candles, current_index):
        row = data.loc[i]
        if is_hoffman_bar(row, trend):
            hoffman_found = True
            hoffman_high = row[‘High’]
            hoffman_low = row[‘Low’]
            break # 找到最近的一根即可
    
    # 3. 检查是否发生突破
    if hoffman_found:
        current_close = data.loc[current_index, ‘Close’]
        if trend == ‘up’ and current_close > hoffman_high:
            return ‘buy’
        elif trend == ‘down’ and current_close < hoffman_low:
            return ‘sell’
    
    return None

# 将信号函数应用到整个数据集
data[‘Signal’] = [generate_hoffman_signal(data, i) for i in range(len(data))]
```

---

## Python量化37：3：策略回测与结果分析

在生成了交易信号之后，本节我们将对策略进行回测，设置止损止盈，并分析其表现。

### 回测参数设置

以下是进行回测时需要定义的关键参数：

*   **初始资金**：例如 `initial_capital = 10000`。
*   **交易规模**：例如每次交易固定1手，或使用资金的一定比例。
*   **止损止盈**：我们将尝试两种方法：
    1.  **基于ATR**：止损设为入场价 `± n * ATR`，止盈设为入场价 `± m * ATR`。
    2.  **基于前期高低点**：在上升趋势中，止损设为前N根K线的最低价；止盈通过风险回报比计算。

### 回测过程简述

回测过程是一个模拟交易循环。我们遍历每一根K线，当出现信号时，根据规则开仓，并在后续K线中检查止损止盈条件是否触发以平仓。

以下是基于ATR设置止损止盈的回测逻辑核心片段：

```python
# 假设已有一个包含‘Signal’列的数据框 `data`
position = None # 当前持仓，例如 {‘type’: ‘long’, ‘entry_price’: 100, ‘stop_loss’: 98, ‘take_profit’: 105}
equity_curve = [initial_capital] # 权益曲线

for i in range(1, len(data)):
    current_row = data.iloc[i]
    prev_row = data.iloc[i-1]
    
    # 开仓逻辑
    if position is None and prev_row[‘Signal’] == ‘buy’:
        entry_price = current_row[‘Open’] # 假设下一根K线开盘价入场
        atr_value = prev_row[‘ATR’]
        stop_loss = entry_price - 2 * atr_value # 举例：2倍ATR止损
        take_profit = entry_price + 3 * atr_value # 举例：3倍ATR止盈，风险回报比1:1.5
        position = {‘type’: ‘long’, ‘entry_price’: entry_price, ‘stop_loss’: stop_loss, ‘take_profit’: take_profit}
    
    # 平仓逻辑
    if position is not None:
        current_low = current_row[‘Low’]
        current_high = current_row[‘High’]
        if position[‘type’] == ‘long’:
            # 检查止损
            if current_low <= position[‘stop_loss’]:
                # 计算盈亏，更新资金，并重置position为None
                pass
            # 检查止盈
            elif current_high >= position[‘take_profit’]:
                # 计算盈亏，更新资金，并重置position为None
                pass
    
    # 记录当前权益
    equity_curve.append(current_capital)
```

### 回测结果与总结

使用上述代码对一段3个月的历史数据进行回测后，我们可能得到如下结果（具体数值因参数和市场而异）：

*   **总收益率**：可能出现小幅盈利（如5.1%）或亏损（如-4.8%）。
*   **胜率**：交易获胜的百分比，可能在50%左右。
*   **净值曲线**：图表可能显示曲线波动较大，并未呈现稳定增长。

![](img/c6fae663e637ce1df208a600f18a54cf_7.png)

**本节课总结**：
我们一起学习了霍夫曼回撤柱交易策略从理论到实践的全过程。
1.  我们定义了该策略的核心形态：在明确趋势中，影线长度占比超过40%的K线。
2.  我们使用Python逐步实现了数据获取、形态检测和信号生成。
3.  我们构建了简单的回测框架，并尝试了不同的止损止盈设置方法。

![](img/c6fae663e637ce1df208a600f18a54cf_8.png)

需要强调的是，本例中的策略是一个基础原型，未经参数优化和严格验证。在实际应用中，你需要结合更多技术指标进行过滤，并在更长的历史数据中进行测试与优化。希望本教程为你实现自己的量化策略提供了一个清晰的起点。