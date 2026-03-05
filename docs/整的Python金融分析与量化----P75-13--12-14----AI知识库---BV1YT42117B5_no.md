# 量化交易完全可自学教程：P75：13. 12.14.3-最大回撤区间P14 📉

![](img/1edf17f31a4acc8f0076f3cc892d6729_0.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_2.png)

在本节课中，我们将要学习量化交易中的一个重要风险指标——最大回撤。我们将理解它的概念、意义，并学习如何通过Python代码进行计算。

## 概述

最大回撤是评估投资策略风险的关键指标。它描述的是在特定时间段内，资产净值从最高点到之后最低点的最大跌幅。简单来说，它衡量的是投资者可能经历的最糟糕的“惨劲儿”。

## 什么是最大回撤？

上一节我们介绍了各种策略评估指标，本节中我们来看看最大回撤。它不能仅从字面理解，其核心是描述投资过程中可能遭遇的最大、最持续的亏损区间。

![](img/1edf17f31a4acc8f0076f3cc892d6729_4.png)

假设你投资股票，价格有涨有跌。最大回撤指的就是从某个高点开始，到之后某个低点结束，这段期间内资产价值缩水最严重的那个阶段。它关注的是“最惨”的那段下跌旅程，而不是最终的盈亏结果。

![](img/1edf17f31a4acc8f0076f3cc892d6729_6.png)

**公式**可以表示为：
`最大回撤 = max[(Pi - Pj) / Pi]`，其中 `Pi` 是时间点 `i` 的资产价值，`Pj` 是 `i` 之后时间点 `j` 的资产价值（`j > i`）。

这个指标非常重要。即使一个策略长期是盈利的，但如果其最大回撤非常大，意味着投资者在过程中可能需要承受巨大的心理压力和资金风险。例如，2008年股市从6000多点暴跌至1600多点，就是一次巨大的回撤，许多投资者难以承受。

![](img/1edf17f31a4acc8f0076f3cc892d6729_8.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_10.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_12.png)

## 如何计算最大回撤？

![](img/1edf17f31a4acc8f0076f3cc892d6729_14.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_16.png)

理解了概念后，我们来看看如何用Python进行计算。核心思路是：遍历所有可能的高点（`Pi`），找到其之后的最低点（`Pj`），计算所有 `(Pi - Pj)/Pi` 中的最大值。

![](img/1edf17f31a4acc8f0076f3cc892d6729_18.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_20.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_22.png)

计算过程可以借助Pandas的 `cummax()` 函数来高效完成。`cummax()` 会计算序列截至当前点的历史最大值。

![](img/1edf17f31a4acc8f0076f3cc892d6729_24.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_26.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_28.png)

以下是计算最大回撤的步骤分解：

![](img/1edf17f31a4acc8f0076f3cc892d6729_30.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_32.png)

1.  首先，我们有一个资产净值序列 `price_series`。
2.  计算该序列的历史最高值序列：`historical_max = price_series.cummax()`。
3.  计算每一天的“回撤值”：当天的历史最高值减去当天的实际净值，即 `drawdown = historical_max - price_series`。
4.  计算每一天的“回撤率”：回撤值除以对应的历史最高值，即 `drawdown_ratio = drawdown / historical_max`。
5.  所有回撤率中的最大值，即为**最大回撤率**。

![](img/1edf17f31a4acc8f0076f3cc892d6729_34.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_36.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_38.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_40.png)

让我们通过代码示例来演示：

![](img/1edf17f31a4acc8f0076f3cc892d6729_42.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_44.png)

```python
import pandas as pd

![](img/1edf17f31a4acc8f0076f3cc892d6729_46.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_48.png)

# 假设这是我们的资产净值序列（例如每日收盘价）
price_data = [100, 105, 102, 98, 96, 110, 108, 115, 112, 120]
price_series = pd.Series(price_data)

![](img/1edf17f31a4acc8f0076f3cc892d6729_50.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_52.png)

# 步骤1: 计算历史最高值序列
historical_max = price_series.cummax()
print("历史最高值序列:\n", historical_max)

![](img/1edf17f31a4acc8f0076f3cc892d6729_54.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_56.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_58.png)

# 步骤2 & 3: 计算每日回撤值和回撤率
drawdown_value = historical_max - price_series
drawdown_ratio = drawdown_value / historical_max
print("\n每日回撤率序列:\n", drawdown_ratio)

![](img/1edf17f31a4acc8f0076f3cc892d6729_60.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_62.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_64.png)

# 步骤4: 计算最大回撤率
max_drawdown = drawdown_ratio.max()
print(f"\n最大回撤率为: {max_drawdown:.2%}")
```

![](img/1edf17f31a4acc8f0076f3cc892d6729_66.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_68.png)

运行这段代码，你将看到如何从价格数据一步步计算出最大回撤。在实际的回测框架中，这些计算通常已被封装好，但理解其原理至关重要。

![](img/1edf17f31a4acc8f0076f3cc892d6729_70.png)

## 总结

![](img/1edf17f31a4acc8f0076f3cc892d6729_72.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_74.png)

本节课中我们一起学习了最大回撤这个核心风险指标。我们明确了它衡量的是投资过程中资产价值的最大峰值到谷底的跌幅，反映了策略可能带来的最糟糕体验。同时，我们通过公式和Python代码演示了其计算过程，即通过 `cummax()` 函数辅助，找出所有回撤率中的最大值。

![](img/1edf17f31a4acc8f0076f3cc892d6729_76.png)

![](img/1edf17f31a4acc8f0076f3cc892d6729_78.png)

记住，一个优秀的策略不仅要看最终收益率，还必须严格控制最大回撤，这样才能在实际投资中具备更强的可执行性和风险抵御能力。