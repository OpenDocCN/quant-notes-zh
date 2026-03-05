# Python金融量化分析：P52：完成策略交易展示结果 📈

![](img/36858fe302bbde1b8457e02740e05b58_1.png)

在本节课中，我们将完成一个量化交易策略的核心函数，即根据最新的股票排名结果，计算需要买入和卖出的股票，并执行相应的交易操作。我们将学习如何获取持仓信息、计算持仓与目标组合的差异，并基于此进行调仓。

![](img/36858fe302bbde1b8457e02740e05b58_3.png)

---

![](img/36858fe302bbde1b8457e02740e05b58_5.png)

上一节我们介绍了如何获取并筛选出目标股票池。本节中，我们来看看如何根据这个新的排名结果，与我们当前的持仓进行比较，并执行实际的买卖操作。

![](img/36858fe302bbde1b8457e02740e05b58_7.png)

![](img/36858fe302bbde1b8457e02740e05b58_9.png)

首先，我们需要一个函数来获取当前账户的持仓信息。

![](img/36858fe302bbde1b8457e02740e05b58_11.png)

![](img/36858fe302bbde1b8457e02740e05b58_13.png)

以下是 `get_holdings` 函数的实现步骤：

1.  从 `context` 对象中获取持仓列表 `positions`。
2.  遍历 `positions` 列表。
3.  检查每个持仓的数量 `quantity` 是否大于0。
4.  将所有数量大于0的持仓股票代码添加到一个列表中。
5.  返回这个持仓股票代码列表。

```python
def get_holdings(context):
    holdings = []
    positions = context.portfolio.positions
    for position in positions:
        if positions[position].quantity > 0:
            holdings.append(position)
    return holdings
```

![](img/36858fe302bbde1b8457e02740e05b58_15.png)

---

![](img/36858fe302bbde1b8457e02740e05b58_17.png)

现在，我们可以在主调仓函数中使用这个函数。我们已经有了新的目标股票列表 `stocks`，也知道了当前的持仓 `holdings`。接下来，我们需要计算两者之间的差异。

以下是计算买卖列表的逻辑：

![](img/36858fe302bbde1b8457e02740e05b58_19.png)

*   **需要买入的股票 (`to_buy`)**：存在于新目标列表 `stocks` 中，但不在当前持仓 `holdings` 中的股票。公式为：`to_buy = set(stocks) - set(holdings)`。
*   **需要卖出的股票 (`to_sell`)**：存在于当前持仓 `holdings` 中，但不在新目标列表 `stocks` 中的股票。公式为：`to_sell = set(holdings) - set(stocks)`。

![](img/36858fe302bbde1b8457e02740e05b58_21.png)

```python
# 获取当前持仓
holdings = get_holdings(context)

![](img/36858fe302bbde1b8457e02740e05b58_23.png)

![](img/36858fe302bbde1b8457e02740e05b58_25.png)

# 计算需要买卖的股票列表
to_buy = set(stocks) - set(holdings)
to_sell = set(holdings) - set(stocks)
```

![](img/36858fe302bbde1b8457e02740e05b58_27.png)

---

![](img/36858fe302bbde1b8457e02740e05b58_29.png)

计算出买卖列表后，我们就可以执行交易了。通常的交易逻辑是先卖出，再买入。

![](img/36858fe302bbde1b8457e02740e05b58_31.png)

以下是执行卖出操作的步骤：

1.  遍历 `to_sell` 列表中的每一只股票。
2.  使用 `order_target_percent` 函数，将该股票的持仓比例调整为0%，即全部卖出。

```python
# 执行卖出操作
for stock in to_sell:
    order_target_percent(stock, 0)
```

![](img/36858fe302bbde1b8457e02740e05b58_33.png)

---

![](img/36858fe302bbde1b8457e02740e05b58_35.png)

卖出完成后，我们执行买入操作。在买入前，需要先检查是否有股票需要买入，并计算每只股票应分配的金额。

![](img/36858fe302bbde1b8457e02740e05b58_37.png)

以下是执行买入操作的步骤：

![](img/36858fe302bbde1b8457e02740e05b58_39.png)

1.  检查 `to_buy` 列表是否为空。如果为空，则直接返回。
2.  获取当前账户的总资产 `total_value`。
3.  计算平均每只股票应投入的金额 `average_value`。公式为：`average_value = total_value / len(to_buy)`。
4.  遍历 `to_buy` 列表中的每一只股票。
5.  使用 `order_target_value` 函数，为该股票下达目标市值为 `average_value` 的买入订单。

![](img/36858fe302bbde1b8457e02740e05b58_41.png)

![](img/36858fe302bbde1b8457e02740e05b58_43.png)

```python
# 执行买入操作
if len(to_buy) == 0:
    return

![](img/36858fe302bbde1b8457e02740e05b58_45.png)

total_value = context.portfolio.portfolio_value
average_value = total_value / len(to_buy)

![](img/36858fe302bbde1b8457e02740e05b58_47.png)

![](img/36858fe302bbde1b8457e02740e05b58_49.png)

for stock in to_buy:
    order_target_value(stock, average_value)
```

![](img/36858fe302bbde1b8457e02740e05b58_51.png)

---

![](img/36858fe302bbde1b8457e02740e05b58_53.png)

在编写和调试代码时，可能会遇到各种错误。例如，集合运算要求操作数是 `set` 类型，如果使用列表直接相减会导致错误。此外，代码中的标点符号（如逗号）必须使用英文格式，否则会引发语法错误。

![](img/36858fe302bbde1b8457e02740e05b58_55.png)

![](img/36858fe302bbde1b8457e02740e05b58_57.png)

遇到报错时，需要仔细阅读错误信息，定位问题代码行，并进行修正。例如，将列表转换为集合再进行差集运算，或者检查并更正所有中文标点为英文标点。

![](img/36858fe302bbde1b8457e02740e05b58_59.png)

![](img/36858fe302bbde1b8457e02740e05b58_61.png)

```python
# 错误示例：列表不能直接相减
# to_buy = stocks - holdings

![](img/36858fe302bbde1b8457e02740e05b58_63.png)

# 正确示例：转换为集合后再运算
to_buy = set(stocks) - set(holdings)
to_sell = set(holdings) - set(stocks)
```

![](img/36858fe302bbde1b8457e02740e05b58_65.png)

---

![](img/36858fe302bbde1b8457e02740e05b58_67.png)

本节课中我们一起学习了量化交易策略调仓环节的完整实现流程。我们首先编写了获取当前持仓的函数 `get_holdings`，然后通过集合运算计算出需要买入 (`to_buy`) 和卖出 (`to_sell`) 的股票列表。接着，我们按照先卖后买的顺序，使用 `order_target_percent` 和 `order_target_value` 函数执行了交易操作。最后，我们讨论了代码调试中常见的类型错误和语法错误及其解决方法。至此，一个基本的股票排名与定期调仓策略的核心逻辑已经构建完成。