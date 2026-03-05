# Python金融分析与量化交易实战课程：P48：6-完成策略交易展示结果 🚀

![](img/4e7fddb08319066276df1a3d31616179_1.png)

在本节课中，我们将完成REBON函数，实现基于股票排名差异的自动化调仓逻辑。我们将学习如何获取当前持仓、计算买卖列表，并执行具体的交易操作。

![](img/4e7fddb08319066276df1a3d31616179_3.png)

![](img/4e7fddb08319066276df1a3d31616179_5.png)

上一节我们介绍了如何筛选和排名股票，本节中我们来看看如何根据排名变化进行实际的买卖操作。

## 获取当前持仓

首先，我们需要一个函数来获取当前投资组合中持有的所有股票。以下是实现此功能的步骤：

以下是获取当前持仓股票的代码逻辑：

![](img/4e7fddb08319066276df1a3d31616179_7.png)

```python
def get_holdings(context):
    """
    获取当前持仓的股票列表。
    :param context: 策略上下文对象，包含账户和持仓信息。
    :return: 当前持有的股票代码列表。
    """
    holdings = []
    # 从context中获取所有持仓头寸
    positions = context.portfolio.positions
    for position in positions:
        # 检查该股票的持仓数量是否大于0
        if positions[position].quantity > 0:
            # 如果持有，则将其添加到持仓列表中
            holdings.append(position)
    return holdings
```

## 计算买卖列表

![](img/4e7fddb08319066276df1a3d31616179_9.png)

获取当前持仓后，我们需要将其与新的股票排名进行比较，以确定需要买入和卖出的股票。

![](img/4e7fddb08319066276df1a3d31616179_11.png)

以下是计算买卖列表的逻辑：

1.  **计算买入列表**：新的股票排名中，剔除当前已持有的股票，剩下的就是需要买入的股票。公式可以表示为：`to_buy = set(new_stocks) - set(current_holdings)`。
2.  **计算卖出列表**：当前持有的股票中，剔除那些仍然在新的股票排名中的股票，剩下的就是需要卖出的股票。公式可以表示为：`to_sell = set(current_holdings) - set(new_stocks)`。

```python
# 假设 new_stocks 是新的股票排名列表
new_stocks = get_new_stock_rank(context)
# 获取当前持仓
current_holdings = get_holdings(context)

# 计算买卖列表
to_buy = list(set(new_stocks) - set(current_holdings))
to_sell = list(set(current_holdings) - set(new_stocks))
```

![](img/4e7fddb08319066276df1a3d31616179_13.png)

## 执行交易操作

![](img/4e7fddb08319066276df1a3d31616179_15.png)

计算出买卖列表后，接下来就是执行具体的交易指令。通常遵循“先卖后买”的原则，以确保有足够的资金进行买入。

![](img/4e7fddb08319066276df1a3d31616179_17.png)

![](img/4e7fddb08319066276df1a3d31616179_19.png)

以下是执行交易的步骤：

![](img/4e7fddb08319066276df1a3d31616179_21.png)

![](img/4e7fddb08319066276df1a3d31616179_23.png)

1.  **执行卖出操作**：遍历卖出列表，将每只股票的持仓比例调整为0%，即全部卖出。
2.  **执行买入操作**：首先检查买入列表是否为空。如果不为空，则计算可用资金，并平均分配到每只要买入的股票上，然后执行买入。

![](img/4e7fddb08319066276df1a3d31616179_25.png)

```python
# 执行卖出操作
for stock in to_sell:
    # order_target_percent 函数用于将某只股票的持仓调整到目标比例
    order_target_percent(stock, 0)

![](img/4e7fddb08319066276df1a3d31616179_27.png)

![](img/4e7fddb08319066276df1a3d31616179_29.png)

# 执行买入操作
if len(to_buy) == 0:
    # 如果没有股票需要买入，则直接返回
    return
else:
    # 计算可用总资产
    total_value = context.portfolio.total_value
    # 计算每只股票平均分配到的资金
    average_value = total_value / len(to_buy)
    # 执行买入操作
    for stock in to_buy:
        # order_target_value 函数用于买入一定价值的股票
        order_target_value(stock, average_value)
```

![](img/4e7fddb08319066276df1a3d31616179_31.png)

## 代码整合与调试

![](img/4e7fddb08319066276df1a3d31616179_33.png)

![](img/4e7fddb08319066276df1a3d31616179_35.png)

![](img/4e7fddb08319066276df1a3d31616179_37.png)

将上述所有步骤整合到REBON函数中，并运行代码。初次运行时可能会遇到一些错误，例如语法错误（如使用了中文标点）或类型错误（如对列表直接进行集合运算）。

![](img/4e7fddb08319066276df1a3d31616179_39.png)

![](img/4e7fddb08319066276df1a3d31616179_41.png)

以下是常见的调试步骤：

![](img/4e7fddb08319066276df1a3d31616179_43.png)

*   **检查语法**：确保所有逗号、括号都是英文格式。
*   **类型转换**：在对列表进行集合运算（如求差集）前，确保将其转换为`set`类型。
*   **检查函数调用**：确认所有调用的API函数（如`order_target_percent`）名称和参数正确。

![](img/4e7fddb08319066276df1a3d31616179_45.png)

本节课中我们一起学习了如何完成一个量化交易策略的核心调仓功能。我们掌握了获取持仓、根据排名差异计算买卖列表，以及执行具体交易操作的方法。通过调试和完善代码，我们能够构建一个自动响应市场变化的交易系统。