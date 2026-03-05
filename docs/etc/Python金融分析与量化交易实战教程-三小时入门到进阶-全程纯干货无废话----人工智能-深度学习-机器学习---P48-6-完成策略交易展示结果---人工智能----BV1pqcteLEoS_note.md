# Python金融量化交易实战：P48：6-完成策略交易展示结果 🚀

![](img/a246808927dbe3e79411797cea5d9796_1.png)

在本节课中，我们将继续完成REBALANCE函数，实现基于股票排名差异的自动化交易逻辑。我们将学习如何计算持仓与最新排名的差异，并据此执行买入和卖出操作。

![](img/a246808927dbe3e79411797cea5d9796_3.png)

上一节我们介绍了如何获取并筛选股票排名，本节中我们来看看如何根据排名变化进行实际的交易操作。

![](img/a246808927dbe3e79411797cea5d9796_5.png)

## 计算持仓股票列表

首先，我们需要一个函数来获取当前投资组合中持有的所有股票。以下是实现步骤：

1.  从 `context` 对象中获取持仓信息 `positions`。
2.  遍历所有持仓，筛选出持有数量大于0的股票。
3.  将这些股票的代码存入一个列表并返回。

以下是获取持仓股票列表的代码：

![](img/a246808927dbe3e79411797cea5d9796_7.png)

```python
def get_holdings(context):
    # 初始化一个空列表，用于存放当前持有的股票代码
    holdings = []
    # 遍历context中的持仓信息
    for position in context.portfolio.positions:
        # 获取该股票的持仓数量
        quantity = context.portfolio.positions[position].quantity
        # 如果持仓数量大于0，则视为当前持有
        if quantity > 0:
            holdings.append(position)
    # 返回当前持有的股票列表
    return holdings
```

## 计算交易信号（买入/卖出列表）

![](img/a246808927dbe3e79411797cea5d9796_9.png)

获取了持仓列表和最新的股票排名后，下一步是计算需要进行的交易操作。核心思想是比较这两个列表的差异。

![](img/a246808927dbe3e79411797cea5d9796_11.png)

以下是计算交易信号的逻辑：

1.  **计算买入列表 (`to_buy`)**：存在于最新排名中但不在当前持仓中的股票。这代表我们需要新购入的股票。公式为：`to_buy = set(new_stocks) - set(holdings)`。
2.  **计算卖出列表 (`to_sell`)**：存在于当前持仓中但不在最新排名中的股票。这代表我们需要卖出的股票。公式为：`to_sell = set(holdings) - set(new_stocks)`。

使用集合（`set`）操作可以方便地计算这些差异。

## 执行交易订单

计算出 `to_buy` 和 `to_sell` 列表后，就可以执行具体的交易指令了。通常的交易逻辑是“先卖后买”，以释放资金用于新的投资。

![](img/a246808927dbe3e79411797cea5d9796_13.png)

以下是执行交易的步骤：

![](img/a246808927dbe3e79411797cea5d9796_15.png)

1.  **卖出操作**：遍历 `to_sell` 列表，对每只股票执行清仓操作，即将其目标持仓比例设置为0%。
    ```python
    for stock in to_sell:
        order_target_percent(stock, 0)
    ```
2.  **买入操作**：在卖出完成后，遍历 `to_buy` 列表。通常采用平均分配资金的方式买入新股票。
    *   首先，计算可用于投资的总资金 `total_cash`。
    *   然后，计算每只计划买入的股票应分配的资金 `cash_per_stock = total_cash / len(to_buy)`。
    *   最后，对每只股票执行买入订单，买入指定金额。
    ```python
    if len(to_buy) > 0:
        total_cash = context.portfolio.cash
        cash_per_stock = total_cash / len(to_buy)
        for stock in to_buy:
            order_value(stock, cash_per_stock)
    ```

![](img/a246808927dbe3e79411797cea5d9796_17.png)

![](img/a246808927dbe3e79411797cea5d9796_19.png)

## 代码整合与运行调试

![](img/a246808927dbe3e79411797cea5d9796_21.png)

现在，我们将以上各部分整合到主函数 `rebalance` 中，并处理可能出现的错误。

![](img/a246808927dbe3e79411797cea5d9796_23.png)

![](img/a246808927dbe3e79411797cea5d9796_25.png)

以下是整合后的 `rebalance` 函数框架和注意事项：

![](img/a246808927dbe3e79411797cea5d9796_27.png)

![](img/a246808927dbe3e79411797cea5d9796_29.png)

```python
def rebalance(context):
    # 1. 获取最新股票排名
    new_stocks = get_top_stocks(context)
    # 2. 获取当前持仓
    holdings = get_holdings(context)
    # 3. 计算交易信号（使用集合避免重复并方便计算差集）
    to_buy = list(set(new_stocks) - set(holdings))
    to_sell = list(set(holdings) - set(new_stocks))

    # 4. 执行卖出订单
    for stock in to_sell:
        order_target_percent(stock, 0)

    # 5. 执行买入订单
    if len(to_buy) > 0:
        total_cash = context.portfolio.cash
        cash_per_stock = total_cash / len(to_buy)
        for stock in to_buy:
            order_value(stock, cash_per_stock)
```

**常见错误与调试**：
*   **无效字符错误**：检查代码中的逗号、括号等是否为英文标点。中文标点会导致语法错误。
*   **类型错误**：确保在进行列表差异计算时，先将其转换为集合（`set`）类型。
*   **函数调用错误**：确认 `order_target_percent`、`order_value` 等交易函数的参数传递正确。

![](img/a246808927dbe3e79411797cea5d9796_31.png)

![](img/a246808927dbe3e79411797cea5d9796_33.png)

运行代码后，如果遇到报错，请根据错误信息提示，仔细检查相关行的代码书写是否正确。

![](img/a246808927dbe3e79411797cea5d9796_35.png)

![](img/a246808927dbe3e79411797cea5d9796_37.png)

## 策略逻辑回顾

![](img/a246808927dbe3e79411797cea5d9796_39.png)

![](img/a246808927dbe3e79411797cea5d9796_41.png)

让我们回顾一下整个策略的完整流程：

![](img/a246808927dbe3e79411797cea5d9796_43.png)

1.  **初始化**：在 `initialize` 函数中设定股票池、初始排名，并安排每月调仓。
2.  **调仓执行**：每月执行的 `rebalance` 函数是核心。
3.  **数据获取与排名**：在调仓日，通过 `get_top_stocks` 函数获取最新的财务指标（如市盈率PE、市净率PB），计算综合得分并筛选出排名靠前的股票。
4.  **持仓比对**：调用 `get_holdings` 函数获取当前账户的实际持仓列表。
5.  **生成交易指令**：通过集合运算，比较最新排名与当前持仓，生成需要买入 (`to_buy`) 和卖出 (`to_sell`) 的股票列表。
6.  **执行交易**：先卖出不再符合排名条件的股票，再将资金平均买入新进入排名列表的股票。

![](img/a246808927dbe3e79411797cea5d9796_45.png)

本节课中我们一起学习了如何完成一个量化交易策略的最后一个关键环节——交易执行。我们实现了获取持仓、计算交易信号以及执行买入卖出订单的完整功能，并将它们整合到一个自动化的调仓流程中。通过不断的调试和优化，你的策略就可以在回测或实盘环境中运行了。