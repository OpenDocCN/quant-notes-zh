# Python金融量化交易：53：完成策略交易展示结果 📈

![](img/488ade7e463071ec221d38234069e1f8_1.png)

在本节课中，我们将要学习如何完成一个量化交易策略的核心部分：根据新的股票排名结果，计算需要买入和卖出的股票，并执行交易操作。我们将编写一个函数来处理持仓与目标持仓之间的差异，并实现调仓逻辑。

![](img/488ade7e463071ec221d38234069e1f8_3.png)

---

![](img/488ade7e463071ec221d38234069e1f8_5.png)

![](img/488ade7e463071ec221d38234069e1f8_7.png)

上一节我们介绍了如何获取并筛选出排名靠前的股票。本节中我们来看看如何根据新的排名结果，进行实际的买卖操作。

![](img/488ade7e463071ec221d38234069e1f8_9.png)

![](img/488ade7e463071ec221d38234069e1f8_11.png)

首先，我们需要一个函数来获取当前账户的持仓信息。

![](img/488ade7e463071ec221d38234069e1f8_13.png)

以下是 `get_holdings` 函数的实现步骤，它用于获取当前持有的所有股票代码：

1.  从 `context` 对象中获取持仓信息 `context.portfolio.positions`。
2.  遍历所有持仓。
3.  判断每个持仓的数量 `position.amount` 是否大于 0。
4.  将所有数量大于 0 的持仓股票代码添加到一个列表中。
5.  返回这个持仓股票代码列表。

```python
def get_holdings(context):
    holdings = []
    for position in context.portfolio.positions.values():
        if position.amount > 0:
            holdings.append(position.sid)
    return holdings
```

![](img/488ade7e463071ec221d38234069e1f8_15.png)

---

![](img/488ade7e463071ec221d38234069e1f8_17.png)

现在，我们有了目标股票列表 `stocks` 和当前持仓列表 `holdings`。接下来，我们需要计算两者之间的差异，以确定需要买入和卖出的股票。

![](img/488ade7e463071ec221d38234069e1f8_19.png)

以下是计算买卖列表的逻辑：

![](img/488ade7e463071ec221d38234069e1f8_21.png)

1.  需要买入的股票 `to_buy` 是目标股票列表中有，但当前持仓中没有的股票。这可以通过集合的差集运算实现：`to_buy = set(stocks) - set(holdings)`。
2.  需要卖出的股票 `to_sell` 是当前持仓中有，但目标股票列表中没有的股票。同样使用集合差集：`to_sell = set(holdings) - set(stocks)`。

![](img/488ade7e463071ec221d38234069e1f8_23.png)

![](img/488ade7e463071ec221d38234069e1f8_25.png)

```python
holdings = get_holdings(context)
to_buy = set(stocks) - set(holdings)
to_sell = set(holdings) - set(stocks)
```

![](img/488ade7e463071ec221d38234069e1f8_27.png)

---

![](img/488ade7e463071ec221d38234069e1f8_29.png)

计算出买卖列表后，我们需要执行交易。通常的策略是先卖出不再需要的股票，再买入新的目标股票。

![](img/488ade7e463071ec221d38234069e1f8_31.png)

以下是执行卖出操作的步骤：

1.  遍历 `to_sell` 列表中的每一个股票。
2.  对每个股票执行 `order_target_percent` 函数，将其目标持仓比例设置为 0%，即全部卖出。

![](img/488ade7e463071ec221d38234069e1f8_33.png)

```python
for stock in to_sell:
    order_target_percent(stock, 0)
```

![](img/488ade7e463071ec221d38234069e1f8_35.png)

---

![](img/488ade7e463071ec221d38234069e1f8_37.png)

在执行买入操作前，我们需要检查是否有股票需要买入，并计算每只股票应分配的资金。

![](img/488ade7e463071ec221d38234069e1f8_39.png)

以下是执行买入操作的准备与步骤：

![](img/488ade7e463071ec221d38234069e1f8_41.png)

![](img/488ade7e463071ec221d38234069e1f8_43.png)

1.  首先判断 `to_buy` 列表是否为空。如果为空，则直接返回，无需操作。
2.  获取当前账户的总资产价值 `context.portfolio.portfolio_value`。
3.  计算平均每只股票应分配的资金比例：`avg_percent = 1.0 / len(to_buy)`。
4.  遍历 `to_buy` 列表中的每一个股票。
5.  对每个股票执行 `order_target_percent` 函数，将其目标持仓比例设置为 `avg_percent`。

![](img/488ade7e463071ec221d38234069e1f8_45.png)

![](img/488ade7e463071ec221d38234069e1f8_47.png)

```python
if len(to_buy) == 0:
    return

![](img/488ade7e463071ec221d38234069e1f8_49.png)

![](img/488ade7e463071ec221d38234069e1f8_51.png)

avg_percent = 1.0 / len(to_buy)
for stock in to_buy:
    order_target_percent(stock, avg_percent)
```

---

![](img/488ade7e463071ec221d38234069e1f8_53.png)

![](img/488ade7e463071ec221d38234069e1f8_55.png)

让我们回顾并整合整个调仓函数的逻辑。在每月执行的 `rebalance` 函数中，完整的流程如下：

![](img/488ade7e463071ec221d38234069e1f8_57.png)

![](img/488ade7e463071ec221d38234069e1f8_59.png)

1.  获取新的目标股票排名列表 `stocks`。
2.  获取当前持仓列表 `holdings`。
3.  计算需要卖出的股票列表 `to_sell` 和需要买入的股票列表 `to_buy`。
4.  执行卖出操作。
5.  执行买入操作。

![](img/488ade7e463071ec221d38234069e1f8_61.png)

![](img/488ade7e463071ec221d38234069e1f8_63.png)

在编写代码时，需要注意细节，例如确保使用英文标点符号、正确处理数据类型（如将列表转换为集合进行差集运算）以及检查函数调用的参数是否正确。初次运行可能会遇到错误，需要根据错误信息逐一调试和修正。

![](img/488ade7e463071ec221d38234069e1f8_65.png)

---

![](img/488ade7e463071ec221d38234069e1f8_67.png)

本节课中我们一起学习了量化交易策略的调仓执行部分。我们掌握了如何获取持仓、计算持仓与目标组合的差异、以及执行买卖订单来使实际持仓向目标组合靠拢。这是将策略逻辑转化为实际交易的关键一步。通过将这一模块与之前的股票筛选模块结合，我们就构建了一个完整的、可以自动运行的量化交易策略原型。