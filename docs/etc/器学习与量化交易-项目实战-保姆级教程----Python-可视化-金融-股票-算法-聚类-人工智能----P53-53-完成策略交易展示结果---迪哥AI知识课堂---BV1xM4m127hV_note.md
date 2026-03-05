# 机器学习与量化交易：P53：完成策略交易展示结果

![](img/1119296d9102242aaff0ee6f04dff394_1.png)

在本节课中，我们将继续完成量化交易策略的核心部分——`rebalance`函数。我们将学习如何根据新的股票排名，计算需要买入和卖出的股票，并执行实际的交易操作。

![](img/1119296d9102242aaff0ee6f04dff394_3.png)

![](img/1119296d9102242aaff0ee6f04dff394_5.png)

![](img/1119296d9102242aaff0ee6f04dff394_7.png)

上一节我们介绍了如何获取并筛选出排名靠前的股票。本节中，我们来看看如何根据新的排名结果，与当前持仓进行比较，并执行调仓操作。

![](img/1119296d9102242aaff0ee6f04dff394_9.png)

## 获取当前持仓

![](img/1119296d9102242aaff0ee6f04dff394_11.png)

首先，我们需要一个函数来获取当前账户持有的所有股票。以下是实现该功能的步骤：

1.  从 `context` 对象中获取持仓信息 `context.portfolio.positions`。
2.  遍历所有持仓，筛选出持有数量大于0的股票。
3.  将这些股票的代码存入一个列表并返回。

![](img/1119296d9102242aaff0ee6f04dff394_13.png)

以下是获取当前持仓的代码：

```python
def get_holdings(context):
    # 初始化一个空列表，用于存放当前持有的股票代码
    holdings = []
    # 遍历账户中的所有持仓
    for position in context.portfolio.positions:
        # 获取该股票的持仓数量
        quantity = context.portfolio.positions[position].quantity
        # 如果持仓数量大于0，则视为当前持有
        if quantity > 0:
            # 将该股票代码添加到持仓列表中
            holdings.append(position)
    # 返回当前持仓的股票代码列表
    return holdings
```

![](img/1119296d9102242aaff0ee6f04dff394_15.png)

![](img/1119296d9102242aaff0ee6f04dff394_17.png)

## 计算交易清单

![](img/1119296d9102242aaff0ee6f04dff394_19.png)

获取了当前持仓和新的股票排名后，下一步是计算需要买入和卖出的股票清单。

以下是计算交易清单的逻辑：

1.  **需要买入的股票 (`to_buy`)**：在新排名股票中，但不在当前持仓里的股票。这可以通过集合的差集运算实现：`to_buy = set(new_stocks) - set(current_holdings)`。
2.  **需要卖出的股票 (`to_sell`)**：在当前持仓中，但不在新排名股票里的股票。计算方式为：`to_sell = set(current_holdings) - set(new_stocks)`。

## 执行交易操作

![](img/1119296d9102242aaff0ee6f04dff394_21.png)

![](img/1119296d9102242aaff0ee6f04dff394_23.png)

计算出交易清单后，就可以执行实际的买卖指令了。通常遵循“先卖后买”的原则，以确保有足够的资金进行买入。

![](img/1119296d9102242aaff0ee6f04dff394_25.png)

![](img/1119296d9102242aaff0ee6f04dff394_27.png)

以下是执行交易的步骤：

![](img/1119296d9102242aaff0ee6f04dff394_29.png)

![](img/1119296d9102242aaff0ee6f04dff394_31.png)

1.  **卖出操作**：遍历 `to_sell` 列表，对每只股票执行卖出指令，将其持仓比例调整为0%。
    ```python
    for stock in to_sell:
        order_target_percent(stock, 0)
    ```
2.  **买入操作**：在卖出完成后，计算可用于买入的总资金，并将其平均分配到 `to_buy` 列表中的每只股票上。
    ```python
    # 计算账户总资产
    total_value = context.portfolio.portfolio_value
    # 计算每只计划买入的股票应分配的资金（平均分配）
    average_allocation = total_value / len(to_buy)
    
    for stock in to_buy:
        # 执行买入指令，买入指定金额的股票
        order_target_value(stock, average_allocation)
    ```

![](img/1119296d9102242aaff0ee6f04dff394_33.png)

![](img/1119296d9102242aaff0ee6f04dff394_35.png)

## 整合与运行

![](img/1119296d9102242aaff0ee6f04dff394_37.png)

现在，我们将上述步骤整合到 `rebalance` 函数中，并处理一些边界情况（例如没有股票需要买入时）。

![](img/1119296d9102242aaff0ee6f04dff394_39.png)

![](img/1119296d9102242aaff0ee6f04dff394_41.png)

![](img/1119296d9102242aaff0ee6f04dff394_43.png)

以下是整合后的 `rebalance` 函数核心逻辑：

![](img/1119296d9102242aaff0ee6f04dff394_45.png)

![](img/1119296d9102242aaff0ee6f04dff394_47.png)

```python
def rebalance(context, data):
    # 1. 获取新的股票排名
    new_stocks = get_top_stocks(context, data)
    
    # 2. 获取当前持仓
    current_holdings = get_holdings(context)
    
    # 3. 计算交易清单（使用集合操作）
    to_buy = set(new_stocks) - set(current_holdings)
    to_sell = set(current_holdings) - set(new_stocks)
    
    # 4. 执行卖出操作
    for stock in to_sell:
        order_target_percent(stock, 0)
    
    # 5. 执行买入操作
    if len(to_buy) == 0:
        # 如果没有需要买入的股票，则直接返回
        return
    else:
        total_value = context.portfolio.portfolio_value
        average_allocation = total_value / len(to_buy)
        for stock in to_buy:
            order_target_value(stock, average_allocation)
```

![](img/1119296d9102242aaff0ee6f04dff394_49.png)

运行代码时可能会遇到一些错误，例如语法错误（中文标点）、数据类型错误（列表不能直接相减）等。我们需要仔细检查并修正这些错误，例如将列表转换为集合再进行差集运算，并确保所有标点符号为英文格式。

![](img/1119296d9102242aaff0ee6f04dff394_51.png)

![](img/1119296d9102242aaff0ee6f04dff394_53.png)

本节课中我们一起学习了量化交易策略的完整调仓流程：从获取持仓、计算交易清单到执行买卖操作。我们掌握了使用集合运算高效计算股票差异的方法，并实现了“先卖后买”的资金管理逻辑。通过调试和修正代码中的常见错误，我们完成了策略核心交易部分的构建。