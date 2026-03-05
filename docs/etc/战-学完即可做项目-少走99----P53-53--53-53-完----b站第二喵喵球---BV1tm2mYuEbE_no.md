# 量化交易实战：P53：完成策略交易展示结果

![](img/f849b01adaf9b14eb42e02e39f7c9dff_1.png)

## 概述
在本节课中，我们将继续完成量化交易策略的核心函数。我们将学习如何根据新的股票排名结果，与当前持仓进行比较，计算出需要买入和卖出的股票列表，并执行相应的交易操作。这是将策略逻辑转化为实际交易指令的关键一步。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_3.png)

上一节我们介绍了如何筛选和排名股票，本节中我们来看看如何根据排名结果进行调仓交易。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_5.png)

## 获取当前持仓股票
首先，我们需要一个函数来获取当前投资组合中持有的所有股票。这个函数会遍历所有仓位，找出持仓数量大于零的股票。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_7.png)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_9.png)

以下是获取当前持仓股票的代码实现：

![](img/f849b01adaf9b14eb42e02e39f7c9dff_11.png)

```python
def get_holdings(context):
    holdings = []
    for position in context.portfolio.positions:
        if context.portfolio.positions[position].quantity > 0:
            holdings.append(position)
    return holdings
```

在这段代码中：
*   `context.portfolio.positions` 包含了当前所有的仓位信息。
*   我们遍历每个仓位，检查其 `quantity`（数量）是否大于0。
*   将持仓数量大于0的股票代码添加到 `holdings` 列表中并返回。

## 计算交易清单
获取了当前持仓和新一轮的股票排名后，下一步是计算需要买入和卖出的股票清单。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_13.png)

以下是计算交易清单的逻辑：

```python
# 获取新的股票排名列表
new_stocks = get_ranked_stocks(context)
# 获取当前持仓列表
current_holdings = get_holdings(context)

# 转换为集合以便进行集合运算
new_stocks_set = set(new_stocks)
current_holdings_set = set(current_holdings)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_15.png)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_17.png)

# 计算需要买入的股票：在新排名中但不在当前持仓中
to_buy = list(new_stocks_set - current_holdings_set)
# 计算需要卖出的股票：在当前持仓中但不在新排名中
to_sell = list(current_holdings_set - new_stocks_set)
```

核心概念是使用**集合的差集运算**：
*   **买入清单** = 新股票排名集合 - 当前持仓集合
*   **卖出清单** = 当前持仓集合 - 新股票排名集合

![](img/f849b01adaf9b14eb42e02e39f7c9dff_19.png)

## 执行卖出操作
计算出需要卖出的股票清单后，我们需要执行卖出指令。通常的策略是将这些股票全部清仓。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_21.png)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_23.png)

以下是执行卖出操作的代码：

```python
for stock in to_sell:
    # order_target_percent 函数将目标股票的仓位调整到指定的百分比
    # 这里设置为0，代表全部卖出
    order_target_percent(stock, 0)
```

`order_target_percent(stock, 0)` 是一个交易API，它的作用是将指定股票 `stock` 的持仓比例调整到0%，即全部卖出。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_25.png)

## 执行买入操作
执行完卖出操作后，我们使用释放出来的资金，平均买入“买入清单”中的股票。

以下是执行买入操作的步骤和代码：

![](img/f849b01adaf9b14eb42e02e39f7c9dff_27.png)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_29.png)

首先，检查买入清单是否为空。如果为空，则直接返回。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_31.png)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_33.png)

```python
if len(to_buy) == 0:
    return
```

![](img/f849b01adaf9b14eb42e02e39f7c9dff_35.png)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_37.png)

接着，计算可用于投资的现金总额，并计算每只股票应分配的平均资金。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_39.png)

```python
# 获取当前投资组合的总价值
total_value = context.portfolio.portfolio_value
# 计算平均每只股票应投入的资金
average_value = total_value / len(to_buy)
```

![](img/f849b01adaf9b14eb42e02e39f7c9dff_41.png)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_43.png)

最后，遍历买入清单，为每只股票执行买入指令。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_45.png)

```python
for stock in to_buy:
    # order_target_value 函数用于将目标股票的头寸价值调整到指定金额
    order_target_value(stock, average_value)
```

![](img/f849b01adaf9b14eb42e02e39f7c9dff_47.png)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_49.png)

`order_target_value(stock, average_value)` 的作用是买入股票 `stock`，直到其持仓价值达到 `average_value`。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_51.png)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_53.png)

## 代码整合与调试
我们将以上所有步骤整合到主调仓函数中。在初次运行代码时，可能会遇到一些语法错误或逻辑错误，这是正常的过程。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_55.png)

![](img/f849b01adaf9b14eb42e02e39f7c9dff_57.png)

常见的调试问题包括：
1.  **中英文标点符号混用**：例如使用了中文逗号 “，” 而不是英文逗号 “,”。
2.  **数据类型错误**：例如对列表（List）直接进行减法运算，应先将列表转换为集合（Set）。
3.  **API参数错误**：确保调用交易函数（如 `order_target_percent`）时传入了正确的参数。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_59.png)

修正错误后，完整的调仓逻辑便构建完成。它每月执行一次，根据最新的股票排名，自动完成卖出旧股票、买入新股票的全过程。

![](img/f849b01adaf9b14eb42e02e39f7c9dff_61.png)

## 总结
本节课中我们一起学习了量化交易策略的最终执行阶段。我们掌握了如何获取当前持仓、如何通过集合运算高效地计算出买卖清单、以及如何使用 `order_target_percent` 和 `order_target_value` 这两个核心交易函数来执行卖出和买入操作。这个过程实现了策略的自动化交易闭环，是量化交易从想法到实践的关键一步。在后续课程中，我们将运行这个策略并分析其回测结果。