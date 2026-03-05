# Python金融量化交易实战课程：P49：完成策略交易展示结果 📈

![](img/92d3bad70e37c673254c7710c15b8d37_1.png)

![](img/92d3bad70e37c673254c7710c15b8d37_3.png)

在本节课中，我们将完成一个量化交易策略的核心部分——调仓函数。我们将学习如何根据新的股票排名，计算需要买入和卖出的股票，并执行相应的交易操作。

![](img/92d3bad70e37c673254c7710c15b8d37_5.png)

![](img/92d3bad70e37c673254c7710c15b8d37_7.png)

![](img/92d3bad70e37c673254c7710c15b8d37_9.png)

---

上一节我们介绍了如何筛选和排名股票。本节中，我们来看看如何根据新的排名结果，执行具体的买卖交易。

![](img/92d3bad70e37c673254c7710c15b8d37_11.png)

## 计算持仓差异

首先，我们需要一个函数来获取当前账户持有的股票。以下是实现步骤：

![](img/92d3bad70e37c673254c7710c15b8d37_13.png)

1.  从 `context` 对象中获取当前所有持仓信息。
2.  遍历这些持仓，筛选出持有数量大于零的股票。
3.  将这些股票的代码存入一个列表并返回。

![](img/92d3bad70e37c673254c7710c15b8d37_15.png)

```python
def get_holdings(context):
    holdings = []
    for position in context.portfolio.positions.values():
        if position.quantity > 0:
            holdings.append(position.sid)
    return holdings
```

## 执行调仓逻辑

获取了新的股票排名和当前持仓后，我们就可以计算买卖清单了。以下是核心逻辑：

![](img/92d3bad70e37c673254c7710c15b8d37_17.png)

1.  **计算买卖清单**：需要买入的股票是新排名中有但当前未持有的；需要卖出的股票是当前持有但不在新排名中的。
2.  **执行卖出操作**：遍历卖出清单，将每只股票的持仓比例调整为0%。
3.  **执行买入操作**：计算可用资金，平均分配到每只需要买入的股票上，然后执行买入。

![](img/92d3bad70e37c673254c7710c15b8d37_19.png)

![](img/92d3bad70e37c673254c7710c15b8d37_21.png)

```python
def rebalance(context):
    # 获取新的股票排名
    new_stocks = get_stocks(context)
    # 获取当前持仓
    holdings = get_holdings(context)

    # 计算买卖清单（使用集合进行差集运算）
    to_buy = list(set(new_stocks) - set(holdings))
    to_sell = list(set(holdings) - set(new_stocks))

    # 先执行卖出操作
    for stock in to_sell:
        order_target_percent(stock, 0)

    # 如果没有需要买入的股票，则直接返回
    if len(to_buy) == 0:
        return

    # 计算每只股票应分配的资金（平均分配）
    available_cash = context.portfolio.cash
    average_value = available_cash / len(to_buy)

    # 执行买入操作
    for stock in to_buy:
        order_target_value(stock, average_value)
```

![](img/92d3bad70e37c673254c7710c15b8d37_23.png)

![](img/92d3bad70e37c673254c7710c15b8d37_25.png)

![](img/92d3bad70e37c673254c7710c15b8d37_27.png)

## 策略流程回顾与调试

![](img/92d3bad70e37c673254c7710c15b8d37_29.png)

![](img/92d3bad70e37c673254c7710c15b8d37_31.png)

![](img/92d3bad70e37c673254c7710c15b8d37_33.png)

现在，我们已经完成了策略的主要函数。让我们回顾一下整个策略的执行流程：

![](img/92d3bad70e37c673254c7710c15b8d37_35.png)

1.  在 `initialize` 函数中设定股票池和初始参数。
2.  每月定时执行 `rebalance` 函数进行调仓。
3.  在 `rebalance` 中，调用 `get_stocks` 函数获取新的股票排名。
4.  调用 `get_holdings` 函数获取当前持仓。
5.  对比两者，计算出需要买卖的股票清单。
6.  依次执行卖出和买入操作。

![](img/92d3bad70e37c673254c7710c15b8d37_37.png)

![](img/92d3bad70e37c673254c7710c15b8d37_39.png)

![](img/92d3bad70e37c673254c7710c15b8d37_41.png)

代码编写完成后，运行测试是必不可少的环节。初学者在运行代码时可能会遇到各种错误，例如语法错误（如使用了中文标点）、类型错误（如对列表直接进行减法运算）等。遇到报错时，需要耐心查看错误信息，定位问题代码行，并逐一修正。例如，将列表转换为集合（`set`）才能进行有效的差集运算以得到买卖清单。

![](img/92d3bad70e37c673254c7710c15b8d37_43.png)

![](img/92d3bad70e37c673254c7710c15b8d37_45.png)

---

![](img/92d3bad70e37c673254c7710c15b8d37_47.png)

![](img/92d3bad70e37c673254c7710c15b8d37_49.png)

本节课中我们一起学习了量化交易策略中调仓功能的完整实现。我们掌握了如何获取持仓、计算买卖差异、以及执行订单操作。整个过程涵盖了策略从决策到执行的关键步骤，是构建自动化交易系统的核心。通过实际的代码编写和调试，我们能够更深入地理解策略是如何在每一个交易周期中运作的。