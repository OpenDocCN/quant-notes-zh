# Python金融分析与量化交易实战课程：P23：3-策略效果演示与指标分析 📊

在本节课中，我们将学习如何对量化策略进行效果演示与指标分析。我们将重点讲解如何调整数据结构、实现交易逻辑，并解读回测结果中的关键指标。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_1.png)

## 数据结构的调整

上一节我们介绍了如何筛选股票。本节中我们来看看如何将筛选结果转换为更直观的格式。

我们最初的想法是将股票名称横向排列，即第一个股票、第二个股票、第三个股票等。但通常大家更喜欢纵向排列，即第一个股票及其财务营收数据，第二个股票及其财务营收数据，以此类推。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_3.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_1.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_5.png)

因此，我们需要对结果进行修改。在执行结果后，我们需要对最终的 `DataFrame` 进行转置操作。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_7.png)

以下是具体操作步骤：
1.  对 `DataFrame` 进行转置。
2.  打印转置后的结果以验证正确性。

```python
# 对筛选结果进行转置
transposed_df = original_df.T
print(transposed_df)
```

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_9.png)

转置完成后，我们的目标不仅仅是打印查看，而是获取最终筛选出的股票列表。在 `before_trading` 函数中，我们最终需要得到的是这十只股票。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_11.png)

我们想要的结果是对数据先进行转置，然后查看转置结果。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_13.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_3.png)

转置结果如图所示，数据变为纵向排列：第一列是股票名称索引，后面是实际指标值。此时，我们只需要获取索引（即股票名称）即可。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_15.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_17.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_5.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_18.png)

接下来，我们将索引（股票名称）提取出来，并存入 `context` 对象中，作为后续交易使用的股票池。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_20.png)

```python
# 获取股票名称列表
selected_stocks = transposed_df.index.tolist()
# 将股票池存入 context
context.stock_pool = selected_stocks
```

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_22.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_24.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_7.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_26.png)

至此，我们在 `before_trading` 阶段完成了股票的筛选工作。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_28.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_9.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_30.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_32.png)

## 交易逻辑的实现

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_34.png)

接下来，我们进入 `handle_bar` 函数，实现具体的交易逻辑。首先，我们需要判断当前是否持有股票。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_36.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_38.png)

以下是实现交易逻辑的关键步骤：
1.  获取当前持仓信息。
2.  遍历持仓，卖出不在当前股票池中的股票。
3.  遍历股票池，买入当前未持有或需要调整仓位的股票。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_40.png)

我们需要参考API文档来编写代码。所有示例都是基于API文档实现的。在API中，`context` 对象包含投资组合信息。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_13.png)

具体来说，我们需要使用 `context.portfolio.positions`，这是一个包含所有持仓的字典，键为股票代码，值为持仓对象。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_42.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_15.png)

现在开始编写交易逻辑。首先，获取当前持仓字典。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_44.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_46.png)

```python
# 获取当前持仓
current_positions = context.portfolio.positions
```

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_48.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_17.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_50.png)

如果持仓字典为空，说明是首次运行，需要执行买入操作。如果不为空，则需要根据最新的股票池调整持仓：卖出池子外的股票，买入池子内的股票。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_52.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_54.png)

以下是具体的判断和操作流程：
1.  遍历当前持仓的股票代码。
2.  如果某只股票不在最新的股票池中，则将其全部卖出。
3.  遍历最新股票池中的股票。
4.  对每只股票进行买入操作，使其在投资组合中达到目标权重。

对于卖出操作，我们使用 `order_target_percent` 函数。该函数需要两个参数：股票代码和目标权重（占投资组合总价值的百分比）。设置目标权重为0，即表示全部卖出。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_56.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_20.png)

对于买入操作，同样使用 `order_target_percent` 函数。我们可以设定一个目标权重。为了简化，这里采用等权配置，即每只股票占投资组合的 `1 / 股票池数量`。

```python
# 计算等权配置下每只股票的权重
weight = 1.0 / len(context.stock_pool)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_58.png)

# 卖出不在股票池中的股票
for stock in current_positions.keys():
    if stock not in context.stock_pool:
        order_target_percent(stock, 0)

# 买入股票池中的股票，调整至目标权重
for stock in context.stock_pool:
    order_target_percent(stock, weight)
```

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_60.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_62.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_22.png)

## 回测结果与指标分析

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_64.png)

策略编写完成后，我们运行回测来查看结果。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_66.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_24.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_68.png)

回测结果可能因选择的时间段不同而有很大差异。我们需要关注结果页面中的几个关键指标。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_70.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_72.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_26.png)

以下是核心指标的解释：
*   **回测年化收益 vs 基准年化收益**：对比策略收益与市场基准（如沪深300指数）的收益。目标是策略收益能跑赢基准。
*   **最大回撤**：策略净值从高点回落的最大幅度，用于衡量策略的风险。数值越小，说明净值曲线越稳健。
*   **夏普比率**：衡量每承受一单位风险，能获得多少超额收益。正数且越大越好。如果策略收益为负，夏普比率通常也为负。

从结果概览中，我们可以直观比较策略与基准的表现。此外，右侧的“交易详情”可以查看每一笔具体的交易记录。

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_28.png)

![](img/7d9cc79d9c6184d7df6e0cd28fdce749_74.png)

本节课中我们一起学习了量化策略的完整实现流程：从数据整理、交易逻辑编码到回测结果分析。我们掌握了如何使用 `DataFrame` 转置、通过 `context.portfolio.positions` 管理持仓，以及利用 `order_target_percent` 函数执行买卖操作。最后，我们学会了解读回测报告中的关键绩效指标，为评估和优化策略奠定了基础。