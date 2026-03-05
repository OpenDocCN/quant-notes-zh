# Python金融分析与量化交易实战教程：P21：01-1-策略任务分析 📈

![](img/abf18ac9ebb1f6c7a798376e01d34e3e_1.png)

在本节课中，我们将学习如何在一个交易平台上构建一个简单的量化交易策略，并熟悉相关的API接口。我们将通过一个具体的任务——从沪深300指数成分股中动态选择并持有表现最好的10只股票——来演示策略开发的完整流程。

![](img/abf18ac9ebb1f6c7a798376e01d34e3e_3.png)

## 策略目标概述

我们的核心目标是构建一个策略，该策略始终持有沪深300指数中表现最好的10只股票。这里的“表现最好”可以基于一个或多个财务指标（例如盈利数据）来衡量。策略需要每天或定期更新持仓，卖出不再属于“最好”行列的股票，并买入新进入“最好”行列的股票。

## 策略开发流程

上一节我们概述了策略目标，本节中我们来看看实现这个策略的具体步骤。整个策略的开发将围绕交易平台提供的几个核心函数模块展开。

以下是策略实现的三个主要步骤：

1.  **初始化 (`initialize`)**: 在此阶段，我们需要设定策略的股票池，例如获取沪深300指数的所有成分股。
2.  **盘前处理 (`before_trading`)**: 在每个交易日开始前，我们需要获取股票池中所有股票的财务数据，根据选定的指标进行排序，并筛选出排名前10的股票。
3.  **盘中处理 (`handle_bar`)**: 在每个交易时段（例如每分钟或每天），我们需要执行实际的交易逻辑：检查当前持仓，与盘前计算出的“最佳10股”名单对比，卖出不在名单中的股票，并买入名单中尚未持有的股票。

## 各模块功能详解

### 初始化模块 (`initialize`)

在初始化函数中，我们需要完成策略的基础设置。这通常包括设置交易佣金、滑点等参数，但对我们当前的任务而言，最关键的一步是定义我们的股票池。

我们将在此获取沪深300指数的所有成分股代码，作为后续分析和交易的备选范围。

```python
def initialize(context):
    # 获取沪深300指数成分股，并设置为策略的股票池
    set_universe('000300.XSHG')
```

### 盘前处理模块 (`before_trading`)

`before_trading` 函数在每个交易日开始前自动调用。所有与当日交易决策相关、但不涉及实际下单的数据准备和计算工作都应放在这里。

对于我们的策略，需要在此完成以下工作：
*   从股票池中获取所有股票的财务数据（例如，净利润）。
*   根据选定的财务指标对所有股票进行排序。
*   选出排名前10的股票，并将它们的代码保存下来，供 `handle_bar` 函数使用。

以下是该模块的核心逻辑框架：

```python
def before_trading(context):
    # 1. 获取股票池
    stock_list = context.universe
    # 2. 查询财务数据（例如：净利润）
    fundamental_data = get_fundamentals(query(valuation.net_profit), stock_list)
    # 3. 按净利润降序排序
    sorted_stocks = fundamental_data.sort_values(by='net_profit', ascending=False)
    # 4. 选取前10只股票
    context.top_10_stocks = sorted_stocks.head(10).index.tolist()
```

### 盘中处理模块 (`handle_bar`)

`handle_bar` 函数是策略的核心，它在每个交易时段被调用，负责执行具体的交易指令。在这里，我们需要将盘前计算出的理想持仓与当前实际持仓进行比对，并做出调仓决策。

其核心逻辑如下：
*   **获取当前持仓**：查看账户当前持有哪些股票及其数量。
*   **调仓逻辑**：
    *   对于当前持有但不在 `context.top_10_stocks` 名单中的股票，执行卖出操作。
    *   对于在 `context.top_10_stocks` 名单中但当前未持有的股票，使用可用资金平均买入。

```python
def handle_bar(context, bar_dict):
    # 获取当前持仓的股票列表
    current_positions = list(context.portfolio.positions.keys())

    # 卖出逻辑：卖出不在今日“最佳10股”名单中的股票
    for stock in current_positions:
        if stock not in context.top_10_stocks:
            order_target_value(stock, 0)  # 卖出该股票全部持仓

    # 买入逻辑：买入在名单中但尚未持有的股票
    # 计算可用于买入新股票的资金（总资产减去已有持仓的价值）
    available_cash = context.portfolio.total_value - context.portfolio.positions_value
    stocks_to_buy = [s for s in context.top_10_stocks if s not in current_positions]

    if stocks_to_buy and available_cash > 0:
        cash_per_stock = available_cash / len(stocks_to_buy)
        for stock in stocks_to_buy:
            order_value(stock, cash_per_stock)  # 买入价值约为 cash_per_stock 的股票
```

## 总结

本节课中我们一起学习了如何为一个简单的量化交易策略进行任务分析和流程设计。我们以“动态持有沪深300中财务表现最佳的10只股票”为例，将策略分解为三个清晰的步骤：

1.  **初始化 (`initialize`)**：设定基础参数与股票池。
2.  **盘前处理 (`before_trading`)**：进行数据获取、指标计算和股票筛选，确定当日的目标持仓组合。
3.  **盘中处理 (`handle_bar`)**：执行交易逻辑，通过比对目标持仓与实际持仓，完成卖出和买入操作，使持仓向目标组合靠拢。

![](img/abf18ac9ebb1f6c7a798376e01d34e3e_5.png)

![](img/abf18ac9ebb1f6c7a798376e01d34e3e_7.png)

这个流程是构建大多数量化策略的通用框架。通过这个实例，我们熟悉了策略开发的基本结构以及各个模块的分工。在接下来的课程中，我们将基于这个框架，深入代码细节，实现并回测这个策略。