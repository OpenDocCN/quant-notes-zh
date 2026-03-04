# Python金融分析与量化交易实战：P47：完成策略交易展示结果

![](img/01b10f74e4c373622326d41559994476_0.png)

![](img/01b10f74e4c373622326d41559994476_2.png)

![](img/01b10f74e4c373622326d41559994476_0.png)

在本节课中，我们将继续完成REBECON策略函数。上一节我们介绍了如何根据财务指标对股票进行排名和筛选。本节中，我们将基于排名结果，计算持仓与目标持仓的差异，并据此执行具体的买卖交易操作，最终完成整个策略的回测逻辑。

![](img/01b10f74e4c373622326d41559994476_4.png)

![](img/01b10f74e4c373622326d41559994476_6.png)

## 🛠️ 计算持仓差异

![](img/01b10f74e4c373622326d41559994476_8.png)

![](img/01b10f74e4c373622326d41559994476_10.png)

接下来，我们需要编写一个函数，用于比较当前持有的股票与最新排名结果之间的差异。这个差异将作为我们执行买卖操作的依据。

![](img/01b10f74e4c373622326d41559994476_12.png)

以下是计算持仓差异的步骤：

![](img/01b10f74e4c373622326d41559994476_14.png)

1.  **获取当前持仓**：从`context`对象中提取当前账户持有的所有股票。
2.  **计算买卖列表**：
    *   `to_buy`：在最新排名中，但不在当前持仓中的股票。
    *   `to_sell`：在当前持仓中，但不在最新排名中的股票。

首先，我们编写获取当前持仓的函数 `get_holdings`。

![](img/01b10f74e4c373622326d41559994476_16.png)

```python
def get_holdings(context):
    """
    获取当前持仓的股票列表。
    参数:
        context: 策略上下文对象，包含账户信息。
    返回:
        一个列表，包含当前持有数量大于0的股票代码。
    """
    holdings = []
    # 遍历所有持仓
    for position in context.portfolio.positions:
        # 检查该股票的持仓数量是否大于0
        if context.portfolio.positions[position].quantity > 0:
            holdings.append(position) # 将股票代码加入持仓列表
    return holdings
```

![](img/01b10f74e4c373622326d41559994476_18.png)

在 `rebalance` 函数中，我们调用 `get_holdings` 并计算差异。

```python
# 获取最新股票排名列表 (stars)
stars = get_stars(context, data)

# 获取当前持仓列表
holdings = get_holdings(context)

![](img/01b10f74e4c373622326d41559994476_20.png)

![](img/01b10f74e4c373622326d41559994476_22.png)

# 将列表转换为集合，以便进行集合运算
stars_set = set(stars)
holdings_set = set(holdings)

![](img/01b10f74e4c373622326d41559994476_24.png)

# 计算需要买入的股票：在目标排名中但不在当前持仓中
to_buy = list(stars_set - holdings_set)

![](img/01b10f74e4c373622326d41559994476_26.png)

![](img/01b10f74e4c373622326d41559994476_28.png)

# 计算需要卖出的股票：在当前持仓中但不在目标排名中
to_sell = list(holdings_set - stars_set)
```

## 💸 执行交易操作

计算出买卖列表后，我们需要执行实际的交易。通常的策略是先卖出不再需要的股票，再买入新的目标股票。

以下是执行交易的步骤：

1.  **卖出操作**：遍历 `to_sell` 列表，将每只股票的持仓比例或价值调整为0。
2.  **买入操作**：遍历 `to_buy` 列表，将可用资金平均分配给每只股票进行买入。

![](img/01b10f74e4c373622326d41559994476_30.png)

```python
# 1. 执行卖出操作
for stock in to_sell:
    # order_target_percent 函数将指定股票的持仓比例调整为目标值
    # 这里目标值为0，代表全部卖出
    order_target_percent(stock, 0)

![](img/01b10f74e4c373622326d41559994476_32.png)

# 2. 执行买入操作
# 如果没有需要买入的股票，则直接返回
if len(to_buy) == 0:
    return

![](img/01b10f74e4c373622326d41559994476_34.png)

![](img/01b10f74e4c373622326d41559994476_36.png)

# 计算可用于买入股票的总资金
total_value = context.portfolio.portfolio_value
# 计算平均每只股票分配的资金
average_allocation = total_value / len(to_buy)

![](img/01b10f74e4c373622326d41559994476_38.png)

# 遍历买入列表，执行买入
for stock in to_buy:
    # order_target_value 函数尝试将指定股票的持仓市值调整为目标值
    order_target_value(stock, average_allocation)
```

![](img/01b10f74e4c373622326d41559994476_40.png)

![](img/01b10f74e4c373622326d41559994476_42.png)

## 🔧 整合与调试

![](img/01b10f74e4c373622326d41559994476_44.png)

现在，我们将所有部分整合到 `rebalance` 函数中，并处理可能出现的错误。

![](img/01b10f74e4c373622326d41559994476_46.png)

![](img/01b10f74e4c373622326d41559994476_48.png)

回顾完整的策略逻辑：
1.  在 `initialize` 中设置股票池和每月调仓。
2.  在每月调仓时，`rebalance` 函数被调用。
3.  `rebalance` 函数首先调用 `get_stars` 获取最新股票排名。
4.  然后调用 `get_holdings` 获取当前持仓。
5.  接着计算买卖列表 `to_buy` 和 `to_sell`。
6.  最后执行先卖后买的交易操作。

![](img/01b10f74e4c373622326d41559994476_50.png)

在编写和整合代码时，可能会遇到语法错误或逻辑错误。例如，之前视频中出现了中文逗号、漏写逗号以及对列表直接进行减法运算等问题。我们需要仔细检查并修正。

修正后的关键点包括：
*   确保所有标点符号为英文格式。
*   在对列表进行差异计算前，先将其转换为集合（`set`）。
*   检查函数调用和参数传递是否正确。

![](img/01b10f74e4c373622326d41559994476_52.png)

![](img/01b10f74e4c373622326d41559994476_54.png)

```python
# 示例：修正后的rebalance函数核心部分
def rebalance(context, data):
    # 获取目标股票列表
    stars = get_stars(context, data)
    # 获取当前持仓
    holdings = get_holdings(context)

    # 转换为集合进行差异计算
    to_buy = list(set(stars) - set(holdings))
    to_sell = list(set(holdings) - set(stars))

    # 卖出操作
    for stock in to_sell:
        order_target_percent(stock, 0)

    # 买入操作
    if len(to_buy) > 0:
        total_value = context.portfolio.portfolio_value
        avg_value = total_value / len(to_buy)
        for stock in to_buy:
            order_target_value(stock, avg_value)
```

![](img/01b10f74e4c373622326d41559994476_56.png)

## 📝 总结

![](img/01b10f74e4c373622326d41559994476_58.png)

本节课中我们一起学习了量化交易策略执行环节的最后步骤。我们首先编写了获取当前持仓的函数，然后通过集合运算高效地找出了需要买入和卖出的股票列表。最后，我们使用平台提供的交易函数 `order_target_percent` 和 `order_target_value` 执行了具体的买卖操作，从而完成了整个策略从选股到交易的全流程。在实践过程中，调试代码、修正语法和逻辑错误是必不可少的环节，这能确保策略能够按照预期正常运行。