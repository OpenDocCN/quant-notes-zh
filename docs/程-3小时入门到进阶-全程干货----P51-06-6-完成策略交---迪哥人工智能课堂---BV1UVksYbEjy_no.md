# Python金融量化交易实战：P51：06-6-完成策略交易展示结果 🚀

![](img/2d9c521512b5d1c3527b63682b321f32_1.png)

在本节课中，我们将继续完成REBON函数，核心任务是实现一个调仓逻辑。我们将编写函数来对比当前持仓股票与最新排名股票之间的差异，并基于此差异执行买入和卖出操作，从而完成策略的交易部分。

![](img/2d9c521512b5d1c3527b63682b321f32_3.png)

![](img/2d9c521512b5d1c3527b63682b321f32_5.png)

上一节我们介绍了如何筛选和排名股票，本节中我们来看看如何根据排名结果进行实际的交易操作。

## 获取当前持仓股票

首先，我们需要一个函数来获取当前投资组合中持有的所有股票。以下是实现此功能的步骤：

![](img/2d9c521512b5d1c3527b63682b321f32_7.png)

以下是获取持仓股票列表的步骤：

1.  从 `context` 对象中获取持仓信息 `positions`。
2.  遍历 `positions` 列表中的每一个持仓对象。
3.  检查每个持仓对象的数量 `quantity` 是否大于零。
4.  将所有数量大于零的持仓股票代码添加到一个列表中。
5.  返回这个持仓股票代码列表。

![](img/2d9c521512b5d1c3527b63682b321f32_9.png)

对应的代码实现如下：

![](img/2d9c521512b5d1c3527b63682b321f32_11.png)

```python
def get_holdings(context):
    holdings = []
    positions = context.portfolio.positions
    for position in positions:
        if positions[position].quantity > 0:
            holdings.append(position)
    return holdings
```

## 计算交易清单并执行调仓

获取了最新股票排名和当前持仓后，下一步是计算需要买入和卖出的股票清单，并执行交易。

以下是计算交易清单并执行调仓的核心逻辑：

![](img/2d9c521512b5d1c3527b63682b321f32_13.png)

1.  调用 `get_holdings` 函数获取当前持仓列表 `holdings`。
2.  计算需要买入的股票：`to_buy = set(stocks) - set(holdings)`。即，在新排名中但不在当前持仓中的股票。
3.  计算需要卖出的股票：`to_sell = set(holdings) - set(stocks)`。即，在当前持仓中但不在新排名中的股票。
4.  执行卖出操作：遍历 `to_sell` 列表，对每只股票下达目标持仓比例为0的指令，即清仓。
5.  执行买入操作：
    *   首先检查 `to_buy` 列表是否为空，若为空则直接返回。
    *   计算可用总资金 `total_value`。
    *   计算每只待买入股票应分配的平均资金 `average_value = total_value / len(to_buy)`。
    *   遍历 `to_buy` 列表，对每只股票下达目标价值为 `average_value` 的买入指令。

![](img/2d9c521512b5d1c3527b63682b321f32_15.png)

![](img/2d9c521512b5d1c3527b63682b321f32_17.png)

![](img/2d9c521512b5d1c3527b63682b321f32_19.png)

对应的代码实现如下：

![](img/2d9c521512b5d1c3527b63682b321f32_21.png)

![](img/2d9c521512b5d1c3527b63682b321f32_23.png)

```python
def rebalance(context):
    # 获取最新股票排名
    stocks = get_ranked_stocks(context)
    # 获取当前持仓
    holdings = get_holdings(context)

    # 计算交易清单
    to_buy = set(stocks) - set(holdings)
    to_sell = set(holdings) - set(stocks)

    # 执行卖出操作
    for stock in to_sell:
        order_target_percent(stock, 0)

    # 执行买入操作
    if len(to_buy) == 0:
        return
    total_value = context.portfolio.portfolio_value
    average_value = total_value / len(to_buy)
    for stock in to_buy:
        order_target_value(stock, average_value)
```

![](img/2d9c521512b5d1c3527b63682b321f32_25.png)

![](img/2d9c521512b5d1c3527b63682b321f32_27.png)

## 策略流程回顾与调试

![](img/2d9c521512b5d1c3527b63682b321f32_29.png)

现在，我们已经完成了策略的核心交易函数。让我们回顾一下整个策略的完整流程。

![](img/2d9c521512b5d1c3527b63682b321f32_31.png)

![](img/2d9c521512b5d1c3527b63682b321f32_33.png)

![](img/2d9c521512b5d1c3527b63682b321f32_35.png)

以下是策略从初始化到定期调仓的完整步骤：

![](img/2d9c521512b5d1c3527b63682b321f32_37.png)

![](img/2d9c521512b5d1c3527b63682b321f32_39.png)

1.  **初始化 (`initialize`)**：设置股票池、基准、交易费用等参数，并安排每月调仓函数。
2.  **数据获取与评分 (`get_ranked_stocks`)**：每月调仓时，获取股票的基本面数据（如市盈率PE、市净率PB、每股收益EPS），根据自定义规则进行筛选和评分排名。
3.  **持仓比对 (`get_holdings`)**：获取当前的股票持仓列表。
4.  **交易执行 (`rebalance`)**：对比新排名与当前持仓，计算出买卖清单，并执行交易。

![](img/2d9c521512b5d1c3527b63682b321f32_41.png)

在编写和运行代码时，可能会遇到错误。常见的错误包括语法错误（如中文标点）、类型错误（如对列表直接进行集合运算）或API使用错误。遇到报错时，应仔细阅读错误信息，逐行检查代码，特别是标点符号、变量名和函数调用是否正确。例如，将列表转换为集合（`set()`）后再进行差集运算，可以避免类型错误。

![](img/2d9c521512b5d1c3527b63682b321f32_43.png)

![](img/2d9c521512b5d1c3527b63682b321f32_45.png)

本节课中我们一起学习了如何实现量化交易策略的调仓逻辑。我们编写了获取持仓、计算买卖清单以及执行交易订单的函数，并将它们整合到每月调仓的流程中。通过这个过程，我们完成了一个完整的、可自动执行“买入排名靠前股票、卖出掉出排名股票”策略的交易系统核心部分。