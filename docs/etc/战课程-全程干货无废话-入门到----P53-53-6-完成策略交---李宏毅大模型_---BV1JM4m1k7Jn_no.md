# Python金融分析与量化交易实战：P53：53.6-完成策略交易展示结果 📈

![](img/7e031d03f2fcfb667abe8caa0496d444_1.png)

在本节课中，我们将完成一个量化交易策略的核心部分：`rebalance`函数。我们将学习如何根据新的股票排名，计算需要买入和卖出的股票，并执行相应的交易操作。整个过程将涉及持仓管理、集合运算和订单执行。

![](img/7e031d03f2fcfb667abe8caa0496d444_3.png)

---

![](img/7e031d03f2fcfb667abe8caa0496d444_5.png)

![](img/7e031d03f2fcfb667abe8caa0496d444_7.png)

上一节我们介绍了如何获取并筛选股票排名。本节中，我们来看看如何根据新的排名结果，调整我们的实际持仓。

![](img/7e031d03f2fcfb667abe8caa0496d444_9.png)

![](img/7e031d03f2fcfb667abe8caa0496d444_11.png)

首先，我们需要一个函数来获取当前账户的持仓信息。

![](img/7e031d03f2fcfb667abe8caa0496d444_13.png)

以下是获取当前持仓股票列表的步骤：
1.  从 `context` 对象中获取持仓信息 `context.portfolio.positions`。
2.  遍历所有持仓，检查其数量是否大于0。
3.  将所有数量大于0的持仓股票代码加入一个列表。
4.  返回这个持仓股票列表。

```python
def get_holdings(context):
    holdings = []
    for position in context.portfolio.positions:
        # 检查该持仓的数量是否大于0
        if context.portfolio.positions[position].quantity > 0:
            holdings.append(position)
    return holdings
```

![](img/7e031d03f2fcfb667abe8caa0496d444_15.png)

---

![](img/7e031d03f2fcfb667abe8caa0496d444_17.png)

现在，我们得到了新的股票排名 `stocks`，也知道了当前的持仓 `holdings`。接下来，我们需要计算买卖清单。

![](img/7e031d03f2fcfb667abe8caa0496d444_19.png)

以下是计算买卖清单的逻辑：
*   **需要买入的股票 (`to_buy`)**：在新排名 `stocks` 中，但不在当前持仓 `holdings` 中的股票。
*   **需要卖出的股票 (`to_sell`)**：在当前持仓 `holdings` 中，但不在新排名 `stocks` 中的股票。

![](img/7e031d03f2fcfb667abe8caa0496d444_21.png)

我们可以使用集合的差集运算来实现这个逻辑。为了进行集合运算，需要先将列表转换为集合。

![](img/7e031d03f2fcfb667abe8caa0496d444_23.png)

```python
# 将列表转换为集合
stocks_set = set(stocks)
holdings_set = set(holdings)

![](img/7e031d03f2fcfb667abe8caa0496d444_25.png)

![](img/7e031d03f2fcfb667abe8caa0496d444_27.png)

# 计算买卖清单
to_buy = list(stocks_set - holdings_set)  # 需要买入的股票
to_sell = list(holdings_set - stocks_set) # 需要卖出的股票
```

![](img/7e031d03f2fcfb667abe8caa0496d444_29.png)

---

![](img/7e031d03f2fcfb667abe8caa0496d444_31.png)

计算出买卖清单后，我们就可以执行交易了。通常的交易原则是“先卖后买”，以便释放资金。

![](img/7e031d03f2fcfb667abe8caa0496d444_33.png)

以下是执行卖出操作的步骤：
1.  遍历 `to_sell` 列表中的每一只股票。
2.  使用 `order_target_percent` 函数，将该股票的持仓比例调整为0%，即全部卖出。

```python
# 执行卖出操作
for stock in to_sell:
    order_target_percent(stock, 0)
```

![](img/7e031d03f2fcfb667abe8caa0496d444_35.png)

---

![](img/7e031d03f2fcfb667abe8caa0496d444_37.png)

卖出操作完成后，我们开始处理买入。在买入前，需要先检查是否有股票需要买入。

![](img/7e031d03f2fcfb667abe8caa0496d444_39.png)

以下是执行买入操作的步骤：
1.  检查 `to_buy` 列表是否为空。如果为空，则直接返回，无需操作。
2.  计算可用于买入股票的总资金：`context.portfolio.portfolio_value`。
3.  计算每只待买入股票应分配的资金：`总资金 / 待买入股票数量`。
4.  遍历 `to_buy` 列表，使用 `order_target_value` 函数，为每只股票买入计算好的金额。

![](img/7e031d03f2fcfb667abe8caa0496d444_41.png)

![](img/7e031d03f2fcfb667abe8caa0496d444_43.png)

```python
# 执行买入操作
if len(to_buy) == 0:
    return  # 没有需要买入的股票，直接返回

![](img/7e031d03f2fcfb667abe8caa0496d444_45.png)

![](img/7e031d03f2fcfb667abe8caa0496d444_47.png)

# 计算平均每只股票投入的资金
avg_cash = context.portfolio.portfolio_value / len(to_buy)

![](img/7e031d03f2fcfb667abe8caa0496d444_49.png)

![](img/7e031d03f2fcfb667abe8caa0496d444_51.png)

for stock in to_buy:
    order_target_value(stock, avg_cash)
```

![](img/7e031d03f2fcfb667abe8caa0496d444_53.png)

---

![](img/7e031d03f2fcfb667abe8caa0496d444_55.png)

现在，让我们回顾并整合整个 `rebalance` 函数的逻辑。

![](img/7e031d03f2fcfb667abe8caa0496d444_57.png)

![](img/7e031d03f2fcfb667abe8caa0496d444_59.png)

以下是完整的 `rebalance` 函数流程：
1.  **获取新排名**：调用 `get_stocks` 函数，基于财务指标（如市盈率PE、市净率PB）筛选出当前周期的目标股票列表 `stocks`。
2.  **获取当前持仓**：调用 `get_holdings` 函数，获取当前账户实际持有的股票列表 `holdings`。
3.  **计算买卖清单**：通过集合运算，计算出需要卖出的股票 `to_sell` 和需要买入的股票 `to_buy`。
4.  **执行交易**：
    *   先遍历 `to_sell` 列表，卖出所有不再需要的股票。
    *   再遍历 `to_buy` 列表，将可用资金平均分配给每只新入选的股票并买入。

![](img/7e031d03f2fcfb667abe8caa0496d444_61.png)

![](img/7e031d03f2fcfb667abe8caa0496d444_63.png)

运行代码时可能会遇到语法错误（如中文标点）或逻辑错误（如对列表直接进行减法运算）。需要仔细检查代码，确保使用英文标点，并在进行集合运算前将列表转换为集合。

![](img/7e031d03f2fcfb667abe8caa0496d444_65.png)

---

![](img/7e031d03f2fcfb667abe8caa0496d444_67.png)

本节课中我们一起学习了如何构建一个完整的调仓函数。我们掌握了获取持仓、通过集合运算计算买卖差异、以及执行卖出和买入订单的关键步骤。这是将量化策略思想转化为实际交易指令的核心环节。通过这个流程，策略可以根据最新的市场数据和排名，自动调整投资组合，向目标状态靠拢。