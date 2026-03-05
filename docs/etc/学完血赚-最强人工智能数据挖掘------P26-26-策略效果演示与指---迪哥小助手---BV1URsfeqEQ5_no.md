# 人工智能数据挖掘实战：P26：策略效果演示与指标分析 📊

![](img/f27a335fe32a02fa7b36af76cbd9963c_1.png)

在本节课中，我们将学习如何对量化交易策略进行回测，并分析其效果指标。我们将重点讲解如何调整数据格式、实现交易逻辑，并解读回测报告中的关键信息。

![](img/f27a335fe32a02fa7b36af76cbd9963c_3.png)

上一节我们介绍了如何筛选股票，本节中我们来看看如何根据筛选结果执行交易，并分析策略的最终表现。

![](img/f27a335fe32a02fa7b36af76cbd9963c_5.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_6.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_7.png)

## 数据格式调整与处理 🔄

![](img/f27a335fe32a02fa7b36af76cbd9963c_8.png)

我们的想法是，通常我们喜欢将数据纵向排列，即每个股票占一行。但当前数据是横向排列的，每个股票占一列。因此，我们需要对数据进行转置操作。

![](img/f27a335fe32a02fa7b36af76cbd9963c_10.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_11.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_13.png)

以下是实现数据转置的代码：

![](img/f27a335fe32a02fa7b36af76cbd9963c_15.png)

```python
# 假设 df 是包含股票数据的 DataFrame
df_transposed = df.T
print(df_transposed)
```

![](img/f27a335fe32a02fa7b36af76cbd9963c_17.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_18.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_19.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_21.png)

转置后，数据变为纵向排列，每一行代表一只股票及其对应的指标值。此时，我们只需要获取股票的索引（即股票代码）即可。

![](img/f27a335fe32a02fa7b36af76cbd9963c_23.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_25.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_26.png)

## 交易逻辑实现 💹

![](img/f27a335fe32a02fa7b36af76cbd9963c_28.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_29.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_30.png)

在 `handle_bar` 函数中，我们需要根据每日筛选出的股票池执行买卖操作。首先，我们需要判断当前账户的持仓情况。

![](img/f27a335fe32a02fa7b36af76cbd9963c_32.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_34.png)

以下是判断当前持仓的代码：

![](img/f27a335fe32a02fa7b36af76cbd9963c_35.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_36.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_37.png)

```python
# 获取当前账户的所有持仓信息
positions = context.portfolio.positions
```

![](img/f27a335fe32a02fa7b36af76cbd9963c_39.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_40.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_41.png)

接下来，我们根据持仓情况执行交易逻辑。如果当前没有持仓，则直接买入筛选出的股票。如果已有持仓，则需要比较当前持仓与最新股票池的差异。

以下是执行买卖操作的逻辑：

![](img/f27a335fe32a02fa7b36af76cbd9963c_43.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_44.png)

1.  **卖出不在股票池中的股票**：遍历当前持仓，如果某只股票不在最新的筛选股票池中，则将其卖出。
2.  **买入股票池中的股票**：遍历最新的筛选股票池，如果某只股票不在当前持仓中，则买入。

![](img/f27a335fe32a02fa7b36af76cbd9963c_46.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_47.png)

以下是实现买卖操作的代码：

![](img/f27a335fe32a02fa7b36af76cbd9963c_48.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_50.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_51.png)

```python
# 卖出不在股票池中的股票
for stock in positions.keys():
    if stock not in stock_pool:
        order_target_percent(stock, 0)  # 卖出全部持仓

![](img/f27a335fe32a02fa7b36af76cbd9963c_53.png)

# 买入股票池中的股票
for stock in stock_pool:
    if stock not in positions:
        # 平均分配资金买入
        order_target_percent(stock, 1.0 / len(stock_pool))
```

## 策略回测与指标分析 📈

完成策略编写后，我们进行回测并分析结果。回测报告会提供一系列关键指标，帮助我们评估策略的优劣。

![](img/f27a335fe32a02fa7b36af76cbd9963c_55.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_56.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_57.png)

以下是回测报告中需要关注的核心指标：

![](img/f27a335fe32a02fa7b36af76cbd9963c_59.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_60.png)

*   **年化收益率**：策略在一年内的平均收益率。公式为：
    `年化收益率 = (1 + 总收益率)^(252 / 交易天数) - 1`
*   **基准年化收益率**：同期市场指数（如沪深300）的年化收益率，用于对比。
*   **最大回撤**：策略净值从最高点到最低点的最大跌幅，衡量策略的风险。
*   **夏普比率**：衡量每承受一单位风险，能获得多少超额回报。公式为：
    `夏普比率 = (策略年化收益率 - 无风险利率) / 策略年化波动率`
*   **交易详情**：包含每次买卖的时间、价格和数量，用于分析交易行为。

![](img/f27a335fe32a02fa7b36af76cbd9963c_62.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_64.png)

![](img/f27a335fe32a02fa7b36af76cbd9963c_65.png)

在分析时，我们主要对比策略收益与基准收益，观察策略是否能跑赢市场或减少亏损。同时，关注最大回撤和夏普比率，评估策略的风险收益比。

## 总结 🎯

![](img/f27a335fe32a02fa7b36af76cbd9963c_67.png)

本节课中我们一起学习了量化策略的完整流程。我们从调整数据格式开始，实现了基于股票池的动态买卖逻辑，最后通过回测报告分析了策略的各项关键指标。理解这些指标的含义对于评估和优化策略至关重要。虽然示例策略在特定时间段内表现不佳，但这正是通过回测发现问题、迭代改进策略价值所在。