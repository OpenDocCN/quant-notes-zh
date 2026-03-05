# Python金融量化分析：P53：完成策略交易展示结果 📈

![](img/17a4738b9c94e0b1cc561ed6d455d27a_1.png)

在本节课中，我们将完成一个量化交易策略的核心部分：根据新的股票排名结果，计算需要买入和卖出的股票，并执行相应的交易操作。我们将学习如何获取当前持仓、计算持仓与目标持仓的差异，并据此进行调仓。

![](img/17a4738b9c94e0b1cc561ed6d455d27a_3.png)

## 概述

![](img/17a4738b9c94e0b1cc561ed6d455d27a_5.png)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_7.png)

上一节我们介绍了如何获取并筛选股票排名。本节中，我们将基于这个排名结果，实现一个完整的调仓逻辑。核心任务是：找出当前持仓与目标持仓（即新排名）之间的差异，然后卖出不再需要的股票，并买入新的目标股票。

![](img/17a4738b9c94e0b1cc561ed6d455d27a_9.png)

## 获取当前持仓

![](img/17a4738b9c94e0b1cc561ed6d455d27a_11.png)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_13.png)

首先，我们需要一个函数来获取当前投资组合中持有的所有股票。以下是实现此功能的步骤：

1.  从 `context` 对象中获取持仓信息。
2.  遍历所有持仓，筛选出持仓数量大于零的股票。
3.  将这些股票的代码存入一个列表并返回。

以下是获取当前持仓的代码实现：

![](img/17a4738b9c94e0b1cc561ed6d455d27a_15.png)

```python
def get_holdings(context):
    # 初始化一个空列表，用于存放当前持有的股票代码
    holdings = []
    # 从context中获取所有持仓信息
    positions = context.portfolio.positions
    # 遍历每个持仓
    for position in positions:
        # 获取该股票的持仓数量
        quantity = context.portfolio.positions[position].quantity
        # 如果持仓数量大于0，则视为当前持有
        if quantity > 0:
            # 将该股票代码添加到持有列表中
            holdings.append(position)
    # 返回当前持有的股票列表
    return holdings
```

![](img/17a4738b9c94e0b1cc561ed6d455d27a_17.png)

## 计算交易清单

获取了当前持仓 `holdings` 和新的目标股票列表 `stocks` 后，下一步是计算需要买入和卖出的股票清单。

![](img/17a4738b9c94e0b1cc561ed6d455d27a_19.png)

以下是计算交易清单的逻辑：

![](img/17a4738b9c94e0b1cc561ed6d455d27a_21.png)

1.  **需要买入的股票 (`to_buy`)**：存在于新排名 `stocks` 中，但不在当前持仓 `holdings` 里的股票。公式为：`to_buy = stocks - holdings`。
2.  **需要卖出的股票 (`to_sell`)**：存在于当前持仓 `holdings` 中，但不在新排名 `stocks` 里的股票。公式为：`to_sell = holdings - stocks`。

![](img/17a4738b9c94e0b1cc561ed6d455d27a_23.png)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_25.png)

在Python中，我们可以使用集合（`set`）的差集运算来高效地完成这个计算。

![](img/17a4738b9c94e0b1cc561ed6d455d27a_27.png)

```python
# 将列表转换为集合，以便进行集合运算
holdings_set = set(holdings)
stocks_set = set(stocks)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_29.png)

# 计算需要买入的股票：在目标集合中但不在持仓集合中
to_buy = list(stocks_set - holdings_set)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_31.png)

# 计算需要卖出的股票：在持仓集合中但不在目标集合中
to_sell = list(holdings_set - stocks_set)
```

## 执行交易操作

![](img/17a4738b9c94e0b1cc561ed6d455d27a_33.png)

计算出 `to_sell` 和 `to_buy` 列表后，我们就可以执行实际的交易了。通常的策略是先卖出，释放资金，然后再买入。

以下是执行交易的步骤：

![](img/17a4738b9c94e0b1cc561ed6d455d27a_35.png)

1.  **卖出操作**：遍历 `to_sell` 列表，对每只股票执行卖出指令，将其持仓比例调整为0%。
2.  **买入操作**：首先检查 `to_buy` 列表是否为空。如果不为空，则计算可用资金，并平均分配到每只待买入的股票上，然后执行买入指令。

![](img/17a4738b9c94e0b1cc561ed6d455d27a_37.png)

```python
# 1. 执行卖出操作
for stock in to_sell:
    # order_target_percent 函数用于将某只股票的持仓调整到目标百分比
    # 这里将目标百分比设为0，即全部卖出
    order_target_percent(stock, 0)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_39.png)

# 2. 执行买入操作
if len(to_buy) == 0:
    # 如果没有需要买入的股票，则直接返回
    return
else:
    # 计算当前投资组合的总价值
    total_value = context.portfolio.portfolio_value
    # 计算平均每只股票应分配的资金比例
    avg_weight = 1.0 / len(to_buy)
    # 遍历需要买入的股票列表
    for stock in to_buy:
        # 对每只股票执行买入指令，买入金额占总资金的比例为 avg_weight
        order_target_percent(stock, avg_weight)
```

![](img/17a4738b9c94e0b1cc561ed6d455d27a_41.png)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_43.png)

## 整合与运行策略

![](img/17a4738b9c94e0b1cc561ed6d455d27a_45.png)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_47.png)

现在，我们将上述所有步骤整合到主调仓函数 `rebalance` 中，并尝试运行整个策略。

![](img/17a4738b9c94e0b1cc561ed6d455d27a_49.png)

以下是整合后的代码框架和运行示例：

![](img/17a4738b9c94e0b1cc561ed6d455d27a_51.png)

```python
def rebalance(context):
    # 步骤1：获取新的股票排名
    stocks = get_stocks(context)
    # 步骤2：获取当前持仓
    holdings = get_holdings(context)
    # 步骤3：计算交易清单
    holdings_set = set(holdings)
    stocks_set = set(stocks)
    to_buy = list(stocks_set - holdings_set)
    to_sell = list(holdings_set - stocks_set)
    # 步骤4：执行交易
    for stock in to_sell:
        order_target_percent(stock, 0)
    if len(to_buy) > 0:
        avg_weight = 1.0 / len(to_buy)
        for stock in to_buy:
            order_target_percent(stock, avg_weight)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_53.png)

# 设置回测起止时间
start_date = ‘2016-01-04’
end_date = ‘2016-01-10’
# 运行回测
run_backtest(start_date, end_date, rebalance)
```

![](img/17a4738b9c94e0b1cc561ed6d455d27a_55.png)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_57.png)

在初次运行策略时，很可能会遇到一些错误，例如语法错误（中文标点）、数据类型错误（列表不能直接相减）或函数调用错误。我们需要仔细检查错误信息，逐一修正。

![](img/17a4738b9c94e0b1cc561ed6d455d27a_59.png)

![](img/17a4738b9c94e0b1cc561ed6d455d27a_61.png)

**常见错误与修正：**
*   **无效字符错误**：检查代码中是否误用了中文逗号、括号等，应全部改为英文标点。
*   **类型错误**：如 `‘list’ 对象不支持减法`，需将列表转换为集合（`set`）再进行差集运算。
*   **函数未定义**：确保所有自定义函数（如 `get_stocks`, `get_holdings`）都已正确定义。

![](img/17a4738b9c94e0b1cc561ed6d455d27a_63.png)

修正所有错误后，再次运行策略，直到能够成功执行并输出回测结果。

![](img/17a4738b9c94e0b1cc561ed6d455d27a_65.png)

## 总结

![](img/17a4738b9c94e0b1cc561ed6d455d27a_67.png)

本节课中我们一起学习了量化交易策略调仓环节的完整实现。我们首先编写了 `get_holdings` 函数来获取当前持仓，然后利用集合运算高效地计算出了需要买入 (`to_buy`) 和卖出 (`to_sell`) 的股票清单，最后通过 `order_target_percent` 函数执行了先卖后买的交易操作。这个过程是许多量化策略的核心，理解并掌握它对于构建更复杂的交易系统至关重要。在实践过程中，遇到报错是正常的，关键在于学会阅读错误信息并耐心调试。