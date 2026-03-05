# Python金融分析量化交易：P53：6-完成策略交易展示结果 📈

![](img/181ecefd48f986a9f593550eeb3c2441_1.png)

![](img/181ecefd48f986a9f593550eeb3c2441_3.png)

在本节课中，我们将完成量化交易策略的核心部分：根据股票排名结果进行实际的买卖操作。我们将编写一个函数来比较当前持仓与最新排名，并据此执行调仓。

![](img/181ecefd48f986a9f593550eeb3c2441_5.png)

![](img/181ecefd48f986a9f593550eeb3c2441_7.png)

![](img/181ecefd48f986a9f593550eeb3c2441_9.png)

上一节我们介绍了如何获取并筛选股票排名，本节中我们来看看如何将排名结果转化为具体的交易指令。

![](img/181ecefd48f986a9f593550eeb3c2441_11.png)

![](img/181ecefd48f986a9f593550eeb3c2441_13.png)

## 概述
我们将实现一个名为 `rebalance` 的函数。该函数的核心逻辑是：首先获取当前持仓的股票列表，然后将其与最新一轮的股票排名结果进行比较。根据差异，决定需要卖出哪些股票，以及买入哪些新股票。

## 获取当前持仓
首先，我们需要一个函数来获取当前投资组合中持有的所有股票。

![](img/181ecefd48f986a9f593550eeb3c2441_15.png)

以下是获取当前持仓股票的步骤：

![](img/181ecefd48f986a9f593550eeb3c2441_17.png)

![](img/181ecefd48f986a9f593550eeb3c2441_19.png)

1.  从 `context` 对象中获取持仓信息 `context.portfolio.positions`。
2.  遍历所有持仓，检查其数量是否大于零。
3.  将所有数量大于零的股票代码加入一个列表并返回。

![](img/181ecefd48f986a9f593550eeb3c2441_21.png)

![](img/181ecefd48f986a9f593550eeb3c2441_23.png)

对应的代码实现如下：
```python
def get_holdings(context):
    holdings = []
    for position in context.portfolio.positions:
        # 检查该持仓的股票数量是否大于0
        if context.portfolio.positions[position].quantity > 0:
            holdings.append(position)
    return holdings
```

![](img/181ecefd48f986a9f593550eeb3c2441_25.png)

![](img/181ecefd48f986a9f593550eeb3c2441_27.png)

## 计算买卖清单
在 `rebalance` 函数中，我们首先调用 `get_holdings` 函数获取当前持仓列表 `holdings`，并已有最新的股票排名列表 `stocks`。

![](img/181ecefd48f986a9f593550eeb3c2441_29.png)

接下来，我们基于这两个列表的差异来计算需要买卖的股票。

![](img/181ecefd48f986a9f593550eeb3c2441_31.png)

以下是计算买卖清单的逻辑：

![](img/181ecefd48f986a9f593550eeb3c2441_33.png)

*   **需要买入的股票 (`to_buy`)**：出现在最新排名 `stocks` 中，但不在当前持仓 `holdings` 里的股票。这代表我们认为有潜力的新股票。
    *   计算公式：`to_buy = set(stocks) - set(holdings)`
*   **需要卖出的股票 (`to_sell`)**：出现在当前持仓 `holdings` 中，但不在最新排名 `stocks` 里的股票。这代表我们认为不再具有优势的旧股票。
    *   计算公式：`to_sell = set(holdings) - set(stocks)`

![](img/181ecefd48f986a9f593550eeb3c2441_35.png)

使用集合（`set`）操作可以方便地计算这些差异。

![](img/181ecefd48f986a9f593550eeb3c2441_37.png)

![](img/181ecefd48f986a9f593550eeb3c2441_39.png)

## 执行交易操作
计算出买卖清单后，我们需要将其转化为实际的交易指令。通常的策略是先卖出，再买入，以确保有足够的资金。

![](img/181ecefd48f986a9f593550eeb3c2441_41.png)

![](img/181ecefd48f986a9f593550eeb3c2441_43.png)

![](img/181ecefd48f986a9f593550eeb3c2441_45.png)

以下是执行交易的具体步骤：

![](img/181ecefd48f986a9f593550eeb3c2441_47.png)

![](img/181ecefd48f986a9f593550eeb3c2441_49.png)

![](img/181ecefd48f986a9f593550eeb3c2441_51.png)

1.  **卖出操作**：遍历 `to_sell` 列表，对每只股票执行卖出指令，将其持仓比例或价值调整为0。
    ```python
    for stock in to_sell:
        # 将指定股票的持仓价值调整为0，即全部卖出
        order_target_value(stock, 0)
        # 或者使用 order_target_percent(stock, 0)
    ```
2.  **买入操作**：在卖出完成后，遍历 `to_buy` 列表。常见的策略是将可用资金平均分配给每只要买入的股票。
    ```python
    if len(to_buy) > 0: # 如果有股票需要买入
        # 计算可用于投资的现金
        cash = context.portfolio.cash
        # 计算每只股票应分配的平均资金
        average_cash = cash / len(to_buy)
        for stock in to_buy:
            # 用平均资金买入每只股票
            order_target_value(stock, average_cash)
    ```

![](img/181ecefd48f986a9f593550eeb3c2441_53.png)

## 整合与运行
现在，我们将上述所有步骤整合到 `rebalance` 函数中，并在每月调仓时调用它。

![](img/181ecefd48f986a9f593550eeb3c2441_55.png)

![](img/181ecefd48f986a9f593550eeb3c2441_57.png)

![](img/181ecefd48f986a9f593550eeb3c2441_59.png)

以下是完整的 `rebalance` 函数框架：
```python
def rebalance(context, data):
    # 1. 获取最新股票排名 (假设已由其他函数计算并存储在变量中)
    stocks = get_ranked_stocks(context, data)

    # 2. 获取当前持仓
    holdings = get_holdings(context)

    # 3. 计算买卖差异
    to_sell = set(holdings) - set(stocks)
    to_buy = set(stocks) - set(holdings)

    # 4. 执行卖出操作
    for stock in to_sell:
        order_target_value(stock, 0)

    # 5. 执行买入操作
    if len(to_buy) > 0:
        cash = context.portfolio.cash
        average_cash = cash / len(to_buy)
        for stock in to_buy:
            order_target_value(stock, average_cash)
```

![](img/181ecefd48f986a9f593550eeb3c2441_61.png)

![](img/181ecefd48f986a9f593550eeb3c2441_63.png)

编写完成后，运行代码进行测试。初学者常会遇到语法错误（如中文标点）或类型错误（如对列表直接进行集合运算）。需要仔细检查并修正这些错误，例如确保所有标点为英文，并将列表转换为集合后再进行计算。

![](img/181ecefd48f986a9f593550eeb3c2441_65.png)

![](img/181ecefd48f986a9f593550eeb3c2441_67.png)

## 总结
本节课中我们一起学习了如何完成一个量化交易策略的调仓逻辑。我们实现了三个关键部分：获取当前持仓、根据排名差异计算买卖清单、以及执行具体的买卖交易指令。这个过程是将策略思想转化为实际可执行代码的核心步骤。通过先卖后买、均分资金的简单逻辑，我们构建了一个完整的自动调仓流程，为后续的策略回测和结果分析打下了基础。