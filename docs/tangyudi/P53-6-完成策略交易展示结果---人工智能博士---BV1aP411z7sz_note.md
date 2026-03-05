# 量化交易策略：P53：6-完成策略交易展示结果 📊

![](img/3f1362c0bd4cf51547a62a77f7bf3796_1.png)

在本节课中，我们将要学习如何完成一个量化交易策略的核心部分：根据新的股票排名结果，计算需要买入和卖出的股票，并执行相应的交易操作。我们将一步步构建一个完整的调仓函数。

![](img/3f1362c0bd4cf51547a62a77f7bf3796_3.png)

![](img/3f1362c0bd4cf51547a62a77f7bf3796_5.png)

## 概述

上一节我们介绍了如何获取并筛选股票数据，本节中我们来看看如何基于新的排名结果，与当前持仓进行比较，从而生成具体的买卖指令，完成策略的闭环。

## 获取当前持仓

首先，我们需要一个函数来获取当前投资组合中持有的所有股票。以下是实现该功能的步骤：

![](img/3f1362c0bd4cf51547a62a77f7bf3796_7.png)

以下是获取当前持仓的代码实现：

```python
def get_holdings(context):
    holdings = []
    for position in context.portfolio.positions:
        if context.portfolio.positions[position].quantity > 0:
            holdings.append(position)
    return holdings
```

这段代码遍历投资组合中的所有持仓，检查其数量是否大于零，如果是，则将该股票代码加入持仓列表。

![](img/3f1362c0bd4cf51547a62a77f7bf3796_9.png)

![](img/3f1362c0bd4cf51547a62a77f7bf3796_11.png)

## 计算买卖清单

获取了当前持仓后，下一步是将其与新的股票排名结果进行比较，计算出需要买入和卖出的股票清单。

以下是计算买卖清单的逻辑：

1.  **计算买入清单 (`to_buy`)**：新的股票排名 (`stocks`) 减去当前持仓 (`holdings`)。这代表了排名靠前但尚未持有的股票。
    *   `to_buy = set(stocks) - set(holdings)`
2.  **计算卖出清单 (`to_sell`)**：当前持仓 (`holdings`) 减去新的股票排名 (`stocks`)。这代表了已持有但已不在新排名中的股票。
    *   `to_sell = set(holdings) - set(stocks)`

## 执行交易操作

![](img/3f1362c0bd4cf51547a62a77f7bf3796_13.png)

计算出买卖清单后，接下来需要执行具体的交易指令。通常的策略是先卖出，再买入。

![](img/3f1362c0bd4cf51547a62a77f7bf3796_15.png)

以下是执行交易的步骤：

![](img/3f1362c0bd4cf51547a62a77f7bf3796_17.png)

![](img/3f1362c0bd4cf51547a62a77f7bf3796_19.png)

1.  **执行卖出操作**：遍历卖出清单，将每只股票的持仓比例调整为0%，即清仓。
    ```python
    for stock in to_sell:
        order_target_percent(stock, 0)
    ```
2.  **执行买入操作**：首先检查买入清单是否为空。如果不为空，则计算可用于投资的现金，并平均分配到每只要买入的股票上。
    *   计算可用资金：`cash = context.portfolio.cash`
    *   计算每只股票应分配的资金比例：`average_percent = 1.0 / len(to_buy)`
    *   遍历买入清单，为每只股票下单。
    ```python
    if len(to_buy) > 0:
        average_percent = 1.0 / len(to_buy)
        for stock in to_buy:
            order_target_percent(stock, average_percent)
    ```

![](img/3f1362c0bd4cf51547a62a77f7bf3796_21.png)

![](img/3f1362c0bd4cf51547a62a77f7bf3796_23.png)

## 整合与调试

![](img/3f1362c0bd4cf51547a62a77f7bf3796_25.png)

现在，我们将上述步骤整合到每月调仓的主函数中。同时，需要注意代码中的细节，例如标点符号必须使用英文格式，以及变量类型转换（如将列表转换为集合进行差集运算）。

![](img/3f1362c0bd4cf51547a62a77f7bf3796_27.png)

![](img/3f1362c0bd4cf51547a62a77f7bf3796_29.png)

以下是整合后的核心流程回顾：

![](img/3f1362c0bd4cf51547a62a77f7bf3796_31.png)

1.  每月初，获取新的股票排名 (`stocks`)。
2.  调用 `get_holdings` 函数获取当前持仓 (`holdings`)。
3.  通过集合运算，计算出 `to_buy` 和 `to_sell`。
4.  先执行卖出操作，再执行买入操作。

![](img/3f1362c0bd4cf51547a62a77f7bf3796_33.png)

![](img/3f1362c0bd4cf51547a62a77f7bf3796_35.png)

运行代码时可能会遇到错误，常见的包括：
*   **无效字符错误**：检查代码中是否误用了中文标点（如逗号、括号）。
*   **类型错误**：确保在进行集合差集运算（`-`）前，已将列表转换为集合（`set()`）。

![](img/3f1362c0bd4cf51547a62a77f7bf3796_37.png)

通过逐步调试和修正这些错误，最终使策略能够顺利运行。

![](img/3f1362c0bd4cf51547a62a77f7bf3796_39.png)

## 总结

![](img/3f1362c0bd4cf51547a62a77f7bf3796_41.png)

本节课中我们一起学习了如何完成一个量化交易策略的交易执行部分。我们掌握了三个关键环节：获取当前持仓、通过比较新旧排名计算买卖清单、以及执行具体的买入和卖出订单。这个过程是策略从信号生成到实际交易的关键一步，确保了策略逻辑的闭环。记住，在编写代码时，注意细节和调试是成功实现策略的重要保障。