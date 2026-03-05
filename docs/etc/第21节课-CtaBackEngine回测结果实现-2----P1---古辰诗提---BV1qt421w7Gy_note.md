# 量化交易：第21课：CtaBackEngine回测结果实现(2)

在本节课中，我们将继续完善回测引擎的结果计算模块。我们将学习如何计算持仓、平均成本、浮动盈亏、净利润、总资金以及胜率、盈亏比等关键绩效指标，并最终将结果数据可视化。

上一节我们介绍了如何计算手续费，本节中我们来看看如何计算持仓、浮动盈亏和净利润等核心数据。

## 计算持仓与平均成本

要计算浮动盈亏和净利润，首先需要知道持仓情况。我们可以通过交易记录来调整持仓量。

以下是调整持仓的代码逻辑：
```python
if trade.direction == Direction.LONG:
    self.pos = self.pos + trade.volume
else:
    self.pos = self.pos - trade.volume
```

调整持仓后，我们需要计算平均持仓成本。平均成本只在开仓时计算，平仓时不更新。

以下是计算平均持仓成本的代码逻辑：
```python
if trade.offset == Offset.OPEN:
    self.avg_price = (abs(self.pos) * self.avg_price + trade.price * trade.volume) / (abs(self.pos) + trade.volume)
```

**注意**：这种计算平均成本的方法在多次加减仓时可能不够精确。更精确的方法是使用列表记录每一笔开仓的成本，平仓时按先进先出（FIFO）或其他规则进行匹配。本教程为简化演示，采用平均成本法。

## 计算浮动盈亏与净利润

有了平均持仓成本，我们就可以计算浮动盈亏了。浮动盈亏基于当前K线的收盘价与平均成本的差值。

以下是计算浮动盈亏的公式：
`浮动盈亏 = (bar.close_price - self.avg_price) * self.pos * self.size`

接下来计算净利润。净利润在平仓时计算，是已实现的盈亏。

以下是计算净利润的逻辑：
```python
if trade.offset == Offset.CLOSE:
    if trade.direction == Direction.SHORT: # 平多仓
        profit = (trade.price - self.avg_price) * trade.volume * self.size
    elif trade.direction == Direction.LONG: # 平空仓
        profit = (self.avg_price - trade.price) * trade.volume * self.size

    self.net_profit += profit
    if profit > 0:
        self.win_count += 1
        self.total_win_profit += profit
    else:
        self.total_loss_profit += profit
    self.total_count += 1
```

**注意**：这种“开平算一笔”的统计方式在中间有加减仓时可能不够科学。更严谨的做法是在持仓量归零时，统计这一完整交易周期的总盈亏。本教程采用简化处理。

## 计算总资金与滑点

总资金等于初始资金加上净利润和浮动盈亏，再减去手续费和滑点成本。

以下是计算总资金的公式：
`总资金 = 初始资金 + 净利润 + 浮动盈亏 - 总手续费 - 总滑点成本`

滑点成本只在Stop单（市价单）成交时产生。我们需要在成交时根据方向调整成交价并累加滑点成本。

以下是处理滑点的代码逻辑：
```python
if trade.orderid.startswith(‘StopOrder.’):
    if trade.direction == Direction.LONG:
        trade.price = trade.price - self.slip
    elif trade.direction == Direction.SHORT:
        trade.price = trade.price + self.slip
    self.slip_total += self.slip * self.size
```

## 计算绩效指标

核心绩效指标包括盈亏比和胜率。

以下是计算盈亏比和胜率的逻辑：
```python
# 盈亏比
if self.total_loss_profit != 0:
    self.win_loss_rate = abs(self.total_win_profit / self.total_loss_profit)
else:
    self.win_loss_rate = 0

# 胜率
if self.total_count != 0:
    self.win_rate = self.win_count / self.total_count
else:
    self.win_rate = 0
```

此外，还可以计算持仓占比等指标。

以下是计算持仓占比的公式：
`持仓占比 = abs(self.pos) * bar.close_price * self.size * self.margin_rate / self.capital_total`

## 数据收集与存储

为了后续分析和绘图，我们需要按时间（例如每个交易日收盘）收集并存储上述数据到一个`DataFrame`中。

以下是创建和更新数据`DataFrame`的步骤：
1.  初始化一个空的`DataFrame`，并定义列名（如`datetime`, `capital_total`, `capital_net`等）。
2.  在每天特定时间（如下午3点），将各类数据存入一个字典。
3.  将字典作为新的一行，追加到`DataFrame`中。

## 结果可视化

最后，我们可以使用`pandas`和`matplotlib`将存储的资金曲线绘制出来，直观地展示策略表现。

以下是绘制资金曲线的示例代码：
```python
import pandas as pd
import matplotlib.pyplot as plt

# 从结果类中提取DataFrame
df_plot = pd.DataFrame()
df_plot[‘datetime’] = self.df[‘datetime’]
df_plot[‘capital_total’] = self.df[‘capital_total’]
df_plot[‘capital_net’] = self.df[‘capital_net’]

![](img/d728be757cf70103ea67eaf7cca9defd_1.png)

# 设置时间为索引
df_plot.set_index(‘datetime’, inplace=True)

![](img/d728be757cf70103ea67eaf7cca9defd_3.png)

![](img/d728be757cf70103ea67eaf7cca9defd_5.png)

# 绘图
df_plot.plot()
plt.show()
```

![](img/d728be757cf70103ea67eaf7cca9defd_7.png)

![](img/d728be757cf70103ea67eaf7cca9defd_9.png)

## 总结

![](img/d728be757cf70103ea67eaf7cca9defd_11.png)

![](img/d728be757cf70103ea67eaf7cca9defd_13.png)

本节课中我们一起学习了回测结果计算的完整流程。我们从调整持仓和计算平均成本开始，逐步实现了浮动盈亏、净利润、总资金的计算，并考虑了滑点的影响。随后，我们计算了盈亏比、胜率等关键绩效指标，学会了如何按时间收集这些数据并存储到`DataFrame`中。最后，我们利用`matplotlib`将资金曲线可视化，从而能够直观地评估策略的历史表现。至此，一个基础但完整的回测引擎核心计算模块就搭建完成了。下一节，我们将探讨如何对策略参数进行穷举优化。