# Python金融分析与量化交易实战教程：P38：06-6-完成策略交易展示结果 📈

![](img/f1aebf6c5de358184fab180225ce5eda_1.png)

在本节课中，我们将完成REBON函数的核心部分，即根据最新的股票排名与当前持仓的差异，执行具体的买入和卖出操作。我们将编写函数来获取当前持仓、计算买卖列表，并最终执行交易指令。

---

![](img/f1aebf6c5de358184fab180225ce5eda_3.png)

![](img/f1aebf6c5de358184fab180225ce5eda_5.png)

## 获取当前持仓

上一节我们介绍了如何计算新的股票排名。本节中，我们来看看如何获取当前的投资组合持仓情况。

以下是获取当前持仓股票的步骤：

1.  从 `context` 对象中提取持仓信息 `positions`。
2.  遍历所有持仓，筛选出持有数量大于0的股票。
3.  将这些股票的代码存入一个列表并返回。

![](img/f1aebf6c5de358184fab180225ce5eda_7.png)

```python
def get_holdings(context):
    """
    获取当前持仓的股票列表。
    """
    holdings = []  # 初始化一个空列表，用于存放持仓股票
    positions = context.portfolio.positions  # 从context中获取持仓信息

    for position in positions.values():  # 遍历所有持仓
        if position.quantity > 0:  # 判断持仓数量是否大于0
            holdings.append(position.sid)  # 将股票代码加入列表

    return holdings  # 返回持仓股票列表
```

---

## 计算买卖列表并执行交易

获取了当前持仓和新的股票排名后，我们需要计算两者之间的差异，以确定需要买入和卖出的股票。

![](img/f1aebf6c5de358184fab180225ce5eda_9.png)

![](img/f1aebf6c5de358184fab180225ce5eda_11.png)

以下是执行交易的核心逻辑：

1.  计算需要买入的股票列表：`to_buy = set(stocks) - set(holdings)`。
2.  计算需要卖出的股票列表：`to_sell = set(holdings) - set(stocks)`。
3.  先执行卖出操作：遍历 `to_sell` 列表，将每只股票的持仓比例调整为0%。
4.  再执行买入操作：计算可用资金，并平均分配到 `to_buy` 列表中的每只股票上，然后执行买入。

```python
# 假设 stocks 是新的股票排名列表，holdings 是当前持仓列表
holdings = get_holdings(context)

# 计算买卖列表（使用集合操作求差集）
to_buy = list(set(stocks) - set(holdings))
to_sell = list(set(holdings) - set(stocks))

# 执行卖出操作
for stock in to_sell:
    order_target_percent(stock, 0)  # 将股票持仓比例调整为0%，即全部卖出

# 执行买入操作
if len(to_buy) > 0:  # 如果有需要买入的股票
    # 计算平均分配到每只股票的资金
    available_cash = context.portfolio.cash
    average_value = available_cash / len(to_buy)

    for stock in to_buy:
        order_target_value(stock, average_value)  # 按目标金额买入股票
else:
    return  # 如果没有需要买入的股票，则直接返回
```

![](img/f1aebf6c5de358184fab180225ce5eda_13.png)

---

![](img/f1aebf6c5de358184fab180225ce5eda_15.png)

## 代码整合与回顾

![](img/f1aebf6c5de358184fab180225ce5eda_17.png)

![](img/f1aebf6c5de358184fab180225ce5eda_19.png)

现在，让我们将上述步骤整合到每月调仓的主函数中，并回顾整个策略的逻辑流程。

![](img/f1aebf6c5de358184fab180225ce5eda_21.png)

1.  **初始化**：在 `initialize` 函数中设置股票池和初始变量。
2.  **每月调仓**：在 `rebalance` 函数中：
    *   调用 `get_scores` 函数计算新的股票排名 `stocks`。
    *   调用 `get_holdings` 函数获取当前持仓 `holdings`。
    *   计算买卖列表 `to_buy` 和 `to_sell`。
    *   执行先卖后买的交易操作。

![](img/f1aebf6c5de358184fab180225ce5eda_23.png)

![](img/f1aebf6c5de358184fab180225ce5eda_25.png)

主要的数据处理函数 `get_scores` 负责查询财务指标（如市盈率PE、市净率PB等），并进行排序筛选，最终输出排名靠前的股票列表。

![](img/f1aebf6c5de358184fab180225ce5eda_27.png)

---

![](img/f1aebf6c5de358184fab180225ce5eda_29.png)

## 运行调试与错误修正

![](img/f1aebf6c5de358184fab180225ce5eda_31.png)

代码编写完成后，运行测试是必不可少的环节。初学者在运行过程中可能会遇到各种错误，这是正常的学习过程。

![](img/f1aebf6c5de358184fab180225ce5eda_33.png)

![](img/f1aebf6c5de358184fab180225ce5eda_35.png)

以下是一些常见的错误及修正方法：

![](img/f1aebf6c5de358184fab180225ce5eda_37.png)

![](img/f1aebf6c5de358184fab180225ce5eda_39.png)

*   **无效字符错误**：代码中误用了中文标点符号（如中文逗号）。确保所有标点均为英文格式。
*   **类型错误**：对列表直接进行减法操作会导致错误。应先将列表转换为集合（`set`），再进行差集运算。
*   **语法错误**：检查函数调用、变量赋值等语句是否缺少必要的逗号或括号。

![](img/f1aebf6c5de358184fab180225ce5eda_41.png)

通过仔细阅读错误信息，定位问题代码行，并参照上述方法进行修正，可以逐步解决这些问题，使策略成功运行。

![](img/f1aebf6c5de358184fab180225ce5eda_43.png)

---

![](img/f1aebf6c5de358184fab180225ce5eda_45.png)

本节课中我们一起学习了量化交易策略的执行环节。我们掌握了如何获取持仓、根据排名差异计算买卖列表，并最终使用 `order_target_percent` 和 `order_target_value` 函数执行自动化交易。整个过程涵盖了策略从逻辑判断到实际下单的关键步骤，是构建一个完整交易系统的重要基础。