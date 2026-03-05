# Python金融量化：P26：策略效果演示与指标分析 📊

![](img/ef7de6060e03ac6222e2baed438142db_1.png)

在本节课中，我们将学习如何对量化策略进行回测，并分析其关键绩效指标。我们将重点讲解如何将筛选出的股票池应用到交易逻辑中，以及如何解读回测报告中的各项数据。

![](img/ef7de6060e03ac6222e2baed438142db_3.png)

上一节我们介绍了如何在交易前筛选股票，本节中我们来看看如何基于筛选结果执行买卖操作，并分析策略的最终表现。

![](img/ef7de6060e03ac6222e2baed438142db_5.png)

![](img/ef7de6060e03ac6222e2baed438142db_7.png)

![](img/ef7de6060e03ac6222e2baed438142db_9.png)

## 数据格式调整与股票池获取

![](img/ef7de6060e03ac6222e2baed438142db_11.png)

在交易前，我们通常希望数据以更直观的竖排形式展示，即每一行代表一只股票及其对应的财务指标。原始数据可能是横排的，需要进行转置操作。

![](img/ef7de6060e03ac6222e2baed438142db_13.png)

![](img/ef7de6060e03ac6222e2baed438142db_15.png)

以下是实现数据转置的代码：

![](img/ef7de6060e03ac6222e2baed438142db_17.png)

![](img/ef7de6060e03ac6222e2baed438142db_19.png)

```python
# 假设 df 是包含股票财务数据的DataFrame
df_transposed = df.T  # 使用 .T 属性进行转置
print(df_transposed)
```

![](img/ef7de6060e03ac6222e2baed438142db_21.png)

![](img/ef7de6060e03ac6222e2baed438142db_23.png)

![](img/ef7de6060e03ac6222e2baed438142db_25.png)

转置后，数据的第一列是股票代码（索引），后续各列是其对应的财务指标值。我们的目标是从中获取股票代码列表，作为当日的交易股票池。

![](img/ef7de6060e03ac6222e2baed438142db_27.png)

![](img/ef7de6060e03ac6222e2baed438142db_29.png)

![](img/ef7de6060e03ac6222e2baed438142db_31.png)

```python
# 获取转置后DataFrame的索引，即股票代码列表
selected_stocks = df_transposed.index.tolist()
# 将股票池存入context中，供后续交易函数使用
context.stock_pool = selected_stocks
```

![](img/ef7de6060e03ac6222e2baed438142db_33.png)

![](img/ef7de6060e03ac6222e2baed438142db_35.png)

![](img/ef7de6060e03ac6222e2baed438142db_37.png)

这样，在 `before_trading` 函数中，我们就完成了股票筛选工作，并将结果存储在 `context` 对象中。

![](img/ef7de6060e03ac6222e2baed438142db_39.png)

![](img/ef7de6060e03ac6222e2baed438142db_41.png)

![](img/ef7de6060e03ac6222e2baed438142db_43.png)

## 交易逻辑实现

![](img/ef7de6060e03ac6222e2baed438142db_45.png)

![](img/ef7de6060e03ac6222e2baed438142db_47.png)

接下来，我们进入 `handle_bar` 函数，实现具体的交易逻辑。核心思路是：每日判断当前持仓与当日股票池的差异，卖出不在池中的股票，买入在池中但未持有的股票。

![](img/ef7de6060e03ac6222e2baed438142db_49.png)

![](img/ef7de6060e03ac6222e2baed438142db_51.png)

![](img/ef7de6060e03ac6222e2baed438142db_53.png)

首先，我们需要了解如何获取当前持仓信息。以下是相关API的使用方法：

![](img/ef7de6060e03ac6222e2baed438142db_55.png)

```python
# 获取当前账户的所有持仓信息，返回一个字典
current_positions = context.portfolio.positions
# 获取当前持仓的所有股票代码列表
held_stocks = list(current_positions.keys())
```

![](img/ef7de6060e03ac6222e2baed438142db_57.png)

以下是完整的交易逻辑步骤：

![](img/ef7de6060e03ac6222e2baed438142db_59.png)

![](img/ef7de6060e03ac6222e2baed438142db_61.png)

1.  **判断持仓状态**：检查当前是否有持仓。
2.  **卖出操作**：遍历当前持仓，如果某只股票不在当日的目标股票池中，则将其全部卖出。
3.  **买入操作**：遍历当日目标股票池，如果某只股票未被持有，则买入一定比例。

![](img/ef7de6060e03ac6222e2baed438142db_63.png)

![](img/ef7de6060e03ac6222e2baed438142db_65.png)

以下是实现该逻辑的代码框架：

![](img/ef7de6060e03ac6222e2baed438142db_67.png)

![](img/ef7de6060e03ac6222e2baed438142db_69.png)

```python
def handle_bar(context):
    # 1. 获取当前持仓和当日股票池
    current_positions = context.portfolio.positions
    held_stocks = list(current_positions.keys())
    target_pool = context.stock_pool

    # 2. 卖出不在目标池中的股票
    for stock in held_stocks:
        if stock not in target_pool:
            # order_target_percent 函数用于调整仓位至目标百分比
            # 股票代码， 目标仓位比例（0代表清仓）
            order_target_percent(stock, 0)

    # 3. 买入在目标池中但未持有的股票
    # 计算每只股票应占的均衡权重
    weight = 1.0 / len(target_pool) if target_pool else 0
    for stock in target_pool:
        if stock not in held_stocks:
            # 买入并调整至目标权重
            order_target_percent(stock, weight)
```

![](img/ef7de6060e03ac6222e2baed438142db_71.png)

在这个示例中，我们采用了简单的等权重配置策略。在实际应用中，你可以根据财务指标、价格动量等计算更复杂的权重。

## 回测结果与指标分析

![](img/ef7de6060e03ac6222e2baed438142db_73.png)

![](img/ef7de6060e03ac6222e2baed438142db_75.png)

策略编写完成后，需要进行回测以评估其历史表现。回测引擎会生成一份包含多项关键指标的报告。

![](img/ef7de6060e03ac6222e2baed438142db_77.png)

以下是一些核心绩效指标及其含义：

![](img/ef7de6060e03ac6222e2baed438142db_79.png)

![](img/ef7de6060e03ac6222e2baed438142db_81.png)

*   **回测年化收益 / 基准年化收益**：你的策略在回测期间的年化收益率，以及与基准指数（如沪深300）年化收益率的对比。正值代表盈利。
*   **阿尔法（Alpha）与贝塔（Beta）**：
    *   **Alpha**：策略相对于市场基准的超额收益，衡量选股能力。
    *   **Beta**：策略收益相对于市场基准收益的波动性，衡量系统风险。
*   **夏普比率（Sharpe Ratio）**：衡量每承受一单位总风险，所获得的超额回报。公式为：
    `Sharpe Ratio = (策略收益率 - 无风险利率) / 策略收益波动率`
    该值越高，说明风险调整后的收益越好。
*   **最大回撤（Max Drawdown）**：在选定周期内，资产净值从最高点到最低点的最大跌幅。这是衡量策略下行风险的重要指标。
*   **收益波动率（Volatility）**：策略收益率的波动程度，即标准差。波动率越大，风险通常越高。

![](img/ef7de6060e03ac6222e2baed438142db_83.png)

![](img/ef7de6060e03ac6222e2baed438142db_85.png)

![](img/ef7de6060e03ac6222e2baed438142db_87.png)

分析报告时，应综合看待这些指标：
1.  比较策略收益与基准收益，看是否能跑赢大盘。
2.  观察夏普比率，判断收益是否足以补偿所承担的风险。
3.  检查最大回撤，评估策略可能带来的最大亏损是否在可接受范围内。
4.  结合交易详情，分析买卖点的合理性。

请注意，历史回测结果并不代表未来表现，策略在实际运行中可能面临滑点、市场环境变化等挑战。

![](img/ef7de6060e03ac6222e2baed438142db_89.png)

本节课中我们一起学习了量化策略从数据准备、交易逻辑实现到回测分析的全过程。我们掌握了如何调整数据格式、利用 `context` 对象传递信息、编写基于股票池调仓的交易逻辑，并学会了解读回测报告中的关键绩效指标，如年化收益、夏普比率和最大回撤。这是构建和评估一个完整量化策略的基础步骤。