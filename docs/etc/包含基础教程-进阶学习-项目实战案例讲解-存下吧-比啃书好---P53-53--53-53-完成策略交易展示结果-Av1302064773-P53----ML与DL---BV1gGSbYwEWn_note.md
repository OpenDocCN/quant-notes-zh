# 量化交易教程：P53：完成策略交易展示结果

![](img/0525a40af67af43178eacca72640297a_1.png)

在本节课中，我们将继续完成策略的核心交易函数。我们将学习如何比较当前持仓股票与最新股票排名之间的差异，并基于此差异执行具体的买入和卖出操作，最终完成一个完整的调仓逻辑。

## 获取当前持仓股票

![](img/0525a40af67af43178eacca72640297a_3.png)

![](img/0525a40af67af43178eacca72640297a_5.png)

上一节我们介绍了如何获取最新的股票排名。本节中，我们来看看如何获取当前投资组合中实际持有的股票。

![](img/0525a40af67af43178eacca72640297a_7.png)

![](img/0525a40af67af43178eacca72640297a_9.png)

我们需要编写一个函数，从 `context` 对象中提取当前持仓的股票列表。以下是该函数的实现步骤：

![](img/0525a40af67af43178eacca72640297a_11.png)

*   首先，从 `context` 中获取持仓信息 `positions`。
*   然后，遍历 `positions` 列表。
*   对于每个持仓，检查其数量是否大于零。
*   如果持仓数量大于零，则将该股票代码添加到持仓列表 `holdings` 中。
*   最后，返回 `holdings` 列表。

```python
def get_holdings(context):
    holdings = []
    positions = context.portfolio.positions
    for position in positions:
        quantity = positions[position].quantity
        if quantity > 0:
            holdings.append(position)
    return holdings
```

## 计算买卖清单

![](img/0525a40af67af43178eacca72640297a_13.png)

现在，我们同时拥有了最新的股票排名 `stocks` 和当前持仓 `holdings`。接下来，我们需要计算需要买入和卖出的股票清单。

以下是计算逻辑：

*   **需要买入的股票**：存在于最新排名 `stocks` 中，但不在当前持仓 `holdings` 中的股票。公式为：`to_buy = set(stocks) - set(holdings)`。
*   **需要卖出的股票**：存在于当前持仓 `holdings` 中，但不在最新排名 `stocks` 中的股票。公式为：`to_sell = set(holdings) - set(stocks)`。

```python
# 获取最新排名和当前持仓
stocks = get_ranked_stocks(context)  # 假设这是获取排名的函数
holdings = get_holdings(context)

![](img/0525a40af67af43178eacca72640297a_15.png)

![](img/0525a40af67af43178eacca72640297a_17.png)

# 计算买卖清单
to_buy = set(stocks) - set(holdings)
to_sell = set(holdings) - set(stocks)
```

![](img/0525a40af67af43178eacca72640297a_19.png)

## 执行交易操作

计算出买卖清单后，我们就可以执行实际的交易指令了。通常的交易原则是“先卖后买”，以便释放资金用于新的投资。

![](img/0525a40af67af43178eacca72640297a_21.png)

![](img/0525a40af67af43178eacca72640297a_23.png)

以下是执行交易的步骤：

1.  **卖出操作**：遍历 `to_sell` 列表，对每只股票执行清仓操作，将其目标持仓比例设置为0%。
2.  **买入操作**：首先检查 `to_buy` 列表是否为空。如果为空，则直接返回。
3.  如果 `to_buy` 列表不为空，则计算可用于投资的现金总额。
4.  将现金总额平均分配给 `to_buy` 列表中的每只股票，得到每只股票的计划投资金额 `avg_value`。
5.  遍历 `to_buy` 列表，对每只股票执行买入操作，买入金额为 `avg_value`。

```python
# 1. 执行卖出操作
for stock in to_sell:
    order_target_percent(stock, 0)

# 2. 检查是否需要买入
if len(to_buy) == 0:
    return

![](img/0525a40af67af43178eacca72640297a_25.png)

# 3. 计算可用资金
total_cash = context.portfolio.portfolio_value

![](img/0525a40af67af43178eacca72640297a_27.png)

![](img/0525a40af67af43178eacca72640297a_29.png)

# 4. 计算每只股票平均分配金额
avg_value = total_cash / len(to_buy)

![](img/0525a40af67af43178eacca72640297a_31.png)

# 5. 执行买入操作
for stock in to_buy:
    order_target_value(stock, avg_value)
```

![](img/0525a40af67af43178eacca72640297a_33.png)

![](img/0525a40af67af43178eacca72640297a_35.png)

## 整合与运行策略

![](img/0525a40af67af43178eacca72640297a_37.png)

现在，我们将以上所有步骤整合到主调仓函数 `rebalance` 中。这个函数会在每个调仓周期（例如每月）被调用。

![](img/0525a40af67af43178eacca72640297a_39.png)

![](img/0525a40af67af43178eacca72640297a_41.png)

以下是整合后的代码框架和需要注意的细节：

![](img/0525a40af67af43178eacca72640297a_43.png)

*   在 `context` 中初始化股票池。
*   在 `rebalance` 函数中，依次调用获取排名、获取持仓、计算买卖清单和执行交易的函数。
*   注意处理数据查询函数（如 `get_fundamentals`）中的语法细节，例如使用英文逗号分隔查询字段，避免因格式错误导致运行失败。
*   运行策略时，需要设置明确的回测开始和结束日期。

![](img/0525a40af67af43178eacca72640297a_45.png)

![](img/0525a40af67af43178eacca72640297a_47.png)

```python
def initialize(context):
    # 初始化，设置股票池等
    pass

![](img/0525a40af67af43178eacca72640297a_49.png)

![](img/0525a40af67af43178eacca72640297a_51.png)

def rebalance(context):
    # 主调仓逻辑
    # 1. 获取最新股票排名
    stocks = get_ranked_stocks(context)
    # 2. 获取当前持仓
    holdings = get_holdings(context)
    # 3. 计算买卖清单
    to_buy = set(stocks) - set(holdings)
    to_sell = set(holdings) - set(stocks)
    # 4. 执行交易
    # ... (卖出和买入操作)
```

![](img/0525a40af67af43178eacca72640297a_53.png)

![](img/0525a40af67af43178eacca72640297a_55.png)

初次运行策略时可能会遇到错误，常见的包括列表与集合的运算错误、API函数调用参数格式错误等。根据错误提示逐一检查并修正代码即可，例如确保在计算集合差集前将列表转换为集合。

![](img/0525a40af67af43178eacca72640297a_57.png)

## 总结

![](img/0525a40af67af43178eacca72640297a_59.png)

本节课中我们一起学习了如何完成一个量化交易策略的最终交易环节。我们掌握了三个核心部分：
1.  编写函数获取当前投资组合的持仓股票列表。
2.  通过集合运算，比较持仓与最新股票排名的差异，生成具体的买入和卖出清单。
3.  使用 `order_target_percent` 和 `order_target_value` 等函数执行卖出和买入操作，并整合所有步骤到月度调仓函数中。

![](img/0525a40af67af43178eacca72640297a_61.png)

至此，一个基于基本面指标排名进行定期调仓的完整交易策略就构建完成了。下一节，我们将运行这个策略并分析其回测结果。