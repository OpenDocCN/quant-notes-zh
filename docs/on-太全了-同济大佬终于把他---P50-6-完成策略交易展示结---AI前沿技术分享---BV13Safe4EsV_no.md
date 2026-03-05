# 量化投资与Python编程：P50：6-完成策略交易展示结果

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_1.png)

在本节课中，我们将继续完成REBON策略的交易执行部分。上一节我们介绍了如何根据财务指标对股票进行排名和筛选，本节中我们来看看如何根据最新的排名结果，与我们当前持有的股票进行比较，并执行具体的买入和卖出操作，以完成整个策略的调仓流程。

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_3.png)

## 计算持仓差异

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_5.png)

首先，我们需要一个函数来获取当前投资组合中持有的股票。以下是实现此功能的步骤。

```
def get_holdings(context):
    holdings = []
    for position in context.portfolio.positions:
        if context.portfolio.positions[position].quantity > 0:
            holdings.append(position)
    return holdings
```

这个函数遍历投资组合中的所有头寸，筛选出持有数量大于零的股票代码，并将其放入一个列表中返回。

## 执行调仓逻辑

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_7.png)

获取了当前持仓和新一轮的股票排名后，下一步是计算需要买入和卖出的股票列表。以下是计算逻辑。

```
# 获取新的股票排名列表
new_stocks = get_ranked_stocks(context)
# 获取当前持仓列表
current_holdings = get_holdings(context)

# 转换为集合以便进行集合运算
set_new = set(new_stocks)
set_hold = set(current_holdings)

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_9.png)

# 计算需要买入的股票（在新列表中但不在当前持仓中）
to_buy = list(set_new - set_hold)
# 计算需要卖出的股票（在当前持仓中但不在新列表中）
to_sell = list(set_hold - set_new)
```

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_11.png)

## 执行卖出操作

在量化交易中，通常遵循“先卖后买”的原则，以确保有足够的资金进行新的投资。以下是卖出操作的实现。

对于需要卖出的股票列表，我们遍历每一项，并将其目标持仓比例设置为0%，即清仓。

```
for stock in to_sell:
    order_target_percent(stock, 0)
```

## 执行买入操作

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_13.png)

在执行买入操作前，需要先检查是否有股票需要买入。如果没有，则直接返回。以下是买入前的检查逻辑。

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_15.png)

```
if len(to_buy) == 0:
    return
```

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_17.png)

如果有股票需要买入，我们需要计算可用于投资的资金，并平均分配到每只待买入的股票上。以下是计算和执行的步骤。

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_19.png)

```
# 获取当前投资组合的总价值
total_value = context.portfolio.portfolio_value
# 计算每只股票应分配的平均资金
average_cash = total_value / len(to_buy)

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_21.png)

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_23.png)

for stock in to_buy:
    order_target_value(stock, average_cash)
```

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_25.png)

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_27.png)

## 代码整合与错误调试

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_29.png)

将以上所有步骤整合到主调仓函数中后，运行代码可能会遇到一些语法或逻辑错误。常见的错误包括使用了中文标点符号、变量名拼写错误或数据类型不匹配。

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_31.png)

例如，在计算`to_buy`时，如果直接对两个列表进行减法操作会导致错误，必须先将列表转换为集合（set）。

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_33.png)

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_35.png)

另一个常见错误是在调用数据接口时，字段名书写错误或使用了不支持的字符，需要仔细检查并修正。

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_37.png)

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_39.png)

## 策略流程回顾

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_41.png)

现在，让我们回顾一下整个策略的完整流程。

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_43.png)

1.  **初始化**：在`initialize`函数中设定股票池和调仓频率（例如每月）。
2.  **调仓逻辑**：在每次调仓时（例如`rebalance`函数），执行以下操作：
    *   调用`get_ranked_stocks`函数，根据最新的财务数据获取股票排名。
    *   调用`get_holdings`函数，获取当前投资组合的持仓情况。
    *   计算需要买入（`to_buy`）和卖出（`to_sell`）的股票列表。
    *   遍历`to_sell`列表，执行卖出操作（`order_target_percent(stock, 0)`）。
    *   计算可用资金，并平均分配给`to_buy`列表中的股票，执行买入操作（`order_target_value`）。

![](img/cfd3348650ee36b84a4c5b5fc35ac0c9_45.png)

本节课中我们一起学习了如何将股票排名策略转化为实际的交易指令。我们实现了获取持仓、计算调仓差异、执行卖出和买入操作等核心功能，并讨论了在代码整合过程中可能遇到的典型错误及其解决方法。通过本节的学习，你已经掌握了构建一个完整量化交易策略执行端的关键步骤。