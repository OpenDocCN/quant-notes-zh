# 量化交易教程：P53：完成策略交易展示结果

## 概述
在本节课中，我们将继续完成量化交易策略的核心函数 `rebalance`。我们将学习如何根据最新的股票排名，计算当前持仓与目标持仓之间的差异，并据此执行具体的买入和卖出操作，最终完成一个完整的自动化调仓流程。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_1.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_3.png)

上一节我们介绍了如何获取并筛选股票排名，本节中我们来看看如何根据这个排名进行实际的交易操作。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_5.png)

## 核心函数：`get_holdings`
首先，我们需要一个函数来获取当前账户的持仓信息。这个函数的作用是遍历账户的所有持仓，筛选出当前实际持有的股票列表。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_6.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_7.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_9.png)

以下是 `get_holdings` 函数的实现步骤：

```python
def get_holdings(context):
    # 初始化一个空列表，用于存放当前持有的股票
    holdings = []
    # 遍历账户的所有持仓
    for position in context.portfolio.positions.values():
        # 判断该持仓的股票数量是否大于0
        if position.quantity > 0:
            # 如果持有，则将该股票添加到列表中
            holdings.append(position.sid)
    # 返回当前持有的股票列表
    return holdings
```

## 核心函数：`rebalance`
`rebalance` 函数是策略的调仓核心。它首先获取最新的股票排名和当前持仓，然后计算需要买入和卖出的股票，最后执行交易指令。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_11.png)

### 1. 获取持仓与排名差异
在 `rebalance` 函数中，我们首先调用 `get_holdings` 获取当前持仓，然后与最新的目标股票列表 `target_stocks` 进行比较。

```python
def rebalance(context, data):
    # 获取最新的目标股票排名列表
    target_stocks = get_rank(context, data)
    # 获取当前账户的持仓列表
    holdings = get_holdings(context)
```

### 2. 计算买卖列表
接下来，我们通过集合运算来计算需要买入和卖出的股票列表。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_13.png)

*   **需要买入的股票 (`to_buy`)**: 存在于目标列表但不在当前持仓中的股票。
    *   公式：`to_buy = set(target_stocks) - set(holdings)`
*   **需要卖出的股票 (`to_sell`)**: 存在于当前持仓但不在目标列表中的股票。
    *   公式：`to_sell = set(holdings) - set(target_stocks)`

![](img/b548b0ad17fd64ac4b718951a43ae3d3_14.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_15.png)

以下是计算买卖列表的代码：

![](img/b548b0ad17fd64ac4b718951a43ae3d3_17.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_18.png)

```python
    # 将列表转换为集合，以便进行集合运算
    target_set = set(target_stocks)
    holdings_set = set(holdings)

    # 计算需要卖出的股票：当前有，但新排名里没有
    to_sell = holdings_set - target_set
    # 计算需要买入的股票：新排名里有，但当前没有
    to_buy = target_set - holdings_set
```

### 3. 执行卖出操作
在量化交易中，通常遵循“先卖后买”的原则，以确保有足够的资金进行买入。我们遍历需要卖出的股票列表，并将它们的持仓比例调整为0。

以下是执行卖出操作的代码：

![](img/b548b0ad17fd64ac4b718951a43ae3d3_20.png)

```python
    # 先执行卖出操作：将不在目标列表中的持仓清空
    for stock in to_sell:
        # order_target_percent 函数用于将某只股票的持仓调整到目标比例
        # 这里将比例设为0，即全部卖出
        order_target_percent(stock, 0)
```

### 4. 执行买入操作
卖出完成后，我们计算可用资金，并将其平均分配到需要买入的股票上。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_22.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_23.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_24.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_25.png)

以下是执行买入操作的步骤和代码：

![](img/b548b0ad17fd64ac4b718951a43ae3d3_26.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_28.png)

1.  **检查买入列表**：如果买入列表为空，则直接返回。
2.  **计算可用资金**：从 `context` 中获取当前投资组合的总价值。
3.  **计算每只股票应分配的资金比例**：将可用资金平均分配给每只待买入的股票。
4.  **执行买入订单**：为每只股票下达买入指令。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_30.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_32.png)

```python
    # 如果没有需要买入的股票，则直接返回
    if len(to_buy) == 0:
        return

    # 计算当前投资组合的总价值
    total_value = context.portfolio.portfolio_value
    # 计算平均每只待买入股票应分配的资金比例
    avg_weight = 1.0 / len(to_buy)

    # 执行买入操作：按平均比例买入目标股票
    for stock in to_buy:
        order_target_percent(stock, avg_weight)
```

![](img/b548b0ad17fd64ac4b718951a43ae3d3_34.png)

## 代码整合与调试
将以上所有部分整合到 `rebalance` 函数中，并设置好策略的运行周期（例如每月调仓），就完成了一个基础的量化交易策略框架。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_36.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_38.png)

在编写和整合代码时，初学者常会遇到一些语法错误，例如：
*   使用了中文标点符号（如中文逗号）。
*   变量名拼写错误。
*   函数调用参数缺失。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_39.png)

如果代码运行报错，需要仔细阅读错误信息，定位到具体的行号和错误类型，然后逐一检查并修正。例如，将 `fundamentals.` 后的中文逗号改为英文逗号。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_41.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_42.png)

## 总结
本节课中我们一起学习了如何完成一个量化交易策略的最终步骤——执行交易。我们掌握了三个关键点：
1.  **获取当前持仓**：通过遍历 `context.portfolio.positions` 来得到实际持有的股票列表。
2.  **计算调仓差异**：利用集合运算高效地找出需要买入和卖出的股票。
3.  **执行买卖订单**：使用 `order_target_percent` 函数，遵循“先卖后买”的原则，将资金平均分配到目标股票上，实现自动化调仓。

![](img/b548b0ad17fd64ac4b718951a43ae3d3_44.png)

![](img/b548b0ad17fd64ac4b718951a43ae3d3_45.png)

通过本课的学习，你已经能够将一个股票排名逻辑转化为可实际运行的交易策略。接下来，你可以在此基础上回测策略，分析其收益与风险，并进一步优化筛选指标和资金管理逻辑。