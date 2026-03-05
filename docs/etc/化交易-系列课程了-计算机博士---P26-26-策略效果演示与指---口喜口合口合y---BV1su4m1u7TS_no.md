# Python金融分析+量化交易：P26：策略效果演示与指标分析 📊

![](img/4434b3fb6275f28eaf9365218cb2c59b_1.png)

在本节课中，我们将学习如何对量化策略进行效果演示与指标分析。我们将通过一个具体的例子，展示如何调整数据结构、执行交易逻辑，并最终解读策略回测报告中的关键指标。

---

上一节我们介绍了策略的初始化与数据筛选，本节中我们来看看如何执行交易逻辑并分析策略效果。

![](img/4434b3fb6275f28eaf9365218cb2c59b_3.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_5.png)

我们最初的想法是将数据横向排列，即每个股票名称作为一行。但通常大家更喜欢纵向排列数据，即每个股票名称作为一列，其财务指标如营收等数据纵向展示。

![](img/4434b3fb6275f28eaf9365218cb2c59b_7.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_1.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_9.png)

因此，我们需要对数据结构进行调整。在执行筛选操作的结尾，我们需要对最终的`DataFrame`结果进行转置操作。

![](img/4434b3fb6275f28eaf9365218cb2c59b_11.png)

**代码示例：**
```python
# 对DataFrame进行转置
transposed_df = original_df.T
print(transposed_df)
```

![](img/4434b3fb6275f28eaf9365218cb2c59b_13.png)

转置完成后，我们检查结果是否正确。如果正确，则继续后续步骤。转置操作本身并不是最终目的。

![](img/4434b3fb6275f28eaf9365218cb2c59b_15.png)

在`before_trading`函数中，我们的最终目标不是仅仅打印数据，而是筛选出目标股票。因此，我们最终需要得到的是转置后数据的索引，即股票名称列表。

![](img/4434b3fb6275f28eaf9365218cb2c59b_17.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_19.png)

以下是转置后的结果示例：

![](img/4434b3fb6275f28eaf9365218cb2c59b_21.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_3.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_23.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_25.png)

可以看到，数据现在是纵向排列的：

![](img/4434b3fb6275f28eaf9365218cb2c59b_27.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_29.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_31.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_5.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_33.png)

每一行代表一个股票名称及其对应的指标值。此时，我们只需要获取索引部分，即股票名称。

![](img/4434b3fb6275f28eaf9365218cb2c59b_35.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_37.png)

**代码示例：**
```python
# 获取转置后DataFrame的索引（股票名称）
stock_list = transposed_df.index.tolist()
```

![](img/4434b3fb6275f28eaf9365218cb2c59b_39.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_41.png)

我们将这个股票列表存入`context`上下文中，作为后续交易使用的股票池。这样，在交易开始前，我们就完成了股票的筛选工作。

![](img/4434b3fb6275f28eaf9365218cb2c59b_43.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_7.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_45.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_47.png)

接下来，我们进入`handle_bar`函数，开始编写实际的交易逻辑。

![](img/4434b3fb6275f28eaf9365218cb2c59b_49.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_51.png)

第一步是判断当前账户的持仓情况。我们需要参考API文档来了解如何获取持仓信息。

![](img/4434b3fb6275f28eaf9365218cb2c59b_53.png)

API文档中包含了丰富的解释和示例。我们需要找到与交易和组合信息相关的部分。

在API中，`context.portfolio.positions`是一个字典，它包含了当前所有持仓的信息。我们可以通过检查这个字典来判断当前是否持有股票。

![](img/4434b3fb6275f28eaf9365218cb2c59b_55.png)

**代码示例：**
```python
# 获取当前持仓字典
positions = context.portfolio.positions
```

![](img/4434b3fb6275f28eaf9365218cb2c59b_57.png)

首先进行判断：如果持仓字典为空，说明当前没有持有任何股票。如果不为空，则说明从第二天开始，我们需要根据最新的股票池进行调整。

![](img/4434b3fb6275f28eaf9365218cb2c59b_59.png)

当持仓不为空时，我们需要卖出那些不在最新股票池中的股票。以下是具体的操作步骤：

![](img/4434b3fb6275f28eaf9365218cb2c59b_61.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_63.png)

1.  遍历当前持有的所有股票（即`positions.keys()`）。
2.  判断每只股票是否存在于我们刚刚筛选出的最新股票池中。
3.  如果某只股票不在股票池中，则将其卖出。

![](img/4434b3fb6275f28eaf9365218cb2c59b_65.png)

关于卖出操作，API提供了`order_target_percent`函数，它允许我们通过设置目标百分比来调整仓位。

![](img/4434b3fb6275f28eaf9365218cb2c59b_67.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_69.png)

**函数公式：**
`order_target_percent(security, target_percent)`
-   `security`: 要操作的股票代码。
-   `target_percent`: 该股票在投资组合中期望占有的目标百分比（0到1之间）。设置为0表示全部卖出。

**代码示例（卖出不在股票池中的股票）：**
```python
for stock in positions.keys():
    if stock not in context.stock_pool: # 假设股票池保存在context.stock_pool中
        # 卖出该股票，目标持仓比例设为0
        order_target_percent(stock, 0)
```

![](img/4434b3fb6275f28eaf9365218cb2c59b_71.png)

完成卖出操作后，接下来需要买入股票池中我们尚未持有的股票。同样使用`order_target_percent`函数。

**代码示例（买入股票池中的股票）：**
```python
# 计算每只股票应占的平均仓位比例
avg_weight = 1.0 / len(context.stock_pool)
for stock in context.stock_pool:
    # 买入该股票，目标持仓比例设为平均权重
    order_target_percent(stock, avg_weight)
```

![](img/4434b3fb6275f28eaf9365218cb2c59b_73.png)

这样，我们就完成了一个简单的“调仓”逻辑：卖出不再需要的股票，并平均买入股票池中的所有股票。

![](img/4434b3fb6275f28eaf9365218cb2c59b_75.png)

策略编写完成后，我们运行回测来查看结果。回测报告会包含一系列评估策略表现的指标。

![](img/4434b3fb6275f28eaf9365218cb2c59b_77.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_83.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_79.png)

回测结果可能因选择的时间段不同而有很大差异。我们需要重点关注以下几个核心指标：

![](img/4434b3fb6275f28eaf9365218cb2c59b_81.png)

1.  **回测年化收益 vs 基准年化收益**：这对比了我们的策略收益与市场基准（如沪深300指数）的收益。目标是策略收益能跑赢基准。
2.  **最大回撤**：这衡量了策略在历史上从高点下跌的最大幅度，反映了策略的风险。数值越小越好。
3.  **夏普比率**：这衡量了每承担一单位风险所获得的超额回报。正值且越大越好。如果策略收益为负，夏普比率通常也为负。

![](img/4434b3fb6275f28eaf9365218cb2c59b_83.png)

在示例的回测结果中，策略和基准都出现了亏损，但策略的亏损幅度小于基准，最大回撤控制在17%左右。由于总体收益为负，夏普比率也为负值。

![](img/4434b3fb6275f28eaf9365218cb2c59b_85.png)

![](img/4434b3fb6275f28eaf9365218cb2c59b_87.png)

此外，回测报告还提供了详细的交易记录，可以点击查看每一笔交易的具体情况。

![](img/4434b3fb6275f28eaf9365218cb2c59b_89.png)

---

![](img/4434b3fb6275f28eaf9365218cb2c59b_89.png)

本节课中我们一起学习了量化策略从数据调整到交易执行，再到效果评估的完整流程。我们掌握了如何使用`DataFrame.T`进行数据转置，如何利用`context.portfolio.positions`管理持仓，以及如何使用`order_target_percent`函数执行买卖操作。最后，我们学会了解读回测报告中的关键指标，如年化收益、最大回撤和夏普比率，这些都是评估策略好坏的重要依据。