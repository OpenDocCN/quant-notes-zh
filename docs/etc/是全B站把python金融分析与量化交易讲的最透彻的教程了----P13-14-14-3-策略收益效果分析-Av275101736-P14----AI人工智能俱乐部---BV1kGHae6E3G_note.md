# Python金融量化：P13：14.14.3-策略收益效果分析 📈

在本节课中，我们将学习如何计算和比较量化交易策略的收益。我们将通过一个具体的例子，分析双均线策略相对于“买入并持有”策略的收益表现。

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_1.png)

## 概述

上一节我们介绍了如何通过双均线策略生成交易信号（`position`）。本节中，我们来看看如何基于这些信号计算策略的最终收益，并与不做任何操作的原始股价增长进行对比。

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_3.png)

## 计算原始股价增长率

首先，我们需要计算股价的日增长率。增长率不能直接累加，我们通常使用对数收益率进行计算。

以下是计算对数收益率的公式和代码：

**公式**：`log_return = log(price_t / price_{t-1})`

**代码**：
```python
# 计算股价的对数收益率
returns = np.log(data['price'] / data['price'].shift(1))
```
这段代码计算了当前股价与前一日股价比值的自然对数，即对数收益率。

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_5.png)

## 计算策略收益率

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_6.png)

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_7.png)

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_8.png)

接下来，我们需要根据交易信号调整收益率。我们的策略信号`position`取值为1或-1。

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_9.png)

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_10.png)

*   当`position`为1时，代表跟随原始走势。
*   当`position`为-1时，代表进行反向操作（例如做空），其收益应与原始走势相反。

因此，策略收益率等于交易信号乘以原始对数收益率。同时，需要注意信号也需要进行`shift(1)`操作，以确保使用前一日生成的信号来影响当日的收益。

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_12.png)

以下是计算策略收益率的代码：
```python
# 计算策略收益率：用前一日信号乘以当日收益率
strategy_returns = position.shift(1) * returns
```

## 累计收益与结果还原

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_14.png)

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_15.png)

计算完每日的策略收益率后，我们需要将其累加起来，得到总的对数收益率。然后，通过指数函数将其还原为最终的资金倍数。

以下是计算累计收益并还原的步骤：

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_17.png)

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_18.png)

1.  对策略收益率序列进行累加求和。
2.  使用`np.exp()`函数将总对数收益率还原为资金增长倍数。

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_19.png)

**代码**：
```python
# 1. 累加所有日期的策略收益率（需先处理缺失值）
cumulative_log_return = strategy_returns.cumsum()

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_21.png)

# 2. 还原为最终资金倍数
final_value = np.exp(cumulative_log_return)
```

## 收益对比分析

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_23.png)

最后，我们将策略的最终收益与原始股价的累计收益进行对比。

以下是进行对比的代码示例：
```python
# 计算原始股价的累计收益（资金倍数）
buy_hold_return = np.exp(returns.cumsum())

# 计算策略的累计收益（资金倍数）
strategy_final_return = np.exp(strategy_returns.cumsum())

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_25.png)

# 打印对比结果
print(f"买入并持有策略最终资金倍数：{buy_hold_return.iloc[-1]:.2f}")
print(f"双均线策略最终资金倍数：{strategy_final_return.iloc[-1]:.2f}")
```
通过对比可以发现，在本例中，应用双均线策略后，1元初始资金的最终价值高于简单的“买入并持有”策略，这初步验证了该策略在特定数据上的有效性。

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_26.png)

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_27.png)

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_28.png)

## 总结

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_29.png)

![](img/992efb5ef9d2fd1bb9229fc7204fde2e_30.png)

本节课中我们一起学习了量化策略收益分析的核心步骤。我们首先计算了股价的对数收益率，然后根据交易信号生成了策略收益率序列，最后通过累加和指数还原，得到了策略的最终收益，并与基准收益进行了对比。这个过程是评估任何量化策略表现的基础。