# Python金融分析量化交易：P26：3-策略效果演示与指标分析 📊

![](img/f9c7422eaaf797b5890b26501d3b538d_1.png)

在本节课中，我们将学习如何对量化策略进行效果演示与指标分析。我们将重点讲解如何调整数据结构、实现交易逻辑，并解读回测结果中的关键指标。

![](img/f9c7422eaaf797b5890b26501d3b538d_3.png)

## 数据结构的调整

![](img/f9c7422eaaf797b5890b26501d3b538d_5.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_7.png)

上一节我们介绍了如何筛选股票，本节我们来看看如何处理筛选后的数据。我们的想法是将数据从横向排列（每个股票名作为一列）调整为更符合阅读习惯的纵向排列（每个股票名作为一行）。

![](img/f9c7422eaaf797b5890b26501d3b538d_9.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_11.png)

以下是调整数据结构的步骤：

![](img/f9c7422eaaf797b5890b26501d3b538d_13.png)

1.  首先，我们需要对存储筛选结果的 `DataFrame` 进行转置操作。
2.  转置后，数据将变为纵向排列，每一行代表一只股票及其对应的财务指标。
3.  我们的最终目标是获取这十只股票的代码列表。

![](img/f9c7422eaaf797b5890b26501d3b538d_15.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_17.png)

```python
# 假设 df_result 是筛选后的DataFrame
df_transposed = df_result.T  # 进行转置操作
print(df_transposed)  # 查看转置后的结果
```

![](img/f9c7422eaaf797b5890b26501d3b538d_19.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_21.png)

转置后的结果如下图所示，可以看到股票名称和对应的指标值已变为纵向排列：

![](img/f9c7422eaaf797b5890b26501d3b538d_23.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_25.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_3.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_27.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_28.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_30.png)

此时，我们只需要获取转置后 `DataFrame` 的索引，即可得到我们想要的股票代码列表。

![](img/f9c7422eaaf797b5890b26501d3b538d_32.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_34.png)

```python
stock_list = df_transposed.index.tolist()  # 获取股票代码列表
context.stock_pool = stock_list  # 将列表存入context中，供后续交易函数使用
```

![](img/f9c7422eaaf797b5890b26501d3b538d_36.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_38.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_40.png)

至此，我们在 `before_trading` 函数中完成了股票的筛选工作，并将结果存储起来。

![](img/f9c7422eaaf797b5890b26501d3b538d_42.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_44.png)

## 交易逻辑的实现

![](img/f9c7422eaaf797b5890b26501d3b538d_46.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_48.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_50.png)

接下来，我们进入 `handle_bar` 函数，实现具体的交易逻辑。本节中我们来看看如何根据每日筛选的股票池进行买卖操作。

![](img/f9c7422eaaf797b5890b26501d3b538d_52.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_54.png)

第一步是判断当前账户的持仓情况。我们可以通过 `context.portfolio.positions` 来获取一个包含所有持仓信息的字典。

以下是实现交易逻辑的步骤：

![](img/f9c7422eaaf797b5890b26501d3b538d_56.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_58.png)

1.  首先，检查当前持仓字典是否为空。如果为空，说明是策略运行的第一天，我们将执行买入操作。
2.  如果持仓不为空，则需要对比当前持仓与今日筛选出的股票池。
3.  对于持仓中但不在今日股票池的股票，执行卖出操作。
4.  对于在今日股票池但尚未持仓的股票，执行买入操作。

![](img/f9c7422eaaf797b5890b26501d3b538d_60.png)

```python
def handle_bar(context):
    # 获取当前持仓
    current_positions = context.portfolio.positions

    # 获取今日筛选的股票池
    target_stocks = context.stock_pool

    # 如果当前没有持仓，则执行初始买入
    if not current_positions:
        for stock in target_stocks:
            # 平均分配资金买入股票池中的所有股票
            order_target_percent(stock, 1.0 / len(target_stocks))
    else:
        # 卖出不在目标股票池中的持仓
        for stock in current_positions.keys():
            if stock not in target_stocks:
                order_target_percent(stock, 0)  # 卖出全部持仓

        # 买入目标股票池中的股票（平均分配资金）
        for stock in target_stocks:
            order_target_percent(stock, 1.0 / len(target_stocks))
```

![](img/f9c7422eaaf797b5890b26501d3b538d_62.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_64.png)

在上面的代码中，`order_target_percent(stock, percent)` 是一个重要的交易函数。它的作用是调整某只股票的持仓，使其占总资产的比例达到指定的百分比。例如，`percent=0` 表示清仓该股票，`percent=0.1` 表示使该股票持仓占总资产的10%。

![](img/f9c7422eaaf797b5890b26501d3b538d_66.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_68.png)

## 回测结果与指标分析

![](img/f9c7422eaaf797b5890b26501d3b538d_70.png)

策略编写完成后，我们运行回测来观察其效果。回测结果会生成一系列绩效指标和图表。

首先，我们从整体上观察策略的表现。回测报告主要包含以下关键指标：

![](img/f9c7422eaaf797b5890b26501d3b538d_72.png)

*   **回测年化收益**：我们设计的策略在整个回测期间的年化收益率。
*   **基准年化收益**：同期市场基准指数（如沪深300）的年化收益率。通过对比两者，可以判断策略是否跑赢了市场。
*   **最大回撤**：策略净值从最高点到最低点的最大跌幅，用于衡量策略的风险。
*   **夏普比率**：衡量策略承担单位风险所获得的超额回报。通常为正数表示策略有价值，为负数则说明收益未能补偿风险。

![](img/f9c7422eaaf797b5890b26501d3b538d_74.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_76.png)

如下图所示，虽然策略和基准在所选时间段内可能都出现亏损，但我们的策略目标是控制亏损幅度，使其小于市场基准。

![](img/f9c7422eaaf797b5890b26501d3b538d_78.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_82.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_80.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_82.png)

此外，回测结果还提供了详细的交易记录，可以查看每一天的具体买卖操作、成交价格和数量，便于我们深入分析策略的每一笔交易。

![](img/f9c7422eaaf797b5890b26501d3b538d_84.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_86.png)

![](img/f9c7422eaaf797b5890b26501d3b538d_88.png)

---

![](img/f9c7422eaaf797b5890b26501d3b538d_88.png)

本节课中我们一起学习了量化策略的完整实现流程。我们首先调整了数据结构以获取股票列表，然后在 `handle_bar` 函数中实现了基于动态股票池的买卖逻辑，最后运行回测并解读了包括年化收益、最大回撤和夏普比率在内的关键绩效指标。理解这些步骤和指标是评估和优化量化策略的基础。