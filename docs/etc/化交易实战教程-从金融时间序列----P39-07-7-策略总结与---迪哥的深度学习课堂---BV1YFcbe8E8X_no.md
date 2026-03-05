# Python金融分析与量化交易实战：P39：07-7-策略总结与分析 📊

在本节课中，我们将对之前构建的因子选股策略进行回测总结与分析。我们将通过调整回测时间区间和股票选择范围，来观察策略在不同市场环境下的表现，并理解策略的有效性。

![](img/20203f95ded68d9b1706ac6a77c37efe_0.png)

## 策略回测执行与初步观察

![](img/20203f95ded68d9b1706ac6a77c37efe_2.png)

上一节我们介绍了策略的构建，本节中我们来看看策略的回测结果。代码执行完成后，没有出现报错，说明策略逻辑可以正常运行。

![](img/20203f95ded68d9b1706ac6a77c37efe_0.png)

首先观察一个较短时间区间的回测结果。基准收益为负，而我们的策略虽然盈利不多，但基本稳住了净值。最大回撤约为17%，表现尚可。策略的超额收益维持在67%左右，效果不错。

![](img/20203f95ded68d9b1706ac6a77c37efe_2.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_4.png)

## 调整回测时间区间

为了更全面地评估策略，我们需要测试更长的回测周期。通常，量化回测会选择3到5年的时间，以避免短期市场噪音的干扰。

以下是调整回测时间区间的操作：

![](img/20203f95ded68d9b1706ac6a77c37efe_6.png)

*   将起始日期设置为`2016-01-04`，结束日期设置为`2018-01-04`，进行为期两年的回测。
*   结果显示，在此时间段内，策略收益与基准收益相差不大，没有表现出明显的超额收益。

![](img/20203f95ded68d9b1706ac6a77c37efe_4.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_8.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_6.png)

接着，我们将回测区间调整为`2018-01-04`至`2020-01-04`。

![](img/20203f95ded68d9b1706ac6a77c37efe_10.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_8.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_12.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_10.png)

策略逻辑和指标计算均无误，但在此区间内，策略效果依然不显著。基准收益为0点几，策略收益为2点几，提升有限。这可能与2018年的熊市环境有关。

![](img/20203f95ded68d9b1706ac6a77c37efe_14.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_12.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_16.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_14.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_18.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_16.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_20.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_18.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_20.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_22.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_22.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_24.png)

## 测试策略选股逻辑的有效性

为了避开明显的熊市，我们测试`2016-01-04`至`2017-01-04`这个市场相对较好的时间段。结果显示，策略的回测收益确实高于基准收益。

![](img/20203f95ded68d9b1706ac6a77c37efe_24.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_26.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_26.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_28.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_28.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_30.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_30.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_32.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_32.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_34.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_34.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_36.png)

为了验证选股逻辑是否有效，我们进行反向测试。原策略是选择综合得分**排名前10**的股票。现在，我们修改代码，选择排名**最后（最差）** 的股票组合。

![](img/20203f95ded68d9b1706ac6a77c37efe_36.png)

在代码中，将选股范围从 `ranked_stocks.iloc[:10]`（前10名）改为 `ranked_stocks.iloc[-10:]`（后10名）。

![](img/20203f95ded68d9b1706ac6a77c37efe_38.png)

```python
# 原策略：选择得分最高的前10只股票
selected_stocks = ranked_stocks.iloc[:10]

![](img/20203f95ded68d9b1706ac6a77c37efe_40.png)

# 反向测试：选择得分最低的后10只股票
selected_stocks = ranked_stocks.iloc[-10:]
```

执行反向测试后，结果符合预期：选择“最差”股票组合的收益，比市场基准收益更差。这从反面证明了我们的选股因子（得分越高越好）是有效的。

![](img/20203f95ded68d9b1706ac6a77c37efe_42.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_38.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_40.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_44.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_42.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_46.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_44.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_46.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_48.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_48.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_50.png)

## 回测报告详情分析

![](img/20203f95ded68d9b1706ac6a77c37efe_52.png)

回测系统提供了详细的报告，我们可以进一步查看。

以下是回测报告包含的主要部分：

![](img/20203f95ded68d9b1706ac6a77c37efe_54.png)

*   **交易详情**：记录了每次调仓的日期和操作。可以看到策略是每月定期调仓的。
*   **持仓详情**：展示了每个调仓日持有的具体股票和数量。
*   **账户信息**：展示了初始资金、最终资金、收益率等。例如，初始100万资金，在反向测试中最终变为77万。

![](img/20203f95ded68d9b1706ac6a77c37efe_56.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_50.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_52.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_58.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_54.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_56.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_60.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_58.png)

## 课程总结

![](img/20203f95ded68d9b1706ac6a77c37efe_62.png)

本节课中我们一起学习了如何对量化策略进行总结与分析。我们将代码改回默认选择前10名股票的策略。**打分法**在量化选股中应用广泛，它直接明了，通常能获得相对不错的结果。

![](img/20203f95ded68d9b1706ac6a77c37efe_60.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_64.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_62.png)

需要明确的是，任何策略的表现都受市场环境影响。在熊市中，策略收益可能表现平平，这是正常现象。通过本节课不同时间区间和正反向的测试，我们验证了策略逻辑的有效性，也理解了市场周期对策略表现的巨大影响。

![](img/20203f95ded68d9b1706ac6a77c37efe_64.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_66.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_66.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_68.png)

![](img/20203f95ded68d9b1706ac6a77c37efe_68.png)