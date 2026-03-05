# 金融量化实战：P25：3-策略效果演示与指标分析 📊

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_1.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_3.png)

在本节课中，我们将学习如何对已构建的量化交易策略进行效果演示与关键指标分析。我们将调整数据处理格式，完善交易逻辑，并最终运行回测以解读各项绩效指标。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_5.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_6.png)

---

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_8.png)

上一节我们介绍了策略的初始化与数据筛选逻辑。本节中，我们来看看如何对筛选结果进行格式转换，并实现完整的交易下单逻辑。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_9.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_11.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_13.png)

我们最初得到的股票筛选结果（`DataFrame`）是横向排列的，即每一列代表一只股票。然而，在后续处理中，我们更习惯纵向查看数据，即每一行代表一只股票及其对应的财务指标。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_15.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_17.png)

以下是数据格式转换的步骤：
1.  对原始的 `DataFrame` 使用 `.T` 属性进行转置操作。
2.  转置后，索引（`index`）变为股票名称，我们将其提取出来，作为最终要交易的股票池。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_19.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_20.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_21.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_23.png)

核心操作代码如下：
```python
# 假设 df 是原始的横向DataFrame
df_transposed = df.T  # 进行转置
stock_list = df_transposed.index.tolist()  # 获取股票名称列表作为股票池
```
完成转置后，我们便得到了一个按行排列的股票列表，方便后续处理。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_25.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_26.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_27.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_28.png)

---

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_30.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_31.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_32.png)

在 `handle_bar` 函数中，我们需要实现每日的交易逻辑。核心思路是：对比当前持仓与最新股票池，卖出池中不再包含的股票，并买入池中新增的股票。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_34.png)

首先，我们需要获取当前的持仓信息。通过 `context.portfolio.positions` 可以获取一个包含所有持仓的字典。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_35.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_37.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_38.png)

以下是 `handle_bar` 函数中的交易逻辑步骤：
1.  **判断持仓状态**：检查 `context.portfolio.positions` 是否为空。若为空，则为首个交易日，需执行买入操作。
2.  **卖出不在股票池的持仓**：若非首个交易日，则遍历当前持仓。若某只股票不在最新的 `stock_list` 中，则将其全部卖出。卖出使用 `order_target_percent(stock, 0)` 函数，其含义是将该股票的仓位调整至0%，即清仓。
3.  **买入股票池中的股票**：遍历最新的 `stock_list`。对于池中的每只股票，我们执行买入操作，使用 `order_target_percent(stock, weight)`。这里我们采用等权配置策略，即每只股票占用的资金比例相同，计算公式为 `weight = 1.0 / len(stock_list)`。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_39.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_41.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_42.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_43.png)

关键交易API调用示例：
```python
# 卖出股票
order_target_percent(sell_stock, 0)
# 买入股票，等权重分配
buy_weight = 1.0 / len(context.stock_pool)
order_target_percent(buy_stock, buy_weight)
```

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_45.png)

---

完成策略代码后，我们运行回测。系统会生成一份详细的回测报告，其中包含多个关键绩效指标。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_47.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_48.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_49.png)

以下是对主要回测结果指标的解读：
*   **策略收益 vs 基准收益**：图中蓝色曲线代表策略净值走势，绿色曲线代表基准（如沪深300指数）走势。对比两者可以直观看出策略是否跑赢市场。
*   **年化收益率**：策略在回测期间的年化收益水平。正值代表盈利。
*   **最大回撤**：策略净值从最高点到最低点的最大跌幅，是衡量策略风险的重要指标。该值越小，说明策略在极端市场下的抗风险能力越强。
*   **夏普比率**：衡量策略承担单位风险所获得的超额回报。该值为正且越大，说明策略的风险调整后收益越好。若收益为负，则夏普比率通常也为负。
*   **交易详情**：点击报告中的“交易详情”可以查看每一笔具体的买卖记录，包括时间、股票、价格和数量，用于复核策略逻辑是否正确执行。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_51.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_52.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_54.png)

在本案例的回测结果中，策略收益与基准收益均为负值，这可能是由于所选回测时间段处于市场下行周期。但通过指标对比可以发现，我们的策略最大回撤相对较小，在一定程度上控制了下跌风险，体现了策略的防御性。

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_56.png)

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_57.png)

---

![](img/c28fbb372b2b5c24f8c2e79b480e74d5_59.png)

本节课中我们一起学习了量化策略的收尾工作：首先将数据格式转换为更易处理的形式，然后实现了完整的自动化交易逻辑，包括持仓判断、卖出与买入操作。最后，我们运行回测并学习了如何解读年化收益率、最大回撤、夏普比率等核心绩效指标，从而能够客观评估策略的有效性与风险水平。