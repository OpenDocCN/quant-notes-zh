# Python金融分析与量化交易实战课程：P22：策略效果演示与指标分析

![](img/7ea75d2c1351b3affa82dde9b34581d9_1.png)

在本节课中，我们将学习如何对量化策略进行回测，并分析其效果。我们将重点讲解如何将筛选出的股票数据转换为更易读的格式，并在每日交易中执行买卖逻辑。最后，我们将解读回测报告中的关键指标，以评估策略的优劣。

![](img/7ea75d2c1351b3affa82dde9b34581d9_3.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_5.png)

## 数据格式转换

![](img/7ea75d2c1351b3affa82dde9b34581d9_7.png)

上一节我们介绍了如何筛选股票，本节中我们来看看如何将筛选结果转换为更直观的格式。原始的DataFrame是横向排列的，我们希望将其转换为纵向排列，以便于查看每个股票及其对应的财务指标。

![](img/7ea75d2c1351b3affa82dde9b34581d9_9.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_11.png)

以下是实现数据转置的步骤：

![](img/7ea75d2c1351b3affa82dde9b34581d9_13.png)

1.  对包含筛选结果的DataFrame执行转置操作。
2.  转置后，每一行代表一只股票，第一列是股票代码（索引），后续列是其财务指标。
3.  我们的目标是获取股票代码列表，并将其存储在策略的上下文（context）中，供后续交易使用。

![](img/7ea75d2c1351b3affa82dde9b34581d9_15.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_17.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_18.png)

核心操作是使用Pandas的`.T`属性进行转置：

![](img/7ea75d2c1351b3affa82dde9b34581d9_20.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_22.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_24.png)

```python
# 假设 df_selected 是筛选后的DataFrame
df_transposed = df_selected.T
```

![](img/7ea75d2c1351b3affa82dde9b34581d9_26.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_28.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_30.png)

转置后，我们可以通过索引获取股票列表：

![](img/7ea75d2c1351b3affa82dde9b34581d9_32.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_34.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_36.png)

```python
selected_stocks = df_transposed.index.tolist()
context.stock_pool = selected_stocks  # 将股票池存入上下文
```

![](img/7ea75d2c1351b3affa82dde9b34581d9_38.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_40.png)

## 每日交易逻辑实现

![](img/7ea75d2c1351b3affa82dde9b34581d9_42.png)

在完成数据准备后，我们需要在`handle_bar`函数中实现每日的交易逻辑。核心思路是：比较当前持仓与最新股票池的差异，卖出不在新股票池中的股票，买入新股票池中尚未持有的股票。

![](img/7ea75d2c1351b3affa82dde9b34581d9_44.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_46.png)

以下是实现每日调仓的逻辑步骤：

![](img/7ea75d2c1351b3affa82dde9b34581d9_48.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_50.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_52.png)

1.  **获取当前持仓**：通过`context.portfolio.positions`获取当前账户持有的所有股票及其仓位信息。这是一个字典，键（keys）为股票代码。
2.  **判断并执行卖出操作**：遍历当前持仓。如果某只股票不在最新的股票池（`context.stock_pool`）中，则将其全部卖出。
3.  **执行买入操作**：遍历最新的股票池。对池中的每只股票，执行买入操作，使该股票在总资产中占据一个平均的权重。

![](img/7ea75d2c1351b3affa82dde9b34581d9_54.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_56.png)

关键的API是`order_target_percent`函数，用于将目标股票的仓位调整至指定的资产百分比。

![](img/7ea75d2c1351b3affa82dde9b34581d9_58.png)

*   **卖出操作**：将仓位百分比目标设为0，即清仓。
    ```python
    order_target_percent(stock, 0)
    ```
*   **买入操作**：将仓位百分比目标设为 `1 / len(context.stock_pool)`，即平均分配资金。
    ```python
    order_target_percent(stock, 1.0 / len(context.stock_pool))
    ```

![](img/7ea75d2c1351b3affa82dde9b34581d9_60.png)

## 回测结果与指标分析

![](img/7ea75d2c1351b3affa82dde9b34581d9_62.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_64.png)

策略编写完成后，我们进行回测并分析结果。回测报告提供了多个关键指标来评估策略表现。

![](img/7ea75d2c1351b3affa82dde9b34581d9_66.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_68.png)

以下是回测报告中需要关注的核心指标及其含义：

![](img/7ea75d2c1351b3affa82dde9b34581d9_70.png)

![](img/7ea75d2c1351b3affa82dde9b34581d9_72.png)

1.  **累计收益率**：策略在整个回测期间的总收益。图中通常包含“策略收益曲线”和“基准（如沪深300指数）收益曲线”的对比。
2.  **年化收益率**：将累计收益率折算为每年的平均收益率，便于不同周期策略的比较。
3.  **最大回撤**：策略净值从最高点到最低点的最大跌幅，用于衡量策略可能面临的最大风险。
4.  **夏普比率**：衡量每承受一单位风险，所能获得的超额回报。公式为 `(策略年化收益率 - 无风险利率) / 策略收益波动率`。该值为正且越大越好，若为负则说明风险调整后的收益为负。

![](img/7ea75d2c1351b3affa82dde9b34581d9_74.png)

分析时，应对比策略指标与基准指数指标。即使策略在绝对收益上为负，但如果能跑赢基准（亏损更少或收益更高），并控制好最大回撤，也说明策略具备一定的有效性。具体的买卖记录和每日持仓变化可以在回测详情中进一步查看，用于优化交易逻辑。

![](img/7ea75d2c1351b3affa82dde9b34581d9_76.png)

本节课中我们一起学习了量化策略回测的完整流程：从数据格式处理、每日交易逻辑的代码实现，到最终回测结果的关键指标解读。通过对比策略收益与基准收益，并分析最大回撤、夏普比率等风险指标，我们可以对策略的实战效果有一个初步的评估，为后续的策略优化打下基础。