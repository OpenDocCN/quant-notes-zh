# Python金融分析量化交易：P14：3-策略收益效果分析 📈

![](img/6d47b622a0d9b421a3e02b50bf98af46_1.png)

在本节课中，我们将学习如何计算和比较量化策略的收益。我们将从计算股票的基础收益率开始，然后应用我们之前构建的双均线策略，最终对比策略收益与原始持有收益的差异。

![](img/6d47b622a0d9b421a3e02b50bf98af46_3.png)

## 计算基础收益率

上一节我们介绍了双均线策略的信号生成，本节中我们来看看如何计算策略的最终收益。首先，我们需要计算股票每日的基础收益率。

在量化分析中，我们通常使用对数收益率，因为它具有更好的数学性质，便于进行累加计算。对数收益率的计算公式如下：

![](img/6d47b622a0d9b421a3e02b50bf98af46_5.png)

**公式：** `log_return = log(price_t / price_{t-1})`

![](img/6d47b622a0d9b421a3e02b50bf98af46_7.png)

在Python中，我们可以通过以下步骤计算：

![](img/6d47b622a0d9b421a3e02b50bf98af46_9.png)

```python
# 假设 df[‘Close’] 是股价序列
returns = np.log(df[‘Close’] / df[‘Close’].shift(1))
```

![](img/6d47b622a0d9b421a3e02b50bf98af46_11.png)

![](img/6d47b622a0d9b421a3e02b50bf98af46_13.png)

这段代码计算了每日股价相对于前一日股价的对数增长率。`shift(1)` 操作将数据向下移动一行，使得当前行能与前一日的数据进行对比。

![](img/6d47b622a0d9b421a3e02b50bf98af46_15.png)

## 应用策略计算策略收益

![](img/6d47b622a0d9b421a3e02b50bf98af46_17.png)

接下来，我们将策略信号应用到收益率上。我们的策略信号 `position` 是一个由0和1组成的序列，它指示了在每个时间点应该采取的行动。

以下是策略收益的计算逻辑：
*   当 `position` 为 **1** 时，表示我们遵循原始走势，即持有股票。
*   当 `position` 为 **0** 时，表示我们的策略判断此时应该进行反向操作（例如，在预测下跌时做空，或在模拟中将其视为与原始收益相反）。

![](img/6d47b622a0d9b421a3e02b50bf98af46_19.png)

因此，策略收益可以通过将 `position` 序列与基础收益率序列相乘得到。同样，我们需要对 `position` 序列进行 `shift(1)` 操作，以确保信号作用于正确的交易日。

![](img/6d47b622a0d9b421a3e02b50bf98af46_21.png)

![](img/6d47b622a0d9b421a3e02b50bf98af46_23.png)

```python
# 计算策略收益
strategy_returns = position.shift(1) * returns
```

![](img/6d47b622a0d9b421a3e02b50bf98af46_25.png)

## 累计收益与结果对比

![](img/6d47b622a0d9b421a3e02b50bf98af46_27.png)

![](img/6d47b622a0d9b421a3e02b50bf98af46_29.png)

在计算出每日的策略收益后，我们需要将其累加起来，以评估从起始点到结束点的总收益。由于我们使用的是对数收益率，累加操作就是简单的求和。

最后，我们将累计的对数收益率通过指数函数 `np.exp()` 还原为最终的资金倍数，从而直观地比较“什么都不做”的持有收益与执行双均线策略的收益。

![](img/6d47b622a0d9b421a3e02b50bf98af46_31.png)

```python
# 计算累计收益并还原为资金倍数
cumulative_returns = np.exp(returns.cumsum())
cumulative_strategy_returns = np.exp(strategy_returns.cumsum())

![](img/6d47b622a0d9b421a3e02b50bf98af46_33.png)

# 获取最终收益倍数
final_return = cumulative_returns.iloc[-1]
final_strategy_return = cumulative_strategy_returns.iloc[-1]
```

![](img/6d47b622a0d9b421a3e02b50bf98af46_35.png)

![](img/6d47b622a0d9b421a3e02b50bf98af46_37.png)

在我们的案例中，对比结果显示：
*   原始持有收益：1元本金最终变为约4元。
*   双均线策略收益：1元本金最终变为约5.8元。

这表明，在该段历史数据上，应用双均线策略获得了比单纯持有股票更高的回报。

![](img/6d47b622a0d9b421a3e02b50bf98af46_39.png)

## 总结

![](img/6d47b622a0d9b421a3e02b50bf98af46_41.png)

![](img/6d47b622a0d9b421a3e02b50bf98af46_43.png)

本节课中我们一起学习了量化策略收益分析的全过程。我们首先计算了股票的对数收益率，然后通过将策略信号与收益率结合，得到了策略的每日收益序列。最后，我们通过累加和指数还原，计算并对比了策略的最终收益与基准收益。这个过程是量化交易回测中评估策略有效性的核心步骤。