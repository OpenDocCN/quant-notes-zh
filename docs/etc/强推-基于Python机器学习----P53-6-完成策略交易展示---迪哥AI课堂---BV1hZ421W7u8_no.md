# 量化交易实战：P53：6-完成策略交易展示结果 📈

![](img/4c8c31a915e235cec1978dafa2b68e32_1.png)

在本节课中，我们将完成交易策略的核心部分，即根据股票排名结果，计算需要买入和卖出的股票，并执行相应的交易操作。我们将学习如何获取持仓信息、计算持仓与目标持仓的差异，并基于此差异执行买卖指令。

![](img/4c8c31a915e235cec1978dafa2b68e32_3.png)

---

![](img/4c8c31a915e235cec1978dafa2b68e32_5.png)

![](img/4c8c31a915e235cec1978dafa2b68e32_7.png)

上一节我们介绍了如何筛选和排名股票，本节中我们来看看如何根据排名结果执行实际的交易操作。

![](img/4c8c31a915e235cec1978dafa2b68e32_9.png)

## 获取当前持仓信息

![](img/4c8c31a915e235cec1978dafa2b68e32_11.png)

![](img/4c8c31a915e235cec1978dafa2b68e32_13.png)

首先，我们需要一个函数来获取当前账户持有的所有股票。以下是实现此功能的步骤：

1.  从 `context` 对象中获取持仓信息。
2.  遍历所有持仓，筛选出持仓数量大于零的股票。
3.  将这些股票的代码存入一个列表并返回。

以下是实现该功能的代码：

![](img/4c8c31a915e235cec1978dafa2b68e32_15.png)

```python
def get_holdings(context):
    # 初始化一个空列表，用于存放当前持有的股票代码
    holdings = []
    # 从context中获取所有持仓信息
    positions = context.portfolio.positions
    # 遍历每一个持仓
    for position in positions:
        # 获取该股票的持仓数量
        quantity = context.portfolio.positions[position].quantity
        # 如果持仓数量大于0，则将其加入持有列表
        if quantity > 0:
            holdings.append(position)
    # 返回当前持有的股票列表
    return holdings
```

![](img/4c8c31a915e235cec1978dafa2b68e32_17.png)

## 计算交易信号

![](img/4c8c31a915e235cec1978dafa2b68e32_19.png)

获取了当前持仓和新的目标股票排名后，下一步是计算需要买入和卖出的股票。以下是计算逻辑：

![](img/4c8c31a915e235cec1978dafa2b68e32_21.png)

1.  **需要买入的股票 (`to_buy`)**：目标股票列表中，剔除当前已持有的部分。
2.  **需要卖出的股票 (`to_sell`)**：当前持有的股票中，剔除仍在目标列表中的部分。

![](img/4c8c31a915e235cec1978dafa2b68e32_23.png)

以下是实现此逻辑的代码：

![](img/4c8c31a915e235cec1978dafa2b68e32_25.png)

```python
# 获取新的目标股票排名列表
target_stocks = get_scores(context, data)
# 获取当前持仓列表
current_holdings = get_holdings(context)

![](img/4c8c31a915e235cec1978dafa2b68e32_27.png)

# 将列表转换为集合，以便进行集合运算
target_set = set(target_stocks)
holdings_set = set(current_holdings)

![](img/4c8c31a915e235cec1978dafa2b68e32_29.png)

# 计算需要买入的股票：在目标中但不在当前持仓中
to_buy = list(target_set - holdings_set)
# 计算需要卖出的股票：在当前持仓中但不在目标中
to_sell = list(holdings_set - target_set)
```

## 执行交易操作

![](img/4c8c31a915e235cec1978dafa2b68e32_31.png)

计算出买卖列表后，就可以执行具体的交易指令了。通常遵循“先卖后买”的原则，以确保有足够的资金。

以下是执行交易的步骤：

![](img/4c8c31a915e235cec1978dafa2b68e32_33.png)

1.  **卖出操作**：遍历 `to_sell` 列表，将每只股票的持仓比例调整为0%（即全部卖出）。
2.  **买入操作**：
    *   首先检查 `to_buy` 列表是否为空。若为空，则直接返回。
    *   计算可用于购买股票的总资金。
    *   将总资金平均分配到每只需要买入的股票上，确定每只股票的购买金额。
    *   遍历 `to_buy` 列表，按计算出的金额执行买入操作。

![](img/4c8c31a915e235cec1978dafa2b68e32_35.png)

以下是实现交易执行的代码：

![](img/4c8c31a915e235cec1978dafa2b68e32_37.png)

![](img/4c8c31a915e235cec1978dafa2b68e32_39.png)

```python
# 1. 执行卖出操作
for stock in to_sell:
    # order_target_percent 函数用于将某只股票的持仓调整到目标比例
    # 这里将比例设为0，即全部卖出
    order_target_percent(stock, 0)

![](img/4c8c31a915e235cec1978dafa2b68e32_41.png)

![](img/4c8c31a915e235cec1978dafa2b68e32_43.png)

# 2. 执行买入操作
# 如果没有需要买入的股票，则直接返回
if len(to_buy) == 0:
    return

![](img/4c8c31a915e235cec1978dafa2b68e32_45.png)

# 计算账户总资产价值
total_value = context.portfolio.portfolio_value
# 计算平均每只股票应投入的资金
average_value = total_value / len(to_buy)

![](img/4c8c31a915e235cec1978dafa2b68e32_47.png)

![](img/4c8c31a915e235cec1978dafa2b68e32_49.png)

# 遍历需要买入的股票列表，执行买入
for stock in to_buy:
    # order_target_value 函数用于按目标金额买入股票
    order_target_value(stock, average_value)
```

## 代码整合与调试

![](img/4c8c31a915e235cec1978dafa2b68e32_51.png)

![](img/4c8c31a915e235cec1978dafa2b68e32_53.png)

![](img/4c8c31a915e235cec1978dafa2b68e32_55.png)

将以上所有函数和逻辑整合到主调度函数 `rebalance` 中，并运行整个策略。初次运行很可能会遇到语法错误或逻辑错误，需要根据报错信息进行调试。

![](img/4c8c31a915e235cec1978dafa2b68e32_57.png)

常见的调试点包括：
*   **标点符号错误**：如使用了中文逗号。
*   **变量名拼写错误**。
*   **数据类型错误**：例如对列表直接进行减法运算（应转换为集合）。
*   **函数参数错误**：确保调用API函数时传入了正确的参数。

![](img/4c8c31a915e235cec1978dafa2b68e32_59.png)

![](img/4c8c31a915e235cec1978dafa2b68e32_61.png)

通过逐步修正这些错误，最终使策略能够顺利运行。

![](img/4c8c31a915e235cec1978dafa2b68e32_63.png)

---

![](img/4c8c31a915e235cec1978dafa2b68e32_65.png)

本节课中我们一起学习了量化交易策略执行的完整流程。我们首先编写了获取当前持仓的函数，然后通过集合运算计算出需要买入和卖出的股票列表，最后利用交易API执行了“先卖后买”的操作。这个过程是连接策略逻辑与真实市场交易的关键环节。通过不断的调试和完善，一个基础的量化交易策略就搭建完成了。