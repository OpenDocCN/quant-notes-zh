# Python金融时间序列分析与量化交易实战教程：P50：49.完成策略交易展示结果 📈

![](img/4b14b82b643e217b3cd99af540e6291d_0.png)

![](img/4b14b82b643e217b3cd99af540e6291d_2.png)

## 概述
在本节课中，我们将完成一个量化交易策略的核心部分——调仓逻辑。我们将学习如何根据新的股票排名结果，与当前持仓进行比较，计算出需要买入和卖出的股票列表，并执行相应的交易操作。

![](img/4b14b82b643e217b3cd99af540e6291d_4.png)

![](img/4b14b82b643e217b3cd99af540e6291d_6.png)

---

![](img/4b14b82b643e217b3cd99af540e6291d_8.png)

![](img/4b14b82b643e217b3cd99af540e6291d_10.png)

## 计算持仓股票列表

![](img/4b14b82b643e217b3cd99af540e6291d_12.png)

![](img/4b14b82b643e217b3cd99af540e6291d_14.png)

![](img/4b14b82b643e217b3cd99af540e6291d_16.png)

上一节我们介绍了如何获取新的股票排名。本节中，我们来看看如何获取当前账户的持仓情况。

![](img/4b14b82b643e217b3cd99af540e6291d_18.png)

我们需要编写一个函数，用于获取当前账户中所有持仓数量大于零的股票列表。

![](img/4b14b82b643e217b3cd99af540e6291d_20.png)

以下是 `get_holdings` 函数的实现步骤：

![](img/4b14b82b643e217b3cd99af540e6291d_22.png)

1.  从 `context` 对象中获取当前所有的持仓信息 `positions`。
2.  遍历 `positions` 列表。
3.  检查每个持仓的股票数量 `quantity`。
4.  如果 `quantity` 大于 0，则将该股票代码加入持仓列表。
5.  返回最终的持仓股票列表。

![](img/4b14b82b643e217b3cd99af540e6291d_24.png)

```python
def get_holdings(context):
    # 初始化持仓列表
    holdings = []
    # 获取当前所有持仓
    positions = context.portfolio.positions
    # 遍历持仓
    for position in positions:
        # 获取该股票的持仓数量
        quantity = context.portfolio.positions[position].quantity
        # 如果持仓数量大于0，则加入列表
        if quantity > 0:
            holdings.append(position)
    # 返回持仓股票列表
    return holdings
```

![](img/4b14b82b643e217b3cd99af540e6291d_26.png)

---

![](img/4b14b82b643e217b3cd99af540e6291d_28.png)

![](img/4b14b82b643e217b3cd99af540e6291d_30.png)

## 执行调仓操作

![](img/4b14b82b643e217b3cd99af540e6291d_32.png)

获取了新的股票排名和当前持仓后，接下来我们需要根据两者的差异来执行买卖操作。

![](img/4b14b82b643e217b3cd99af540e6291d_34.png)

![](img/4b14b82b643e217b3cd99af540e6291d_36.png)

核心逻辑是计算两个集合的差集：
*   **需要买入的股票** = 新排名股票集合 - 当前持仓股票集合
*   **需要卖出的股票** = 当前持仓股票集合 - 新排名股票集合

![](img/4b14b82b643e217b3cd99af540e6291d_38.png)

以下是调仓函数 `rebalance` 的核心步骤：

![](img/4b14b82b643e217b3cd99af540e6291d_40.png)

1.  调用 `get_stocks` 函数获取新的股票排名列表 `stocks`。
2.  调用 `get_holdings` 函数获取当前持仓列表 `holdings`。
3.  将列表转换为集合，以便进行集合运算。
4.  计算需要买入 (`to_buy`) 和需要卖出 (`to_sell`) 的股票集合。
5.  遍历 `to_sell` 集合，执行卖出操作，将持仓比例调整为 0%。
6.  计算可用资金，并平均分配到每只需要买入的股票上。
7.  遍历 `to_buy` 集合，执行买入操作。

![](img/4b14b82b643e217b3cd99af540e6291d_42.png)

```python
def rebalance(context):
    # 1. 获取新一期排名股票
    stocks = get_stocks(context)
    # 2. 获取当前持仓股票
    holdings = get_holdings(context)

    # 3. 转换为集合以便计算差集
    stocks_set = set(stocks)
    holdings_set = set(holdings)

    # 4. 计算买卖列表
    to_buy = stocks_set - holdings_set  # 需要买入的股票
    to_sell = holdings_set - stocks_set # 需要卖出的股票

    # 5. 执行卖出操作
    for stock in to_sell:
        # order_target_percent 函数用于调整股票持仓至目标比例
        # 这里将目标比例设为0，即全部卖出
        order_target_percent(stock, 0)

    # 6. 如果没有需要买入的股票，则直接返回
    if len(to_buy) == 0:
        return

    # 7. 计算每只股票应分配的资金（平均分配）
    total_value = context.portfolio.portfolio_value # 总资产
    average_value = total_value / len(to_buy)       # 每只股票分配金额

    # 8. 执行买入操作
    for stock in to_buy:
        # order_target_value 函数用于调整股票持仓至目标价值
        order_target_value(stock, average_value)
```

![](img/4b14b82b643e217b3cd99af540e6291d_44.png)

---

![](img/4b14b82b643e217b3cd99af540e6291d_46.png)

![](img/4b14b82b643e217b3cd99af540e6291d_48.png)

## 策略流程回顾与调试

![](img/4b14b82b643e217b3cd99af540e6291d_50.png)

![](img/4b14b82b643e217b3cd99af540e6291d_52.png)

现在，我们已经完成了策略的所有核心函数。让我们回顾一下整个策略的执行流程。

![](img/4b14b82b643e217b3cd99af540e6291d_54.png)

![](img/4b14b82b643e217b3cd99af540e6291d_56.png)

整个策略在每月调仓时执行以下步骤：
1.  调用 `get_stocks` 函数，基于财务指标（如市盈率PE、市净率PB等）计算并筛选出新一期的股票排名。
2.  调用 `get_holdings` 函数，获取账户当前的持仓情况。
3.  在 `rebalance` 函数中，对比新排名与旧持仓，计算出需要调整的股票。
4.  先卖出不再位于新排名中的持仓股票。
5.  再将可用资金平均买入新进入排名的股票。

![](img/4b14b82b643e217b3cd99af540e6291d_58.png)

![](img/4b14b82b643e217b3cd99af540e6291d_60.png)

![](img/4b14b82b643e217b3cd99af540e6291d_62.png)

代码编写完成后，运行时常会遇到错误。例如，在计算差集时，如果直接对列表使用减号 `-` 操作符，会导致类型错误。正确的做法是先将列表转换为集合 `set` 类型。

另一个常见错误是代码中的拼写错误或使用了中文标点符号。例如，在获取财务数据字段名时，需要确保其与数据源提供的字段名完全一致，且使用英文逗号分隔。

![](img/4b14b82b643e217b3cd99af540e6291d_64.png)

![](img/4b14b82b643e217b3cd99af540e6291d_66.png)

---

![](img/4b14b82b643e217b3cd99af540e6291d_68.png)

## 总结
本节课中我们一起学习了量化交易策略调仓环节的完整实现。我们掌握了三个关键部分：
1.  **获取持仓**：如何从交易上下文 `context` 中提取当前持有的股票。
2.  **计算买卖清单**：如何利用集合运算，高效地找出需要买入和卖出的股票。
3.  **执行交易**：如何使用 `order_target_percent` 和 `order_target_value` 等函数来下达卖出和买入指令。

![](img/4b14b82b643e217b3cd99af540e6291d_70.png)

![](img/4b14b82b643e217b3cd99af540e6291d_72.png)

通过这个过程，我们将之前学习的股票筛选排名逻辑，与实际交易操作连接起来，形成了一个完整的、可自动运行的量化交易策略原型。