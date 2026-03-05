# Python金融时间序列分析与量化交易实战教程：P11：10.策略收益效果分析 📈

![](img/e8697ff84e79d8eec6db734f4fc84b74_0.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_2.png)

在本节课中，我们将学习如何计算和比较量化交易策略的收益。我们将通过一个具体的例子，分析使用双均线策略与不采取任何策略（即持有不动）两种情况下，投资回报的差异。

![](img/e8697ff84e79d8eec6db734f4fc84b74_4.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_6.png)

## 概述

![](img/e8697ff84e79d8eec6db734f4fc84b74_8.png)

上一节我们介绍了如何通过双均线策略生成交易信号（`position`）。本节中，我们来看看如何基于这些信号计算策略的实际收益，并与基准收益进行比较。核心在于理解如何将每日股价变动转化为对数收益率，并如何根据交易信号调整这些收益率。

![](img/e8697ff84e79d8eec6db734f4fc84b74_10.png)

## 计算对数收益率

![](img/e8697ff84e79d8eec6db734f4fc84b74_12.png)

首先，我们需要计算股票每日的对数收益率。这是量化金融中的标准做法，因为对数收益率具有更好的统计性质（如可加性）。

![](img/e8697ff84e79d8eec6db734f4fc84b74_14.png)

以下是计算对数收益率的步骤：
1.  获取股价序列。
2.  计算当前日股价与前一日股价的比值。
3.  对这个比值取自然对数。

![](img/e8697ff84e79d8eec6db734f4fc84b74_16.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_18.png)

在代码中，这可以通过对股价序列进行移位（`shift`）操作来实现。具体公式如下：

![](img/e8697ff84e79d8eec6db734f4fc84b74_20.png)

`log_return = np.log(price_t / price_{t-1})`

![](img/e8697ff84e79d8eec6db734f4fc84b74_22.png)

或者，在Pandas中更简洁的写法是：
```python
returns = np.log(data['close'] / data['close'].shift(1))
```
这里，`data['close'].shift(1)` 表示将收盘价序列向下移动一位，从而让当前行的价格与前一日价格对齐。

![](img/e8697ff84e79d8eec6db734f4fc84b74_24.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_26.png)

执行上述代码后，我们得到了一个表示每日增长率的 `returns` 序列。

## 根据策略调整收益率

![](img/e8697ff84e79d8eec6db734f4fc84b74_28.png)

接下来，我们需要根据上一节生成的交易信号（`position`）来调整收益率。我们的 `position` 序列中，数值为 `1` 代表按照市场原始方向操作（即做多），数值为 `-1` 代表反向操作（即做空或与市场走势相反）。

![](img/e8697ff84e79d8eec6db734f4fc84b74_30.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_32.png)

策略收益的计算逻辑是：用 `position` 信号乘以对应的对数收益率。这意味着：
*   当 `position` 为 `1` 时，策略收益等于市场原始收益。
*   当 `position` 为 `-1` 时，策略收益等于市场原始收益的相反数，实现了“反转”效果。

![](img/e8697ff84e79d8eec6db734f4fc84b74_34.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_36.png)

在应用时，需要注意对齐问题。`position` 信号也应该进行相应的移位操作，以确保信号作用于正确的交易日。计算策略收益的代码如下：
```python
strategy_returns = position.shift(1) * returns
```
这里 `position.shift(1)` 是为了避免使用未来数据，确保交易信号是基于之前的信息做出的。

![](img/e8697ff84e79d8eec6db734f4fc84b74_38.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_40.png)

## 计算累计收益并比较

![](img/e8697ff84e79d8eec6db734f4fc84b74_42.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_44.png)

得到调整后的策略收益率序列后，我们需要计算整个回测周期内的总收益，并与基准（不做任何操作，即始终持有）的总收益进行比较。

![](img/e8697ff84e79d8eec6db734f4fc84b74_46.png)

计算步骤如下：
1.  **处理缺失值**：由于使用了 `shift` 操作，序列开头会产生缺失值（NaN），需要先将其删除。
2.  **计算累计对数收益**：将对数收益率序列求和。因为对数收益率具有可加性，整个周期的总对数收益等于每日对数收益之和。
    `total_log_return = strategy_returns.dropna().sum()`
3.  **还原为实际收益**：将对数总收益通过指数函数还原为实际的总资产倍数。假设初始投资为1元，最终价值为：
    `final_value = np.exp(total_log_return)`

我们将对策略收益 (`strategy_returns`) 和基准收益 (`returns`) 分别执行上述步骤。

![](img/e8697ff84e79d8eec6db734f4fc84b74_48.png)

以下是核心计算与比较的代码框架：
```python
# 计算基准总收益
benchmark_final = np.exp(returns.dropna().sum())

![](img/e8697ff84e79d8eec6db734f4fc84b74_50.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_52.png)

# 计算策略总收益
strategy_final = np.exp(strategy_returns.dropna().sum())

![](img/e8697ff84e79d8eec6db734f4fc84b74_54.png)

print(f"基准最终价值： {benchmark_final:.2f}")
print(f"策略最终价值： {strategy_final:.2f}")
```
执行后，我们可能会得到类似这样的结果：“基准最终价值： 4.00” 和 “策略最终价值： 5.80”。这表明，在本例的回测周期内，应用双均线策略（1元变5.8元）比单纯持有股票（1元变4元）获得了更高的回报。

![](img/e8697ff84e79d8eec6db734f4fc84b74_56.png)

## 总结

![](img/e8697ff84e79d8eec6db734f4fc84b74_58.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_60.png)

本节课中我们一起学习了量化策略收益分析的核心流程。
1.  我们首先介绍了如何计算对数收益率。
2.  接着，讲解了如何利用交易信号（`position`）调整收益率，以反映策略的实际操作。
3.  最后，我们通过求和与指数还原，计算并比较了策略与基准的最终收益效果。

![](img/e8697ff84e79d8eec6db734f4fc84b74_62.png)

![](img/e8697ff84e79d8eec6db734f4fc84b74_64.png)

通过这个完整的分析，我们可以定量地评估一个交易策略在历史数据上的表现，这是量化交易中至关重要的一步。需要注意的是，本例中的策略在特定区间进行了“反转”操作，这在实际应用中需要严谨的逻辑和风险控制，本例主要用于演示收益计算流程。