# Python量化32：布林带策略3 - P1 - 策略详解与初步实现 🚀

![](img/c5a6b7b922d20b484948a44a1b8becd2_1.png)

## 概述
在本节课中，我们将学习一个结合布林带和移动平均线的量化交易策略。我们将详细解析该策略的逻辑，并使用Python进行初步实现和回测。课程内容涵盖策略规则、代码实现细节以及初步的回测结果分析。

![](img/c5a6b7b922d20b484948a44a1b8becd2_3.png)

---

![](img/c5a6b7b922d20b484948a44a1b8becd2_5.png)

## 策略核心逻辑解析 📊

上一节我们介绍了策略的基本概念，本节中我们来看看该策略的具体交易规则。

该策略的核心是结合移动平均线判断趋势，并利用布林带寻找入场信号。

### 趋势判断
我们使用一条长期移动平均线（例如200日SMA）来判断市场趋势。
*   **上升趋势**：当价格（蜡烛）交易在移动平均线**之上**时。
*   **下降趋势**：当价格（蜡烛）交易在移动平均线**之下**时。

为了确认趋势，我们通常要求价格连续多根蜡烛（例如6根）保持在均线同一侧。

### 入场信号
入场信号取决于当前趋势和布林带的位置。

![](img/c5a6b7b922d20b484948a44a1b8becd2_7.png)

*   **买入信号（做多）**：在**上升趋势**中，寻找收盘价**低于**布林带下轨的蜡烛。
*   **卖出信号（做空）**：在**下降趋势**中，寻找收盘价**高于**布林带上轨的蜡烛。

### 订单设置
当出现信号时，我们并不立即以市价入场，而是设置一个限价订单，试图获得更优的价格。

*   **买入限价单**：设置在信号蜡烛收盘价**下方3%** 的位置。公式为：`买入限价 = 信号收盘价 * (1 - 0.03)`
*   **卖出限价单**：设置在信号蜡烛收盘价**上方3%** 的位置。公式为：`卖出限价 = 信号收盘价 * (1 + 0.03)`

### 出场条件
交易入场后，在以下两个条件中任一满足时平仓：
1.  **RSI条件**：
    *   对于**买入交易**，当RSI从下方**上穿50**阈值时平仓。
    *   对于**卖出交易**，当RSI从上方**下穿50**阈值时平仓。
2.  **时间条件**：如果RSI条件在**10个交易日内**都未触发，则无条件平仓。

### 订单与仓位管理
在编码实现前，需要明确几个关键细节：

以下是关于订单管理的具体规则：
1.  **订单有效期**：限价单在市场上保留**5天**。若未被成交，则自动取消。
2.  **订单更新**：当出现新的同方向信号时，用**最新的订单**替换旧的未成交订单。
3.  **仓位限制**：同一时间只允许持有**一个未平仓仓位**。在有未平仓仓位时，不接受新的入场信号。

---

## Python代码实现 💻

上一节我们明确了策略规则，本节中我们来看看如何使用Python代码实现这些逻辑。

### 数据准备与指标计算
首先，我们需要获取数据并计算必要的技术指标。

```python
import pandas as pd
import yfinance as yf
# 假设已导入TA库（如ta）用于计算指标

# 1. 下载数据
df = yf.download('SPY', start='2017-01-01', end='2022-12-31')

# 2. 数据清洗：移除无效蜡烛（如最高价等于最低价）
df = df[df['High'] != df['Low']].reset_index(drop=True)

# 3. 计算指标
# 移动平均线 (例如200日SMA)
df['MA'] = df['Close'].rolling(window=200).mean()
# RSI (例如14日)
df['RSI'] = ta.momentum.RSIIndicator(df['Close'], window=14).rsi()
# 布林带 (长度20，标准差2.5)
bb = ta.volatility.BollingerBands(df['Close'], window=20, window_dev=2.5)
df['BB_High'] = bb.bollinger_hband()
df['BB_Low'] = bb.bollinger_lband()

# 4. 删除因滚动计算产生的空值
df = df.dropna().reset_index(drop=True)
```

### 生成交易信号
接下来，我们根据策略规则生成趋势判断和订单信号。

```python
def add_ma_signal(df, lookback=6):
    """判断移动平均线趋势信号"""
    df['MA_Signal'] = 0  # 0:无趋势，1:下降趋势，2:上升趋势
    # 判断连续lookback根蜡烛在均线上方（上升趋势）
    condition_up = (df['Close'] > df['MA']).rolling(window=lookback).sum() == lookback
    # 判断连续lookback根蜡烛在均线下方（下降趋势）
    condition_down = (df['Close'] < df['MA']).rolling(window=lookback).sum() == lookback
    df.loc[condition_up, 'MA_Signal'] = 2
    df.loc[condition_down, 'MA_Signal'] = 1
    return df

def add_order_signal(df, percent=0.03):
    """生成订单限价信号"""
    df['Order_Signal'] = 0.0
    # 买入信号条件：上升趋势 & 收盘价低于布林带下轨
    buy_condition = (df['MA_Signal'] == 2) & (df['Close'] < df['BB_Low'])
    df.loc[buy_condition, 'Order_Signal'] = df['Close'] * (1 - percent)
    # 卖出信号条件：下降趋势 & 收盘价高于布林带上轨
    sell_condition = (df['MA_Signal'] == 1) & (df['Close'] > df['BB_High'])
    df.loc[sell_condition, 'Order_Signal'] = df['Close'] * (1 + percent)
    return df

# 应用函数
df = add_ma_signal(df, lookback=6)
df = add_order_signal(df, percent=0.03)
```

### 策略回测逻辑
最后，我们整合以上信号，使用回测框架（此处为伪代码逻辑）执行策略。

以下是策略回测的核心循环逻辑：
1.  **遍历每一天**，检查是否有新的订单信号。
2.  **管理未成交订单**：检查订单是否超过5天未成交，若是则取消。
3.  **处理新信号**：如果出现新信号且当前无持仓，则取消旧订单，下达新限价单。
4.  **管理持仓**：检查持仓是否满足出场条件（RSI穿越50或持仓满10天），若满足则平仓。
5.  **订单成交检查**：检查市场价格是否触及限价单价格，若触及则开仓。

```python
# 伪代码，展示回测循环中的关键判断
for i in current_day:
    # 检查并取消过期订单（>5天）
    if order_exists and (current_day - order_creation_day > 5):
        cancel_order()

    # 检查是否有新信号且无持仓
    if new_order_signal and no_open_trade:
        cancel_previous_order() # 取消旧订单
        place_limit_order(side='buy' or 'sell', limit_price=order_signal_value)

    # 检查持仓出场条件
    if open_trade_exists:
        if (trade_duration >= 10 days) or
           (for_long_trade and RSI >= 50) or
           (for_short_trade and RSI <= 50):
            close_trade()
```

---

## 初步回测结果与总结 📈

### 结果分析
运行上述策略代码进行回测后，我们得到了一些初步结果：
*   **高胜率**：在测试周期内，策略交易胜率可达90%左右。
*   **交易频率低**：由于策略条件严格，信号非常有选择性，每年交易次数较少。
*   **参数敏感**：回报率对参数（如订单距离百分比、仓位大小）非常敏感。例如，将仓位比例从50%提升至99%，总回报率显著增加。
*   **难以完全复现**：尽管策略逻辑清晰，但完全复现原视频中极高的回报率（如1680%）是困难的，这可能涉及未公开的细节（如杠杆、手续费、精确参数或数据）。

### 优化方向
本节课中我们一起学习了一个完整的布林带结合移动平均线的交易策略。我们了解了其趋势判断、信号生成、订单管理和出场规则，并完成了初步的Python实现。

该策略展现出了高胜率的潜力，但交易机会较少。为了进一步提升，我们可以从以下几个方面进行探索：
1.  **参数优化**：系统测试移动平均线周期、布林带参数、RSI周期、订单距离百分比等。
2.  **出场策略优化**：尝试使用追踪止损，或调整RSI出场阈值，以捕捉更大利润。
3.  **在更小时间框架测试**：在小时图等更小周期测试，可能增加交易机会。
4.  **风险管理**：严格定义仓位大小和最大回撤控制。

![](img/c5a6b7b922d20b484948a44a1b8becd2_9.png)

![](img/c5a6b7b922d20b484948a44a1b8becd2_11.png)

通过本节课的学习，你已经掌握了该策略的核心思想与实现方法。接下来，你可以下载代码，使用自己的数据进行测试和优化，探索属于你自己的交易系统。