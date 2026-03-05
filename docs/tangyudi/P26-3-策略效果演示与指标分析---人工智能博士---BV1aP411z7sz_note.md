# 量化交易策略：P26：3-策略效果演示与指标分析

![](img/124acb2f5a1ced49742ac45eddcb6b39_1.png)

在本节课中，我们将学习如何将筛选出的股票列表进行格式转换，并基于此实现一个完整的交易策略。我们将编写策略的核心交易逻辑，包括买入和卖出操作，并最终运行回测，分析策略的各项关键指标。

![](img/124acb2f5a1ced49742ac45eddcb6b39_3.png)

## 数据格式转换

![](img/124acb2f5a1ced49742ac45eddcb6b39_5.png)

上一节我们介绍了如何筛选出目标股票。本节中我们来看看如何将筛选结果转换为更易处理的格式。

![](img/124acb2f5a1ced49742ac45eddcb6b39_7.png)

我们最初得到的股票数据是横向排列的，即每一列是一个股票，每一行是一个财务指标。这种格式不便于我们按股票进行后续操作。我们通常更喜欢纵向排列，即每一行代表一只股票，每一列代表其各项指标。

因此，我们需要对数据框（DataFrame）进行转置操作。以下是实现转置的代码：

![](img/124acb2f5a1ced49742ac45eddcb6b39_9.png)

```python
# 假设 df 是包含筛选结果的原始 DataFrame
df_transposed = df.T
```

![](img/124acb2f5a1ced49742ac45eddcb6b39_11.png)

![](img/124acb2f5a1ced49742ac45eddcb6b39_13.png)

![](img/124acb2f5a1ced49742ac45eddcb6b39_14.png)

转置后，数据将变为纵向排列。此时，每一行的索引（index）就是股票代码，这正是我们后续步骤中需要获取的信息。

![](img/124acb2f5a1ced49742ac45eddcb6b39_16.png)

## 存储筛选结果

![](img/124acb2f5a1ced49742ac45eddcb6b39_18.png)

![](img/124acb2f5a1ced49742ac45eddcb6b39_20.png)

转置完成后，我们需要将筛选出的股票列表存储起来，以便在交易函数中使用。我们将股票代码列表保存在 `context` 对象中。

![](img/124acb2f5a1ced49742ac45eddcb6b39_22.png)

![](img/124acb2f5a1ced49742ac45eddcb6b39_24.png)

```python
# 获取转置后 DataFrame 的索引，即股票代码列表
selected_stocks = df_transposed.index.tolist()
# 将股票列表存入 context
context.selected_stocks = selected_stocks
```

![](img/124acb2f5a1ced49742ac45eddcb6b39_26.png)

![](img/124acb2f5a1ced49742ac45eddcb6b39_28.png)

至此，我们在交易开始前（`before_trading`）的准备工作已经完成。

![](img/124acb2f5a1ced49742ac45eddcb6b39_30.png)

![](img/124acb2f5a1ced49742ac45eddcb6b39_32.png)

## 实现交易逻辑

![](img/124acb2f5a1ced49742ac45eddcb6b39_34.png)

准备工作完成后，我们进入核心的交易函数（`handle_data`）。本节中，我们将根据持仓情况和当前筛选出的股票池，执行买入和卖出操作。

首先，我们需要判断当前账户的持仓情况。我们可以通过 `context.portfolio.positions` 来获取一个包含所有持仓信息的字典。

以下是交易逻辑的实现步骤：

![](img/124acb2f5a1ced49742ac45eddcb6b39_36.png)

![](img/124acb2f5a1ced49742ac45eddcb6b39_38.png)

1.  **判断持仓状态**：检查当前是否有持仓。
2.  **处理卖出**：如果当前有持仓，则遍历持仓中的每只股票。如果某只股票不在当前筛选出的股票池中，则将其全部卖出。
3.  **处理买入**：遍历当前筛选出的股票池。对于池中的每只股票，执行买入操作。为了简化，我们采用等权重买入策略，即每只股票分配相同的资金比例。

![](img/124acb2f5a1ced49742ac45eddcb6b39_40.png)

以下是具体的代码实现：

![](img/124acb2f5a1ced49742ac45eddcb6b39_42.png)

```python
def handle_data(context):
    # 1. 获取当前持仓
    current_positions = context.portfolio.positions

    # 2. 卖出不在当前股票池中的持仓
    if current_positions:
        for stock in current_positions:
            if stock not in context.selected_stocks:
                # 卖出该股票的全部持仓
                order_target_percent(stock, 0)

    # 3. 等权重买入当前股票池中的股票
    for stock in context.selected_stocks:
        # 计算每只股票应占的资金比例
        weight = 1.0 / len(context.selected_stocks)
        # 买入股票，调整至目标权重
        order_target_percent(stock, weight)
```

## 策略回测与指标分析

代码编写完成后，我们可以运行回测来评估策略的表现。回测结果会提供一系列关键指标，帮助我们理解策略的盈利能力和风险水平。

以下是几个需要关注的核心指标：

![](img/124acb2f5a1ced49742ac45eddcb6b39_44.png)

*   **累计收益率**：策略在整个回测期间的总收益。可以与基准（如沪深300指数）的收益率进行对比。
*   **年化收益率**：将累计收益率折算为每年的平均收益率，便于不同周期策略的比较。
*   **最大回撤**：策略净值从最高点到最低点的最大跌幅，用于衡量策略可能面临的最大亏损风险。
*   **夏普比率**：衡量策略承担单位风险所获得的超额回报。该值为正且越大越好，若为负则说明风险调整后的收益为负。

![](img/124acb2f5a1ced49742ac45eddcb6b39_46.png)

分析回测结果时，应综合看待这些指标。例如，一个策略可能累计收益很高，但最大回撤也很大，说明其波动性高，风险较大。夏普比率则能更综合地反映收益与风险的平衡情况。

## 总结

![](img/124acb2f5a1ced49742ac45eddcb6b39_48.png)

本节课中我们一起学习了量化交易策略从数据处理到执行回测的完整流程。我们首先将横向的股票数据转置为纵向格式以方便处理，并将筛选出的股票列表存储在上下文对象中。接着，我们实现了策略的核心交易逻辑：卖出不再符合筛选条件的持仓，并以等权重方式买入新筛选出的股票。最后，我们运行回测并学习了如何解读累计收益率、年化收益率、最大回撤和夏普比率等关键绩效指标，从而全面评估一个策略的有效性。