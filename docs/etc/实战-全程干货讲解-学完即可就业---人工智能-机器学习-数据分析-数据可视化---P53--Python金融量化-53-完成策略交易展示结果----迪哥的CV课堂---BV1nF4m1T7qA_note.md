# Python金融量化：53：完成策略交易展示结果

![](img/ee485b11db907b7e86dc96c15537a55e_1.png)

在本节课中，我们将继续完成量化交易策略的核心部分——`rebalance`函数。我们将学习如何根据新的股票排名，计算持仓差异，并执行具体的买卖操作。通过本节内容，你将掌握策略调仓的逻辑实现。

---

![](img/ee485b11db907b7e86dc96c15537a55e_3.png)

上一节我们介绍了如何获取并筛选股票排名。本节中，我们来看看如何根据新的排名结果，执行具体的交易操作。

![](img/ee485b11db907b7e86dc96c15537a55e_5.png)

![](img/ee485b11db907b7e86dc96c15537a55e_7.png)

首先，我们需要一个函数来获取当前账户的持仓情况。

![](img/ee485b11db907b7e86dc96c15537a55e_9.png)

以下是获取当前持仓股票的代码：

![](img/ee485b11db907b7e86dc96c15537a55e_11.png)

```python
def get_holdings(context):
    holdings = []
    for position in context.portfolio.positions:
        if context.portfolio.positions[position].quantity > 0:
            holdings.append(position)
    return holdings
```

![](img/ee485b11db907b7e86dc96c15537a55e_13.png)

这个函数遍历投资组合中的所有持仓，如果某只股票的持有数量大于零，就将其添加到持仓列表中并返回。

---

获取了当前持仓后，我们需要将其与新的股票排名进行比较，以确定需要买入和卖出的股票。

![](img/ee485b11db907b7e86dc96c15537a55e_15.png)

以下是计算买卖列表的逻辑：

![](img/ee485b11db907b7e86dc96c15537a55e_17.png)

```python
# 获取新的股票排名列表
stocks = get_stocks(context)
# 获取当前持仓列表
holdings = get_holdings(context)

![](img/ee485b11db907b7e86dc96c15537a55e_19.png)

# 计算需要买入的股票：在新排名中但不在当前持仓中
to_buy = set(stocks) - set(holdings)
# 计算需要卖出的股票：在当前持仓中但不在新排名中
to_sell = set(holdings) - set(stocks)
```

通过集合的差集运算，我们可以清晰地分离出需要操作的股票。

![](img/ee485b11db907b7e86dc96c15537a55e_21.png)

---

![](img/ee485b11db907b7e86dc96c15537a55e_23.png)

确定了买卖列表后，接下来就是执行交易指令。在量化交易中，通常遵循“先卖后买”的原则，以释放资金。

![](img/ee485b11db907b7e86dc96c15537a55e_25.png)

以下是执行卖出操作的代码：

![](img/ee485b11db907b7e86dc96c15537a55e_27.png)

```python
for stock in to_sell:
    order_target_percent(stock, 0)
```

![](img/ee485b11db907b7e86dc96c15537a55e_29.png)

这段代码遍历`to_sell`列表中的每只股票，并使用`order_target_percent`函数将其目标持仓比例设置为0%，即全部卖出。

---

在执行买入操作前，需要先判断是否有股票需要买入。如果没有，则直接返回。

![](img/ee485b11db907b7e86dc96c15537a55e_31.png)

以下是执行买入操作的代码：

![](img/ee485b11db907b7e86dc96c15537a55e_33.png)

```python
if len(to_buy) == 0:
    return

# 计算可用于投资的资金总额
total_value = context.portfolio.portfolio_value
# 计算每只计划买入的股票应分配的平均资金
average_allocation = total_value / len(to_buy)

![](img/ee485b11db907b7e86dc96c15537a55e_35.png)

for stock in to_buy:
    order_target_value(stock, average_allocation)
```

![](img/ee485b11db907b7e86dc96c15537a55e_37.png)

![](img/ee485b11db907b7e86dc96c15537a55e_39.png)

如果`to_buy`列表不为空，我们首先计算账户总资产，然后将其平均分配给每只要买入的股票，并使用`order_target_value`函数执行买入。

![](img/ee485b11db907b7e86dc96c15537a55e_41.png)

---

![](img/ee485b11db907b7e86dc96c15537a55e_43.png)

现在，让我们将以上所有步骤整合到每月调仓的`rebalance`函数中。

![](img/ee485b11db907b7e86dc96c15537a55e_45.png)

![](img/ee485b11db907b7e86dc96c15537a55e_47.png)

以下是完整的`rebalance`函数框架：

![](img/ee485b11db907b7e86dc96c15537a55e_49.png)

```python
def rebalance(context):
    # 1. 获取新的股票排名
    stocks = get_stocks(context)
    # 2. 获取当前持仓
    holdings = get_holdings(context)
    # 3. 计算买卖列表
    to_buy = set(stocks) - set(holdings)
    to_sell = set(holdings) - set(stocks)
    # 4. 执行卖出操作
    for stock in to_sell:
        order_target_percent(stock, 0)
    # 5. 执行买入操作
    if len(to_buy) > 0:
        total_value = context.portfolio.portfolio_value
        average_allocation = total_value / len(to_buy)
        for stock in to_buy:
            order_target_value(stock, average_allocation)
```

---

![](img/ee485b11db907b7e86dc96c15537a55e_51.png)

![](img/ee485b11db907b7e86dc96c15537a55e_53.png)

代码编写完成后，运行测试是必不可少的环节。初次运行时可能会遇到一些语法错误或逻辑问题。

![](img/ee485b11db907b7e86dc96c15537a55e_55.png)

![](img/ee485b11db907b7e86dc96c15537a55e_57.png)

常见的错误包括使用了中文标点符号、遗漏逗号或变量名拼写错误。例如，在获取基本面数据时，字段名`EPS`如果写成了中文逗号分隔，就会导致程序报错。仔细检查并修正这些细节是调试的关键。

![](img/ee485b11db907b7e86dc96c15537a55e_59.png)

另一个常见问题是数据类型不匹配。例如，直接对列表进行减法操作是不被支持的。我们需要先将列表转换为集合（`set`），然后再进行差集运算。

![](img/ee485b11db907b7e86dc96c15537a55e_61.png)

修正这些问题后，再次运行程序，观察是否能够顺利执行。

![](img/ee485b11db907b7e86dc96c15537a55e_63.png)

---

![](img/ee485b11db907b7e86dc96c15537a55e_65.png)

本节课中我们一起学习了量化交易策略调仓的完整实现流程。我们首先编写了获取持仓的函数，然后通过集合运算找出需要买卖的股票列表，最后按照“先卖后买”的原则执行交易订单。整个过程涵盖了策略从决策到执行的关键步骤，是构建自动化交易系统的核心。通过反复测试和调试，我们可以确保策略逻辑的正确性与稳健性。