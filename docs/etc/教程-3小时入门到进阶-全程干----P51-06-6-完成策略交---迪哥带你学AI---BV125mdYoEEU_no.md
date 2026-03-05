# Python金融分析与量化交易实战教程：P51：06-6-完成策略交易展示结果 🚀

在本节课中，我们将完成REBON函数的核心部分，实现基于股票排名差异的调仓逻辑。我们将学习如何获取当前持仓、计算买卖列表，并执行具体的交易操作。

![](img/4d478e979ec845b77e19fff0cfddae0e_1.png)

---

上一节我们介绍了如何筛选和排名股票。本节中，我们来看看如何根据新的排名结果与现有持仓的差异，来执行具体的买入和卖出操作。

![](img/4d478e979ec845b77e19fff0cfddae0e_3.png)

![](img/4d478e979ec845b77e19fff0cfddae0e_5.png)

首先，我们需要一个函数来获取当前账户的持仓股票列表。

以下是`get_holdings`函数的实现步骤：

1.  从`context`对象中获取持仓信息`positions`。
2.  遍历所有持仓，检查其数量是否大于0。
3.  将所有数量大于0的持仓股票代码加入一个列表。
4.  返回这个持仓股票列表。

```python
def get_holdings(context):
    # 初始化一个空列表，用于存放当前持有的股票
    holdings = []
    # 遍历context中的所有持仓
    for position in context.portfolio.positions:
        # 获取该持仓的数量
        quantity = context.portfolio.positions[position].quantity
        # 如果持仓数量大于0，则视为有效持仓，将其股票代码加入列表
        if quantity > 0:
            holdings.append(position)
    # 返回当前持仓股票列表
    return holdings
```

---

现在，我们可以在主调仓函数中使用这个函数。我们已经有了新的股票排名`stocks`，也知道了当前持仓`holdings`。接下来，我们需要计算需要买入和卖出的股票。

![](img/4d478e979ec845b77e19fff0cfddae0e_7.png)

以下是计算买卖列表的逻辑：

*   **需要买入的股票 (`to_buy`)**：在新排名`stocks`中，但不在当前持仓`holdings`中的股票。这代表了我们认为有潜力但尚未持有的股票。
*   **需要卖出的股票 (`to_sell`)**：在当前持仓`holdings`中，但不在新排名`stocks`中的股票。这代表了我们不再看好的现有持仓。

```python
# 获取新的股票排名
stocks = get_rank(context)
# 获取当前持仓
holdings = get_holdings(context)

# 将列表转换为集合，以便进行集合运算（求差集）
stocks_set = set(stocks)
holdings_set = set(holdings)

![](img/4d478e979ec845b77e19fff0cfddae0e_9.png)

# 计算需要买入的股票：在新排名中但不在持仓中
to_buy = stocks_set - holdings_set
# 计算需要卖出的股票：在持仓中但不在新排名中
to_sell = holdings_set - stocks_set
```

![](img/4d478e979ec845b77e19fff0cfddae0e_11.png)

---

计算出买卖列表后，我们就可以执行交易了。通常的策略是先卖出不再看好的股票，释放资金，再用这些资金买入新的股票。

以下是执行卖出操作的步骤：

1.  遍历`to_sell`列表中的每一只股票。
2.  对每只股票执行`order_target_percent`函数，将其目标持仓比例设置为0%，即全部卖出。

```python
# 先执行卖出操作
for stock in to_sell:
    # order_target_percent函数用于将某只股票的持仓调整到目标比例
    # 将目标比例设为0，即清仓该股票
    order_target_percent(stock, 0)
```

---

卖出操作完成后，我们获得了现金。接下来，我们需要将这些现金平均分配到需要买入的股票上，并执行买入。

![](img/4d478e979ec845b77e19fff0cfddae0e_13.png)

以下是执行买入操作的步骤：

1.  检查`to_buy`列表是否为空。如果为空，则直接返回，无需操作。
2.  计算可用于投资的现金总额（`portfolio_value`）。
3.  计算每只待买入股票应分配的资金（`average_value`），即总现金除以待买入股票数量。
4.  遍历`to_buy`列表，对每只股票执行买入操作，买入价值为`average_value`的该股票。

![](img/4d478e979ec845b77e19fff0cfddae0e_15.png)

```python
# 如果没有需要买入的股票，则直接返回
if len(to_buy) == 0:
    return

![](img/4d478e979ec845b77e19fff0cfddae0e_17.png)

![](img/4d478e979ec845b77e19fff0cfddae0e_19.png)

# 获取当前投资组合的总价值（现金+股票市值）
total_cash = context.portfolio.portfolio_value
# 计算平均每只股票应投入的资金
average_value = total_cash / len(to_buy)

![](img/4d478e979ec845b77e19fff0cfddae0e_21.png)

# 执行买入操作
for stock in to_buy:
    # order_target_value函数用于买入股票，使其持仓价值达到目标金额
    # 这里我们买入价值为average_value的股票
    order_target_value(stock, average_value)
```

![](img/4d478e979ec845b77e19fff0cfddae0e_23.png)

---

![](img/4d478e979ec845b77e19fff0cfddae0e_25.png)

现在，让我们回顾并整合整个调仓函数的逻辑。

![](img/4d478e979ec845b77e19fff0cfddae0e_27.png)

以下是完整的`rebalance`函数流程：

![](img/4d478e979ec845b77e19fff0cfddae0e_29.png)

1.  **获取新排名**：调用`get_rank(context)`函数，基于财务指标计算出新的股票排名。
2.  **获取当前持仓**：调用`get_holdings(context)`函数，获取当前账户中持有的股票列表。
3.  **计算买卖列表**：通过集合运算，得到需要买入(`to_buy`)和卖出(`to_sell`)的股票列表。
4.  **执行卖出**：遍历`to_sell`列表，将每只股票清仓。
5.  **执行买入**：计算可用资金，平均分配给`to_buy`列表中的每只股票，并执行买入。

```python
def rebalance(context):
    # 1. 获取新的股票排名
    stocks = get_rank(context)
    # 2. 获取当前持仓
    holdings = get_holdings(context)

    # 3. 计算买卖列表（转换为集合进行运算）
    stocks_set = set(stocks)
    holdings_set = set(holdings)
    to_buy = stocks_set - holdings_set
    to_sell = holdings_set - stocks_set

    # 4. 执行卖出操作
    for stock in to_sell:
        order_target_percent(stock, 0)

    # 5. 执行买入操作
    if len(to_buy) == 0:
        return
    total_cash = context.portfolio.portfolio_value
    average_value = total_cash / len(to_buy)
    for stock in to_buy:
        order_target_value(stock, average_value)
```

![](img/4d478e979ec845b77e19fff0cfddae0e_31.png)

![](img/4d478e979ec845b77e19fff0cfddae0e_33.png)

---

![](img/4d478e979ec845b77e19fff0cfddae0e_35.png)

**代码调试与常见错误**

![](img/4d478e979ec845b77e19fff0cfddae0e_37.png)

![](img/4d478e979ec845b77e19fff0cfddae0e_39.png)

在编写和运行策略时，遇到错误是正常的。以下是一些常见错误及解决方法：

![](img/4d478e979ec845b77e19fff0cfddae0e_41.png)

*   **无效字符错误**：代码中使用了中文标点符号（如中文逗号），需要替换为英文标点。
*   **类型错误**：对列表(`list`)直接进行减法(`-`)操作是不被支持的。需要先将列表转换为集合(`set`)再进行差集运算。
*   **函数参数错误**：检查`order_target_percent`和`order_target_value`等函数的参数是否正确传递，例如股票代码和数值。
*   **数据获取错误**：检查`get_rank`函数中获取财务指标（如`fundamentals.eod_derivative_indicator.eps`）的字段名拼写是否正确。

遇到报错时，仔细阅读错误信息，定位到出错的行，并检查相关的语法和逻辑。

![](img/4d478e979ec845b77e19fff0cfddae0e_43.png)

---

![](img/4d478e979ec845b77e19fff0cfddae0e_45.png)

本节课中我们一起学习了量化交易策略调仓环节的核心实现。我们掌握了如何获取持仓、根据排名差异计算买卖列表，以及使用`order_target_percent`和`order_target_value`函数执行具体的卖出和买入操作。通过将列表转换为集合进行运算，我们可以高效地处理股票列表之间的差异。最后，我们整合了所有步骤，完成了完整的`rebalance`调仓函数，并了解了调试代码的基本方法。至此，一个简单的多因子选股与定期调仓策略框架已经搭建完成。