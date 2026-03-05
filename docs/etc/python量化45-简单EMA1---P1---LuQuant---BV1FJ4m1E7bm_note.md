# Python量化45：简单EMA策略 - P1 - LuQuant

## 📋 概述
在本节课中，我们将学习如何构建一个基于三条指数移动平均线（EMA）的简单趋势跟踪策略。我们将使用Python进行编码，并通过回测来评估策略的初步表现。课程内容包括数据准备、指标计算、信号生成以及基本的回测分析。

---

## 📊 1：数据准备与指标计算

上一节我们介绍了课程目标，本节中我们来看看如何准备数据和计算核心指标。

首先，我们需要获取并清理历史价格数据。这里我们使用欧元/美元货币对在2019年至2022年之间的15分钟K线数据。

以下是数据准备和指标计算的核心步骤：

![](img/37d5c3061862396fae6ec48a2e090c95_1.png)

1.  **导入数据**：加载包含开盘价、最高价、最低价、收盘价和成交量的K线数据。
2.  **数据清理**：删除成交量为0的K线，这些通常代表周末或非交易时段的数据。
3.  **计算移动平均线**：使用`pandas_ta`库计算三条指数移动平均线（EMA）。
    *   快速EMA（`ema_fast`）：周期为50。
    *   中期EMA（`ema_medium`）：周期为100。
    *   慢速EMA（`ema_slow`）：周期为150。
4.  **计算斜率**：计算每条EMA线在过去一定周期（例如10根K线）内的平均斜率，以判断趋势方向。

```python
# 示例代码：计算EMA和斜率
import pandas_ta as ta

# 计算三条EMA
df['ema_50'] = ta.ema(df['close'], length=50)
df['ema_100'] = ta.ema(df['close'], length=100)
df['ema_150'] = ta.ema(df['close'], length=150)

# 计算斜率（以ema_50为例，使用10周期滚动平均）
df['slope_50'] = df['ema_50'].diff(1) / 1  # 简化计算，实际可使用线性回归
df['slope_50_avg'] = df['slope_50'].rolling(window=10).mean()
```

---

## 🎯 2：趋势判断与信号生成

在准备好数据和基础指标后，本节我们将定义趋势并生成交易信号。

我们通过EMA的排列顺序和斜率方向共同判断市场趋势。

*   **上升趋势条件**：
    1.  快速EMA > 中期EMA > 慢速EMA。
    2.  三条EMA的斜率平均值均为正数。
*   **下降趋势条件**：
    1.  快速EMA < 中期EMA < 慢速EMA。
    2.  三条EMA的斜率平均值均为负数。

当趋势明确后，我们寻找入场点。入场信号由价格与快速EMA（`ema_50`）的交叉决定。

*   **买入信号**：在上升趋势中，出现一根K线，其开盘价低于`ema_50`，但收盘价高于`ema_50`。
*   **卖出信号**：在下降趋势中，出现一根K线，其开盘价高于`ema_50`，但收盘价低于`ema_50`。

以下是生成信号的逻辑代码框架：

```python
import numpy as np

# 初始化信号列
df['trend_signal'] = 0  # 0:无趋势， 1:下降趋势， 2:上升趋势
df['trade_signal'] = 0  # 0:无操作， 1:卖出， 2:买入

# 条件判断：下降趋势
down_trend_condition = (df['ema_50'] < df['ema_100']) & \
                       (df['ema_100'] < df['ema_150']) & \
                       (df['slope_50_avg'] < 0) & \
                       (df['slope_100_avg'] < 0) & \
                       (df['slope_150_avg'] < 0)

# 条件判断：上升趋势
up_trend_condition = (df['ema_50'] > df['ema_100']) & \
                     (df['ema_100'] > df['ema_150']) & \
                     (df['slope_50_avg'] > 0) & \
                     (df['slope_100_avg'] > 0) & \
                     (df['slope_150_avg'] > 0)

# 分配趋势信号
df.loc[down_trend_condition, 'trend_signal'] = 1
df.loc[up_trend_condition, 'trend_signal'] = 2

# 生成交易信号
for i in range(1, len(df)):
    # 卖出信号：下降趋势中，价格下穿快速EMA
    if df.loc[i, 'trend_signal'] == 1 and \
       df.loc[i, 'open'] > df.loc[i, 'ema_50'] and \
       df.loc[i, 'close'] < df.loc[i, 'ema_50']:
        df.loc[i, 'trade_signal'] = 1
    # 买入信号：上升趋势中，价格上穿快速EMA
    elif df.loc[i, 'trend_signal'] == 2 and \
         df.loc[i, 'open'] < df.loc[i, 'ema_50'] and \
         df.loc[i, 'close'] > df.loc[i, 'ema_50']:
        df.loc[i, 'trade_signal'] = 2
```

---

## 📈 3：策略回测与初步结果

在定义了清晰的入场信号后，本节我们对策略进行简单的回测，观察其初步表现。

我们使用`backtesting.py`库进行回测。策略规则如下：

*   **初始资金**：$100。
*   **头寸规模**：每次交易使用$30（即30%的仓位）。
*   **风险管理**：固定止损和止盈均为40点（pips）。
*   **交易规则**：同一时间只持有一个头寸（多头或空头）。

以下是回测的核心设置示例：

```python
from backtesting import Backtest, Strategy

class EMACrossoverStrategy(Strategy):
    # 定义策略参数
    def init(self):
        pass

    def next(self):
        current_index = len(self.data) - 1
        # 获取当前K线的信号
        trade_signal = self.data.trade_signal[current_index]

        # 如果没有未平仓头寸，则根据信号开仓
        if not self.position:
            if trade_signal == 2:  # 买入信号
                buy_price = self.data.Close[current_index]
                stop_loss = buy_price - 0.0040  # 40点止损
                take_profit = buy_price + 0.0040 # 40点止盈
                self.buy(sl=stop_loss, tp=take_profit, size=0.3) # size代表仓位比例
            elif trade_signal == 1:  # 卖出信号
                sell_price = self.data.Close[current_index]
                stop_loss = sell_price + 0.0040
                take_profit = sell_price - 0.0040
                self.sell(sl=stop_loss, tp=take_profit, size=0.3)

# 运行回测
bt = Backtest(df, EMACrossoverStrategy, cash=100)
stats = bt.run()
print(stats)
```

在特定数据段（如1000根K线）的回测中，该策略可能获得约20%的回报，胜率约为66.6%。这表明策略具备一定的潜力。然而，在不同时间段测试时，结果会出现波动，有时会产生负回报，这提示策略有优化空间。

---

## 💎 总结

本节课中我们一起学习了一个基于三条指数移动平均线（EMA）的简单趋势跟踪策略的构建过程。

1.  **数据与指标**：我们学习了如何准备数据，并计算快速、中期、慢速三条EMA及其斜率，作为判断趋势的基础。
2.  **信号生成**：我们定义了结合EMA排列顺序、斜率方向以及价格与快速EMA交叉的规则，来识别趋势和生成具体的买入、卖出信号。
3.  **初步回测**：我们使用固定的仓位、止损和止盈规则对策略进行了初步回测，结果显示策略在部分时间段表现良好，但稳定性有待提高。

![](img/37d5c3061862396fae6ec48a2e090c95_3.png)

这个策略的核心逻辑简单直接，为初学者理解趋势跟踪和信号生成提供了清晰的范例。在接下来的课程中，我们可以在此基础上对止损止盈比例、仓位管理、参数优化等方面进行改进，以提升策略的稳健性和盈利能力。