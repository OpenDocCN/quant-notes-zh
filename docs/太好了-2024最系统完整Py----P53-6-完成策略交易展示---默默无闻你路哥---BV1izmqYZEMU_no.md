# Python金融分析与量化交易实战：P53：6-完成策略交易展示结果

![](img/7d3e9d52794f8a6a459cf06380ab684f_1.png)

在本节课中，我们将继续完成量化交易策略的核心函数。上一节我们介绍了如何筛选出当前排名靠前的股票，本节中我们来看看如何根据新的排名结果，计算需要买入和卖出的股票，并执行实际的交易操作。

![](img/7d3e9d52794f8a6a459cf06380ab684f_3.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_5.png)

## 计算持仓差异函数

![](img/7d3e9d52794f8a6a459cf06380ab684f_7.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_9.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_11.png)

首先，我们需要一个函数来获取当前账户的持仓情况。以下是该函数的实现步骤。

![](img/7d3e9d52794f8a6a459cf06380ab684f_13.png)

```python
def get_holdings(context):
    holdings = []
    for position in context.portfolio.positions:
        if context.portfolio.positions[position].quantity > 0:
            holdings.append(position)
    return holdings
```

这个函数遍历投资组合中的所有持仓，如果某只股票的持有数量大于零，就将其添加到持仓列表中并返回。

![](img/7d3e9d52794f8a6a459cf06380ab684f_15.png)

## 确定买卖清单

![](img/7d3e9d52794f8a6a459cf06380ab684f_17.png)

获取当前持仓后，我们需要将其与新的股票排名列表进行比较，以确定需要买入和卖出的股票。

![](img/7d3e9d52794f8a6a459cf06380ab684f_19.png)

以下是计算买卖清单的逻辑：
*   **买入清单**：新的股票排名列表中，但不在当前持仓列表里的股票。
*   **卖出清单**：当前持仓列表中，但不在新的股票排名列表里的股票。

![](img/7d3e9d52794f8a6a459cf06380ab684f_21.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_23.png)

```python
# 假设 stocks 是新的排名股票列表
stocks = get_stocks(context)
holdings = get_holdings(context)

![](img/7d3e9d52794f8a6a459cf06380ab684f_25.png)

# 转换为集合以便进行集合运算
stocks_set = set(stocks)
holdings_set = set(holdings)

![](img/7d3e9d52794f8a6a459cf06380ab684f_27.png)

# 计算买卖清单
to_buy = list(stocks_set - holdings_set)  # 需要买入的股票
to_sell = list(holdings_set - stocks_set) # 需要卖出的股票
```

![](img/7d3e9d52794f8a6a459cf06380ab684f_29.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_31.png)

## 执行交易操作

确定了买卖清单后，接下来就是执行具体的交易指令。通常遵循“先卖后买”的原则，以确保有足够的资金进行买入。

![](img/7d3e9d52794f8a6a459cf06380ab684f_33.png)

以下是执行卖出操作的代码：

![](img/7d3e9d52794f8a6a459cf06380ab684f_35.png)

```python
# 遍历卖出清单，将持仓比例调整为0（即全部卖出）
for stock in to_sell:
    order_target_percent(stock, 0)
```

![](img/7d3e9d52794f8a6a459cf06380ab684f_37.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_39.png)

在执行买入操作前，需要检查买入清单是否为空，并计算每只股票应分配的资金。

![](img/7d3e9d52794f8a6a459cf06380ab684f_41.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_43.png)

以下是执行买入操作的代码：

![](img/7d3e9d52794f8a6a459cf06380ab684f_45.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_47.png)

```python
# 如果买入清单为空，则直接返回
if len(to_buy) == 0:
    return

![](img/7d3e9d52794f8a6a459cf06380ab684f_49.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_51.png)

# 计算可用总资产和每只股票平均分配的资金比例
total_value = context.portfolio.portfolio_value
average_allocation = 1.0 / len(to_buy)  # 平均分配资金

![](img/7d3e9d52794f8a6a459cf06380ab684f_53.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_55.png)

# 遍历买入清单，按计算出的比例买入
for stock in to_buy:
    order_target_percent(stock, average_allocation)
```

![](img/7d3e9d52794f8a6a459cf06380ab684f_57.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_59.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_61.png)

## 策略流程回顾与调试

![](img/7d3e9d52794f8a6a459cf06380ab684f_63.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_65.png)

现在，我们已经将策略的主要部分组合完成。让我们回顾一下整个策略的月度调仓流程：
1.  获取新的股票排名列表。
2.  获取当前的持仓列表。
3.  通过集合运算，计算出需要买入和卖出的股票清单。
4.  执行卖出操作。
5.  执行买入操作。

![](img/7d3e9d52794f8a6a459cf06380ab684f_67.png)

在编写代码时，难免会出现错误。例如，常见的错误包括使用了中文标点符号、遗漏了逗号，或者对列表直接进行减法运算。遇到报错时，需要仔细检查代码，根据错误提示进行修正。例如，将列表转换为集合（`set`）再进行差集运算，可以避免类型错误。

![](img/7d3e9d52794f8a6a459cf06380ab684f_69.png)

![](img/7d3e9d52794f8a6a459cf06380ab684f_71.png)

本节课中我们一起学习了如何根据股票排名变化来构建交易逻辑，包括计算持仓差异、生成买卖清单以及执行交易订单。这是将策略思想转化为实际可执行代码的关键一步。下一节，我们将运行这个策略并查看其回测结果。