# Python量化55：追踪指数2 - P1 - LuQuant

在本教程中，我们将学习如何在Python中实现追踪止损策略，并将其应用于一个基于支撑/阻力位和烛台模式的交易策略中。我们将通过回测来评估该策略的表现。

![](img/e7df57a31adf1143f3426913d9c2a7e9_1.png)

## 概述

追踪止损是一种动态的止损管理方法，它允许止损位随着盈利的增加而移动，从而在保护利润的同时，最大化潜在收益。本节我们将把此方法整合到已有的策略框架中。

## 策略回顾

![](img/e7df57a31adf1143f3426913d9c2a7e9_3.png)

![](img/e7df57a31adf1143f3426913d9c2a7e9_5.png)

上一节我们介绍了基于自动检测支撑/阻力位和烛台模式（如看涨吞没、流星形态）来生成交易信号的策略。策略会在特定信号出现时执行买入或卖出订单。

![](img/e7df57a31adf1143f3426913d9c2a7e9_7.png)

## 实现追踪止损

本节中，我们来看看如何在已有策略中加入追踪止损逻辑。核心思想是：当价格朝有利方向移动时，止损位随之移动；当价格回调时，止损位保持不变，直至被触发。

### 核心概念与代码

我们将使用 `backtesting.py` 库进行回测。策略类的核心是重写 `init()` 和 `next()` 方法，并在 `next()` 方法中实现追踪止损逻辑。

以下是追踪止损的核心代码片段，用于多头头寸：

```python
# 假设 self.stop_trail_distance 是预设的追踪距离
for trade in self.trades:
    if trade.is_long and trade.sl:
        # 计算新的止损位：当前收盘价减去追踪距离
        new_sl = self.data.Close[-1] - self.stop_trail_distance
        # 止损位只上移，不下移
        trade.sl = max(trade.sl, new_sl)
```

对于空头头寸，逻辑相反，止损位只下移，不上移：

```python
elif trade.is_short and trade.sl:
    new_sl = self.data.Close[-1] + self.stop_trail_distance
    trade.sl = min(trade.sl, new_sl)
```

### 整合到交易策略中

我们将上述逻辑整合到完整的策略类中。策略会在检测到信号且无未平仓头寸时开仓，并为该头寸设置初始追踪止损。

以下是开仓并设置初始止损的代码：

```python
if self.signal == 2 and len(self.trades) == 0: # 买入信号且无持仓
    sl_price = self.data.Close[-1] - self.stop_trail_distance
    self.buy(sl=sl_price)
elif self.signal == 1 and len(self.trades) == 0: # 卖出信号且无持仓
    sl_price = self.data.Close[-1] + self.stop_trail_distance
    self.sell(sl=sl_price)
```

## 策略优化与回测结果

我们使用欧元/美元2003年至2021年的日线数据进行了回测。通过调整 `stop_trail_distance` 等参数，我们寻找较优的策略表现。

### 关键参数与结果

经过测试，追踪距离 `stop_trail_distance = 500 * 10e-5`（即50点）在此数据集上表现较好。

以下是该策略的主要回测统计结果：

*   **总回报率**: 161%
*   **胜率**: 69%
*   **最大回撤**: -10%
*   **平均交易持续时间**: 约30天

### 进一步优化：处理反向信号

为了减少持仓时间过长和应对市场反转，我们增加了条件：当出现反向交易信号时，立即平掉当前头寸。

以下是增加的平仓条件代码：

```python
for trade in self.trades:
    if trade.is_long and self.signal == 1: # 持有多头时出现卖出信号
        trade.close()
    elif trade.is_short and self.signal == 2: # 持有空头时出现买入信号
        trade.close()
```

加入此条件后，策略表现有所提升：

*   **总回报率提升至**: 228%
*   **最大回撤**: -8.5%
*   **平均交易持续时间缩短至**: 约30天

![](img/e7df57a31adf1143f3426913d9c2a7e9_9.png)

## 总结

![](img/e7df57a31adf1143f3426913d9c2a7e9_11.png)

本节课中我们一起学习了追踪止损的实现方法。我们将其整合到一个基于技术分析的交易策略中，并通过回测验证了其有效性。关键点在于：
1.  追踪止损能动态保护利润。
2.  通过参数优化（如 `stop_trail_distance`）可以改善策略表现。
3.  引入反向信号平仓机制，可以进一步控制风险并优化资金使用效率。

![](img/e7df57a31adf1143f3426913d9c2a7e9_13.png)

这是一个基础框架，在实际应用中还需考虑交易成本、滑点以及在不同时间框架下的参数适应性。