# Python金融量化与股票交易：P14：策略收益效果分析 📈

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_1.png)

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_3.png)

在本节课中，我们将学习如何计算和比较股票策略的收益。我们将通过一个具体的例子，分析在应用“双均线策略”后，投资收益相较于不做任何策略（即简单持有）的变化。核心在于理解如何从股价数据计算日收益率，并应用策略信号来调整这些收益率，最终通过累加和对数还原得到总收益。

## 计算基础收益率

上一节我们介绍了如何生成交易信号。本节中，我们来看看如何基于股价数据计算基础的日收益率。

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_5.png)

以下是计算日收益率的步骤：
1.  获取股价数据列。
2.  计算每日股价相对于前一日股价的比率，即 `当日收盘价 / 前一日收盘价`。
3.  这个计算可以通过Pandas的 `shift(1)` 方法将数据下移一行，然后用当前行除以下移后的行来实现。

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_7.png)

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_9.png)

对应的代码公式如下：
```python
# 假设df是包含股价‘Close’列的DataFrame
returns = df[‘Close’] / df[‘Close’].shift(1)
```
执行这段代码后，`returns` 序列就代表了每日的增长率（例如，1.02表示增长2%）。

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_11.png)

## 应用策略调整收益率

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_13.png)

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_15.png)

我们已经有了基础的收益率和代表交易信号的 `position` 序列。现在，我们需要将策略应用到收益率上。

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_17.png)

我们的“双均线策略”生成的 `position` 序列中，数值为1代表我们遵循原始市场走势（即持有），数值为-1代表我们需要对当日的市场走势进行反向操作（例如，市场跌时我们通过策略实现盈利）。因此，策略调整后的收益率可以通过将基础收益率与 `position` 信号相乘得到。

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_19.png)

这里有一个关键点：为了确保信号作用于正确的交易日，`position` 序列也需要进行 `shift(1)` 操作，使其与收益率在时间上对齐。

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_21.png)

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_23.png)

应用策略的公式如下：
```python
# 假设returns是基础日收益率，position是交易信号
strategy_returns = returns * position.shift(1)
```
计算后，`strategy_returns` 序列就反映了应用我们策略后的每日收益情况。

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_25.png)

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_27.png)

## 计算并比较最终收益

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_29.png)

在得到调整后的日收益率序列后，我们需要计算一段时期内的总收益，并比较策略收益与基础收益。

以下是计算总收益的步骤：
1.  **处理缺失值**：由于使用了 `shift` 操作，序列开头会产生缺失值（NaN），需要使用 `dropna()` 方法将其移除。
2.  **累加对数收益**：在金融中，多期收益通常通过累加对数收益率来计算，这等价于计算收益率的连续复合增长。公式为：`总收益 = exp( sum( log(日收益率) ) )`。
3.  **执行计算**：分别对基础收益率序列 `returns` 和策略收益率序列 `strategy_returns` 执行上述操作。

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_31.png)

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_33.png)

对应的核心代码块如下：
```python
# 删除缺失值
returns_clean = returns.dropna()
strategy_clean = strategy_returns.dropna()

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_35.png)

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_37.png)

# 计算总收益：先取对数，再求和，最后指数还原
total_return_basic = np.exp(np.log(returns_clean).sum())
total_return_strategy = np.exp(np.log(strategy_clean).sum())
```
执行后，`total_return_basic` 代表“买入并持有”策略的总资产倍数，`total_return_strategy` 代表应用我们策略后的总资产倍数。通过比较两者，即可评估策略的有效性。例如，若初始为1元，最终基础收益为4元，策略收益为5.8元，则说明策略带来了额外的盈利。

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_39.png)

## 总结

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_41.png)

![](img/a3d39f58da754eaa90a0b3d94e2a0c2d_43.png)

本节课中我们一起学习了量化策略收益分析的全过程。我们首先从股价计算出日收益率，然后利用预先定义好的交易信号（`position`）对收益率进行调整，模拟策略操作。最后，我们通过累加和对数还原的方法，计算出策略的最终总收益，并与不做任何操作的基准收益进行对比，从而定量评估了“双均线策略”在示例数据上的增强效果。这个过程是量化交易中回测和评估策略性能的核心环节。