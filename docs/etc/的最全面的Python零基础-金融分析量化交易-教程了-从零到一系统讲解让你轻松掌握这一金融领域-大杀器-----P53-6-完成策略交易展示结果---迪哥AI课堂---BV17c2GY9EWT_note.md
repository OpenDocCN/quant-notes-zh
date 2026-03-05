# Python金融量化交易：P53：6-完成策略交易展示结果

![](img/11facec4b79628018678e9a0a9d7cae1_1.png)

在本节课中，我们将完成REBALANCE函数的核心逻辑，实现基于股票排名差异的自动化买卖操作。我们将学习如何获取当前持仓、计算买卖列表，并执行具体的交易指令。

![](img/11facec4b79628018678e9a0a9d7cae1_3.png)

![](img/11facec4b79628018678e9a0a9d7cae1_5.png)

上一节我们介绍了如何筛选出排名靠前的股票，本节中我们来看看如何根据新的排名结果与现有持仓的差异，执行具体的买入和卖出操作。

![](img/11facec4b79628018678e9a0a9d7cae1_7.png)

![](img/11facec4b79628018678e9a0a9d7cae1_9.png)

## 获取当前持仓

![](img/11facec4b79628018678e9a0a9d7cae1_11.png)

![](img/11facec4b79628018678e9a0a9d7cae1_13.png)

首先，我们需要一个函数来获取当前投资组合中持有的所有股票。以下是实现步骤：

![](img/11facec4b79628018678e9a0a9d7cae1_15.png)

1.  从 `context` 对象中获取持仓信息 `context.portfolio.positions`。
2.  遍历所有持仓，筛选出持有数量大于0的股票。
3.  将这些股票的代码存入一个列表并返回。

以下是获取持仓的代码实现：

![](img/11facec4b79628018678e9a0a9d7cae1_17.png)

```python
def get_holdings(context):
    """
    获取当前持仓的股票列表。
    """
    holdings = []
    # 遍历所有持仓
    for position in context.portfolio.positions.values():
        # 判断持仓数量是否大于0
        if position.quantity > 0:
            # 将股票代码加入列表
            holdings.append(position.sid)
    return holdings
```

![](img/11facec4b79628018678e9a0a9d7cae1_19.png)

## 计算买卖列表

![](img/11facec4b79628018678e9a0a9d7cae1_21.png)

获取了当前持仓和新的股票排名后，下一步是计算需要买入和卖出的股票列表。

![](img/11facec4b79628018678e9a0a9d7cae1_23.png)

![](img/11facec4b79628018678e9a0a9d7cae1_25.png)

以下是计算逻辑：
*   **需要买入的股票 (`to_buy`)**: 在新排名中，但不在当前持仓中的股票。公式为：`to_buy = set(new_ranks) - set(current_holdings)`。
*   **需要卖出的股票 (`to_sell`)**: 在当前持仓中，但不在新排名中的股票。公式为：`to_sell = set(current_holdings) - set(new_ranks)`。

![](img/11facec4b79628018678e9a0a9d7cae1_27.png)

以下是计算买卖列表的代码片段：

![](img/11facec4b79628018678e9a0a9d7cae1_29.png)

```python
# 假设 new_ranks 是新的股票排名列表
new_ranks = get_scores(context, data)
# 获取当前持仓
current_holdings = get_holdings(context)

![](img/11facec4b79628018678e9a0a9d7cae1_31.png)

![](img/11facec4b79628018678e9a0a9d7cae1_33.png)

# 计算买卖列表（使用集合进行差集运算）
to_buy = list(set(new_ranks) - set(current_holdings))
to_sell = list(set(current_holdings) - set(new_ranks))
```

## 执行交易操作

![](img/11facec4b79628018678e9a0a9d7cae1_35.png)

计算出买卖列表后，我们就可以执行具体的交易指令了。通常的策略是先卖出，再买入。

![](img/11facec4b79628018678e9a0a9d7cae1_37.png)

以下是执行交易的步骤：

![](img/11facec4b79628018678e9a0a9d7cae1_39.png)

1.  **卖出操作**：遍历 `to_sell` 列表，对每只股票执行清仓操作，即将其持仓比例调整为0%。
2.  **买入操作**：首先检查 `to_buy` 列表是否为空。若不为空，则计算可用资金，并平均分配到每只待买入的股票上，然后执行买入。

![](img/11facec4b79628018678e9a0a9d7cae1_41.png)

![](img/11facec4b79628018678e9a0a9d7cae1_43.png)

以下是执行交易的代码实现：

![](img/11facec4b79628018678e9a0a9d7cae1_45.png)

```python
# 1. 执行卖出操作
for stock in to_sell:
    # order_target_percent 函数用于将股票持仓调整到目标百分比
    # 这里设置为0，代表清仓该股票
    order_target_percent(stock, 0)

![](img/11facec4b79628018678e9a0a9d7cae1_47.png)

![](img/11facec4b79628018678e9a0a9d7cae1_49.png)

# 2. 执行买入操作
if len(to_buy) == 0:
    # 如果没有需要买入的股票，则直接返回
    return
else:
    # 计算可用总资产
    total_value = context.portfolio.portfolio_value
    # 计算每只股票应分配的资金比例（平均分配）
    average_allocation = 1.0 / len(to_buy)

    # 遍历买入列表，执行买入
    for stock in to_buy:
        order_target_percent(stock, average_allocation)
```

![](img/11facec4b79628018678e9a0a9d7cae1_51.png)

![](img/11facec4b79628018678e9a0a9d7cae1_53.png)

## 整合与调试

![](img/11facec4b79628018678e9a0a9d7cae1_55.png)

现在，我们将所有步骤整合到 `rebalance` 函数中，并处理可能出现的错误，例如代码中的拼写错误或格式问题（如中英文标点混用）。

![](img/11facec4b79628018678e9a0a9d7cae1_57.png)

![](img/11facec4b79628018678e9a0a9d7cae1_59.png)

![](img/11facec4b79628018678e9a0a9d7cae1_61.png)

以下是整合后的 `rebalance` 函数框架及注意事项：

![](img/11facec4b79628018678e9a0a9d7cae1_63.png)

![](img/11facec4b79628018678e9a0a9d7cae1_65.png)

```python
def rebalance(context, data):
    """
    每月调仓的主函数。
    """
    # 获取新的股票排名
    new_ranks = get_scores(context, data)
    # 获取当前持仓
    current_holdings = get_holdings(context)

    # 计算买卖列表
    to_buy = list(set(new_ranks) - set(current_holdings))
    to_sell = list(set(current_holdings) - set(new_ranks))

    # 执行卖出
    for stock in to_sell:
        order_target_percent(stock, 0)

    # 执行买入
    if len(to_buy) > 0:
        average_allocation = 1.0 / len(to_buy)
        for stock in to_buy:
            order_target_percent(stock, average_allocation)
```

**调试提示**：在运行策略时，常见的错误包括API调用错误、数据类型不匹配（如对列表直接进行减法运算）、以及标点符号错误。务必仔细检查代码，并使用 `print` 语句或日志输出中间结果来辅助调试。

![](img/11facec4b79628018678e9a0a9d7cae1_67.png)

![](img/11facec4b79628018678e9a0a9d7cae1_69.png)

本节课中我们一起学习了量化交易策略中自动调仓的核心流程。我们实现了三个关键部分：获取当前持仓、根据新排名计算买卖差异列表、以及执行具体的买入和卖出交易指令。这个过程是构建自动化交易策略的基础，理解它对于后续开发更复杂的策略至关重要。