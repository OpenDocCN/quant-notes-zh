# 人工智能数据挖掘实战：P53：完成策略交易展示结果

在本节课中，我们将完成交易策略的核心函数 `rebalance`。我们将学习如何根据新的股票排名结果，计算需要买入和卖出的股票，并执行相应的交易操作。整个过程将涉及持仓管理、资金分配和订单执行。

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_1.png)

## 计算当前持仓

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_3.png)

上一节我们介绍了如何获取新的股票排名。本节中，我们来看看如何获取我们当前持有的股票，以便后续进行对比。

我们需要编写一个函数 `get_holdings`，用于从上下文信息中提取当前持仓的股票列表。

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_5.png)

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_6.png)

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_7.png)

以下是 `get_holdings` 函数的实现步骤：

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_9.png)

1.  从 `context` 中获取持仓信息 `positions`。
2.  遍历 `positions`，检查每只股票的持仓数量。
3.  如果持仓数量大于0，则将该股票代码加入持仓列表。
4.  返回最终的持仓列表。

```python
def get_holdings(context):
    # 初始化持仓列表
    holdings = []
    # 获取当前所有持仓
    positions = context.portfolio.positions
    # 遍历持仓
    for position in positions:
        # 获取该股票的持仓数量
        amount = context.portfolio.positions[position].amount
        # 如果持仓数量大于0，则视为有效持仓
        if amount > 0:
            holdings.append(position)
    # 返回持仓股票列表
    return holdings
```

## 执行调仓操作

获取了当前持仓和新股票排名后，我们就可以计算买卖清单并执行交易了。本节我们将完成 `rebalance` 函数的核心逻辑。

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_11.png)

首先，我们调用 `get_holdings` 函数获取当前持仓列表 `holdings`。

```python
holdings = get_holdings(context)
```

接下来，我们需要计算买卖清单。核心思路是利用集合运算找出差异。

*   **待买入股票**：在新排名 `stocks` 中，但不在当前持仓 `holdings` 中的股票。
*   **待卖出股票**：在当前持仓 `holdings` 中，但不在新排名 `stocks` 中的股票。

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_13.png)

我们使用集合的差集运算来实现：

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_14.png)

```python
# 将列表转换为集合，以便进行集合运算
stocks_set = set(stocks)
holdings_set = set(holdings)

# 计算待买入和待卖出的股票
to_buy = list(stocks_set - holdings_set)
to_sell = list(holdings_set - stocks_set)
```

## 执行卖出与买入订单

计算出买卖清单后，我们需要执行具体的交易。通常的策略是先卖出，再买入。

以下是执行卖出操作的步骤。我们遍历 `to_sell` 列表，对每只股票执行清仓操作。

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_16.png)

```python
# 执行卖出操作
for stock in to_sell:
    # order_target_percent 函数用于将股票持仓调整到目标比例
    # 将比例设为0，即全部卖出
    order_target_percent(stock, 0)
```

在执行买入操作前，需要先检查是否有股票需要买入。如果没有，则直接返回。

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_18.png)

```python
# 如果没有股票需要买入，则直接返回
if len(to_buy) == 0:
    return
```

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_19.png)

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_20.png)

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_21.png)

如果需要买入，我们需要计算可用于投资的资金，并平均分配到每只待买入的股票上。

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_22.png)

```python
# 获取当前投资组合的总价值
total_value = context.portfolio.portfolio_value
# 计算每只待买入股票应分配的资金比例（平均分配）
weight = 1.0 / len(to_buy)
```

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_24.png)

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_26.png)

最后，我们遍历 `to_buy` 列表，为每只股票执行买入操作。

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_28.png)

```python
# 执行买入操作
for stock in to_buy:
    # 按计算出的比例买入股票
    order_target_percent(stock, weight)
```

## 代码整合与运行调试

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_30.png)

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_32.png)

现在，我们将所有代码片段整合到 `rebalance` 函数中，并处理可能出现的错误。

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_34.png)

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_35.png)

一个常见的错误是列表与集合的运算问题，我们已经通过转换为集合解决。另外，在获取财务数据时，需要注意字段名的拼写和标点符号（必须使用英文逗号）。

整合后的 `rebalance` 函数结构如下：

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_37.png)

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_38.png)

```python
def rebalance(context):
    # 1. 获取新的股票排名 (假设由其他函数生成，存储在 `stocks` 变量中)
    # stocks = get_ranked_stocks(context)

    # 2. 获取当前持仓
    holdings = get_holdings(context)

    # 3. 计算买卖清单
    stocks_set = set(stocks)
    holdings_set = set(holdings)
    to_buy = list(stocks_set - holdings_set)
    to_sell = list(holdings_set - stocks_set)

    # 4. 执行卖出操作
    for stock in to_sell:
        order_target_percent(stock, 0)

    # 5. 执行买入操作
    if len(to_buy) == 0:
        return
    total_value = context.portfolio.portfolio_value
    weight = 1.0 / len(to_buy)
    for stock in to_buy:
        order_target_percent(stock, weight)
```

运行代码时，如果遇到报错，需要根据错误信息仔细检查：
1.  变量名拼写是否正确。
2.  函数调用参数是否正确。
3.  标点符号（特别是逗号）是否为英文格式。
4.  数据字段名是否与数据源一致。

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_40.png)

![](img/2a3030ecc84fd8a8b2e2c5f044fa9000_41.png)

本节课中我们一起学习了如何完成一个量化交易策略的调仓函数。我们掌握了获取当前持仓、通过集合运算计算买卖差异、以及执行卖出和买入订单的完整流程。这个过程是策略从信号生成到实际交易执行的关键环节。记住，在实盘前，充分的回测和调试是必不可少的步骤。