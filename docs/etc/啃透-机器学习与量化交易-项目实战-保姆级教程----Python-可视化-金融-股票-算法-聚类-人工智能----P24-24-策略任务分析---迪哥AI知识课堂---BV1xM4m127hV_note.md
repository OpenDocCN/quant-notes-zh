# 机器学习与量化交易：P24：策略任务分析 📊

![](img/fa4125a0d15c823c4d90db33649b3665_1.png)

在本节课中，我们将学习如何在一个量化交易平台上构建一个简单的策略。我们将通过一个具体任务——从沪深300指数中动态选择并持有表现最佳的10只股票——来熟悉平台的核心API和策略开发流程。整个过程将分为初始化、盘前数据准备和盘中交易执行三个主要步骤。

![](img/fa4125a0d15c823c4d90db33649b3665_3.png)

## 策略目标概述 🎯

我们的目标是构建一个策略，该策略始终持有沪深300指数中根据特定财务指标（例如盈利数据）排名前10的股票。随着市场数据每日更新，我们持有的股票组合也会相应调整，卖出不再属于“前十”的股票，并买入新进入“前十”的股票。

## 策略开发步骤 🛠️

上一节我们概述了策略目标，本节中我们来看看具体的实现步骤。整个策略代码将写在平台提供的可编译界面中，主要包含三个模块：`initialize`（初始化）、`before_trading`（盘前处理）和`handle_bar`（盘中交易）。

### 第一步：初始化模块 (`initialize`)

在初始化函数中，我们需要完成策略的初始设置，主要是确定我们的股票池。

以下是需要在`initialize`函数中完成的核心任务：
*   获取沪深300指数的全部成分股作为我们的初始股票池。
*   进行其他必要的全局变量初始化。

对应的核心代码框架如下：
```python
def initialize(context):
    # 获取沪深300指数成分股，假设使用平台API函数get_index_stocks
    context.stock_pool = get_index_stocks('000300.SH')
    # 可以在此初始化其他全局变量，例如 context.best_stocks = []
```

### 第二步：盘前处理模块 (`before_trading`)

在每天交易开始前，`before_trading`函数会被调用。这里应执行所有与实际交易下单无关的数据查询和处理工作。

以下是需要在`before_trading`函数中完成的核心任务：
*   从股票池（沪深300成分股）中查询所需的财务数据（例如，每股盈利）。
*   根据该财务指标对股票进行排序。
*   选出排名前10的股票代码，并保存下来供交易逻辑使用。

对应的核心代码逻辑如下：
```python
def before_trading(context):
    # 1. 查询财务数据，例如获取所有股票的盈利数据 (假设API为get_fundamentals)
    fundamentals = get_fundamentals(query(valuation.code, valuation.eps), context.stock_pool)
    # 2. 按盈利数据降序排序
    sorted_stocks = fundamentals.sort_values(by='eps', ascending=False)
    # 3. 选取前10只股票的代码，并存入context
    context.best_stocks = sorted_stocks['code'].iloc[:10].tolist()
```

### 第三步：盘中交易模块 (`handle_bar`)

`handle_bar`函数在交易时间内会被定期调用（例如每分钟或每天），这里是执行实际交易逻辑的地方。

以下是需要在`handle_bar`函数中完成的核心任务：
*   检查当前账户持有的所有股票。
*   将当前持仓与当日计算出的最佳股票列表(`context.best_stocks`)进行比较。
*   卖出那些已不在最佳列表中的持仓股票。
*   用剩余资金买入最佳列表中尚未持有的股票。

对应的核心代码逻辑如下：
```python
def handle_bar(context, bar_dict):
    current_holdings = list(context.portfolio.positions.keys())
    best_stocks_today = context.best_stocks

    # 卖出逻辑：卖出当前持有但不在今日最佳列表中的股票
    for stock in current_holdings:
        if stock not in best_stocks_today:
            order_target_value(stock, 0)  # 卖出该股票全部持仓

    # 买入逻辑：等权重买入今日最佳列表中的股票
    cash_per_stock = context.portfolio.cash / len(best_stocks_today)
    for stock in best_stocks_today:
        order_target_value(stock, cash_per_stock)  # 买入股票至目标市值
```

## 总结 📝

本节课中我们一起学习了如何为一个简单的量化交易策略搭建框架。我们明确了策略目标：动态持有沪深300中财务指标最佳的前10只股票。随后，我们将实现拆解为三个部分：
1.  在`initialize`中初始化股票池。
2.  在`before_trading`中完成每日的数据获取、排序与选股。
3.  在`handle_bar`中执行具体的调仓交易逻辑，通过比较当前持仓与目标持仓，实现“汰弱留强”。

![](img/fa4125a0d15c823c4d90db33649b3665_5.png)

![](img/fa4125a0d15c823c4d90db33649b3665_7.png)

这个策略虽然逻辑清晰，但仅为教学示例。在实际应用中，需要考虑更多因素，如交易成本、滑点、更复杂的选股模型以及风险控制等。