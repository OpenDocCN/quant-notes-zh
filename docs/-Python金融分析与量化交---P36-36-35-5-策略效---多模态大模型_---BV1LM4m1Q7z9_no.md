# Python金融分析与量化交易实战：P36：策略效果评估分析P35 - 多模态大模型

![](img/d1a43cac7c7d73ee5d7a21546db766c2_0.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_2.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_0.png)

在本节课中，我们将学习如何实现一个完整的因子选股策略，并对其进行回测评估。我们将重点讲解策略的调仓逻辑、代码实现细节以及如何解读回测结果。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_4.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何具体实现调仓操作并评估策略效果。

## 策略调仓逻辑实现

首先，我们需要从当前持仓中移除不符合选股条件的股票。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_6.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_8.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_2.png)

接下来，我们需要进行判断。如果确实有股票被移除，即需要执行调仓操作，我们就执行以下步骤。

因为当前持仓中有一些股票不再满足我们的选股要求，所以这是一个非零的操作。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_4.png)

现在，我们需要完成调仓任务。为了方便观察计时器是否正常工作，我们可以打印一条日志。然后开始执行调仓操作。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_10.png)

以下是执行调仓操作的步骤：

![](img/d1a43cac7c7d73ee5d7a21546db766c2_12.png)

首先，对于每一只需要卖出的股票，我们将其持仓比例调整为0，即全部卖出。这里使用 `order_target_percent` 函数。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_14.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_6.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_16.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_18.png)

在 `order_target_percent` 函数中，我们需要指定操作对象（股票代码）和目标持仓比例（0）。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_20.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_8.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_22.png)

第一步是卖出操作，代码如下：
```python
order_target_percent(stock, 0)
```

![](img/d1a43cac7c7d73ee5d7a21546db766c2_24.png)

卖出操作完成后，接下来需要买入新的股票。对于筛选后股票池中的每一只股票，我们执行买入操作。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_26.png)

此时，目标持仓比例不再是0，而是将资金平均分配到每只股票上。我们可以用1除以股票池中股票的数量来计算每只股票应占的比例。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_28.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_30.png)

代码如下：
```python
for stock in stock_pool:
    order_target_percent(stock, 1.0 / len(stock_pool))
```

![](img/d1a43cac7c7d73ee5d7a21546db766c2_32.png)

这样，我们就完成了买入操作。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_34.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_36.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_10.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_38.png)

至此，调仓逻辑编写完成。这意味着每个月我们都需要检查股票池，并及时执行相应的调仓操作。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_12.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_40.png)

以上是我们实现策略调仓所需的所有核心函数。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_42.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_44.png)

## 代码整合与运行调试

![](img/d1a43cac7c7d73ee5d7a21546db766c2_46.png)

我们将所有代码整合到 `rebalance` 函数中。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_48.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_14.png)

代码编写完成后，我们尝试运行，看看是否存在问题。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_50.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_16.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_52.png)

我们直接在 `rebalance` 函数中编写所有逻辑。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_54.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_56.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_18.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_58.png)

运行程序，首先检查是否能正常启动。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_60.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_20.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_62.png)

如果能正常运行，我们再观察输出结果。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_64.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_66.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_22.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_68.png)

直接查看输出日志。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_70.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_72.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_24.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_74.png)

程序可能会报错，我们根据错误信息进行修改。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_76.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_26.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_78.png)

例如，可能在数据中性化处理时缺少参数。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_80.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_28.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_82.png)

检查中性化函数，发现需要传入因子名称参数。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_84.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_30.png)

修正参数后再次运行。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_86.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_32.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_88.png)

可能还会遇到其他错误，例如拼写错误或逻辑遗漏。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_90.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_34.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_92.png)

我们需要仔细检查代码，例如在选股时使用的分位数函数 `quantile` 是否拼写正确。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_36.png)

修正所有错误后，程序应能通过编译并开始执行回测。

## 回测过程与结果分析

![](img/d1a43cac7c7d73ee5d7a21546db766c2_94.png)

程序开始运行后，我们可以看到调仓日志。例如，在2月1日和3月1日执行了调仓。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_38.png)

回测速度可能较慢，因为我们选择了一年的数据，并且是每日进行计算，但调仓是每月执行一次。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_96.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_40.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_98.png)

等待回测完成，我们可以观察策略的收益曲线。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_100.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_42.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_102.png)

在回测期间，基准收益可能是负的，而我们的策略收益可能为正或负值较小，这表明策略产生了一定的效果。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_104.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_44.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_106.png)

最终，我们可以查看完整的回测报告，包括年化收益率、夏普比率、最大回撤等关键指标。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_108.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_46.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_110.png)

虽然这些指标可能不算很高，但相比基准可能有所改善。这验证了我们基于市值和市盈率等因子进行选股的思路。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_112.png)

## 策略流程总结

![](img/d1a43cac7c7d73ee5d7a21546db766c2_114.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_116.png)

本节课中我们一起学习了完整的因子选股策略实现与评估流程。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_118.png)

整个策略遵循以下三步走的方法：
1.  **设定调仓周期**：在 `rebalance` 函数中通过定时器设定，例如每月调仓。
2.  **筛选股票池**：基于选定的因子（如市值、市盈率）查询数据，并进行预处理（如中性化）和筛选（如选择因子值较低的股票）。
3.  **执行调仓操作**：
    *   卖出当前持有但不在新股票池中的股票。使用 `order_target_percent(stock, 0)`。
    *   买入新股票池中的所有股票，并平均分配资金。使用 `order_target_percent(stock, 1.0 / len(stock_pool))`。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_120.png)

通过回测，我们可以评估策略的历史表现，观察其是否能够跑赢基准。这为我们验证投资想法提供了一个有效的模拟工具。

![](img/d1a43cac7c7d73ee5d7a21546db766c2_122.png)

![](img/d1a43cac7c7d73ee5d7a21546db766c2_48.png)

最后，可以查看详细的交易记录和账户信息变化，以更深入地理解策略的运行细节。