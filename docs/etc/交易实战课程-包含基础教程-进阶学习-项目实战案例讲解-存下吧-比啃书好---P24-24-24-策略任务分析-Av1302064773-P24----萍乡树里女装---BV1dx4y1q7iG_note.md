# 量化交易实战：P24：策略任务分析 🎯

![](img/98fa31d8b3241f4958a1bba1ed2bfaa7_1.png)

在本节课中，我们将学习如何在一个交易平台上构建一个简单的量化交易策略。我们将通过一个具体的任务——从沪深300指数中动态选择并持有表现最佳的10只股票——来熟悉平台的基本API和策略编写流程。

![](img/98fa31d8b3241f4958a1bba1ed2bfaa7_2.png)

## 策略概述与目标 🎯

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何将想法转化为代码。我们的核心目标是：构建一个策略，使其始终持有沪深300指数中根据特定财务指标（例如盈利能力）排名前10的股票。这意味着持仓股票会随着指标数据的更新而动态调整。

## 策略实现框架 🏗️

在开始编写代码之前，我们需要理解策略的运行框架。平台通常将策略逻辑分为几个关键的函数模块，我们需要在这些模块中填充具体的业务逻辑。

以下是策略运行的三个核心阶段：

1.  **初始化 (`initialize`)**: 策略启动时执行一次，用于设置股票池、全局变量等。
2.  **盘前处理 (`before_trading`)**: 每个交易日开始前执行，用于获取最新数据并进行计算（如股票排序）。
3.  **盘中处理 (`handle_bar`)**: 在每个交易时间点（如每分钟或每天）执行，根据盘前计算的结果进行实际的买卖交易决策。

## 分步任务拆解 🔧

基于上述框架，我们可以将“持有Top 10股票”这个目标拆解为具体的编程任务。

以下是实现该策略需要完成的步骤：

*   **第一步：初始化股票池**。在 `initialize` 函数中，获取沪深300指数的全部成分股列表。
*   **第二步：每日筛选股票**。在 `before_trading` 函数中，获取所有成分股的最新财务数据（如盈利），根据该数据排序，并选出排名前10的股票代码。
*   **第三步：执行调仓交易**。在 `handle_bar` 函数中，对比当前持仓的股票列表与今日计算出的“Top 10”列表。卖出不再位于“Top 10”列表中的股票，并买入新进入列表的股票，确保最终持仓与目标一致。

## 核心逻辑与代码示意 💻

让我们聚焦于最核心的排序和调仓逻辑，并用简化的代码来描述。

**股票排序逻辑**：在 `before_trading` 中，我们需要获取数据并排序。
```python
# 伪代码示意
def before_trading(context):
    # 1. 获取沪深300所有股票的昨日盈利数据
    stock_list = context.hs300_constituents
    profit_data = get_fundamental_data(stock_list, indicator='net_profit')

    # 2. 按盈利从高到低排序
    sorted_stocks = sorted(profit_data.items(), key=lambda x: x[1], reverse=True)

    # 3. 选取前10名，并将其股票代码列表存入全局变量
    context.top_10_stocks = [stock for stock, _ in sorted_stocks[:10]]
```

**调仓交易逻辑**：在 `handle_bar` 中，执行买卖操作以达到目标持仓。
```python
# 伪代码示意
def handle_bar(context, bar_dict):
    target_stocks = context.top_10_stocks  # 今日目标持仓
    current_stocks = context.portfolio.positions.keys()  # 当前实际持仓

    # 计算需要卖出的股票（在当前持仓中但不在目标中）
    stocks_to_sell = list(set(current_stocks) - set(target_stocks))
    for stock in stocks_to_sell:
        order_target_value(stock, 0)  # 卖出全部

    # 计算需要买入的股票（在目标中但不在当前持仓中）
    stocks_to_buy = list(set(target_stocks) - set(current_stocks))
    if stocks_to_buy:
        cash_per_stock = context.portfolio.cash / len(stocks_to_buy)
        for stock in stocks_to_buy:
            order_value(stock, cash_per_stock)  # 等金额买入
```

## 总结 📝

![](img/98fa31d8b3241f4958a1bba1ed2bfaa7_4.png)

![](img/98fa31d8b3241f4958a1bba1ed2bfaa7_5.png)

本节课中我们一起学习了如何为一个具体的量化交易任务——动态持有沪深300中财务指标最佳的10只股票——进行策略分析和框架搭建。我们明确了策略需要在`initialize`、`before_trading`和`handle_bar`三个函数模块中分别完成初始化、数据计算和交易执行的任务。通过将复杂目标拆解为“获取数据->排序筛选->对比调仓”的清晰步骤，并辅以核心逻辑的代码示意，我们为下一步实际编写和回测策略打下了坚实的基础。记住，一个好的策略始于清晰的任务分析和严谨的逻辑拆解。