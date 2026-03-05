# Python金融量化与股票交易：P53：完成策略交易展示结果 📈

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_1.png)

在本节课中，我们将继续完成 `rebalance` 函数。上一节我们介绍了如何获取当前排名靠前的股票列表。本节中，我们将基于此列表，计算需要买入和卖出的股票，并执行实际的交易操作。

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_3.png)

## 计算持仓股票

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_5.png)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_7.png)

首先，我们需要一个函数来获取当前账户中持有的股票。以下是该函数的实现步骤。

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_9.png)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_11.png)

```python
def get_holdings(context):
    """
    获取当前持仓的股票列表。
    参数:
        context: 策略上下文对象，包含账户信息。
    返回:
        一个列表，包含当前持有的所有股票代码。
    """
    holdings = []
    # 从context中获取当前所有持仓
    positions = context.portfolio.positions
    for position in positions:
        # 检查该持仓的数量是否大于0
        if positions[position].quantity > 0:
            # 如果持有，则将其股票代码加入列表
            holdings.append(position)
    return holdings
```

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_13.png)

## 计算交易信号

获取了当前持仓和新一轮的股票排名后，我们需要计算具体的交易指令：哪些股票需要买入，哪些需要卖出。

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_15.png)

以下是计算逻辑：
*   **买入列表 (to_buy)** = **新排名股票 (stocks)** - **当前持仓股票 (holdings)**
*   **卖出列表 (to_sell)** = **当前持仓股票 (holdings)** - **新排名股票 (stocks)**

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_17.png)

```python
# 获取新一轮的股票排名
stocks = get_stocks(context)
# 获取当前持仓
holdings = get_holdings(context)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_19.png)

# 将列表转换为集合，以便进行集合运算
stocks_set = set(stocks)
holdings_set = set(holdings)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_21.png)

# 计算需要买入和卖出的股票
to_buy = list(stocks_set - holdings_set)  # 在新排名中但不在当前持仓中
to_sell = list(holdings_set - stocks_set) # 在当前持仓中但不在新排名中
```

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_23.png)

## 执行卖出操作

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_25.png)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_27.png)

在量化交易中，通常遵循“先卖后买”的原则，以确保有足够的资金进行买入。以下是卖出操作的实现。

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_29.png)

```python
# 遍历卖出列表，将每只股票的持仓比例调整为0%（即全部卖出）
for stock in to_sell:
    order_target_percent(stock, 0)
```

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_31.png)

## 执行买入操作

卖出完成后，我们用剩余的资金平均买入“买入列表”中的股票。

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_33.png)

以下是买入前的准备和执行步骤：
1.  计算可用总资产。
2.  计算每只计划买入的股票应分配的资金（平均分配）。
3.  执行买入订单。

```python
# 如果没有需要买入的股票，则直接返回
if len(to_buy) == 0:
    return

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_35.png)

# 计算可用总资产
total_value = context.portfolio.portfolio_value
# 计算每只股票应买入的金额（平均分配）
average_value = total_value / len(to_buy)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_37.png)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_39.png)

# 遍历买入列表，按计算出的金额买入每只股票
for stock in to_buy:
    order_target_value(stock, average_value)
```

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_41.png)

## 代码整合与运行调试

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_43.png)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_45.png)

现在，我们将所有步骤整合到 `rebalance` 函数中，并运行策略进行测试。

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_47.png)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_49.png)

在初次运行时，代码可能会因为一些细节错误（如中文标点、缺少逗号、变量名错误或数据类型不匹配）而报错。开发过程中需要耐心检查并修正这些错误。

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_51.png)

常见的调试点包括：
*   检查所有函数调用和变量名的拼写。
*   确保所有列表、元组和函数参数的分隔符是英文逗号。
*   在进行集合运算（如求差集）前，确保操作对象是 `set` 类型。
*   验证从 `context` 对象中获取数据（如 `portfolio.positions`）的路径是否正确。

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_53.png)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_55.png)

修正错误后，再次运行策略，观察是否能够顺利执行调仓逻辑。

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_57.png)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_59.png)

## 策略逻辑回顾

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_61.png)

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_63.png)

本节课中我们一起学习了如何完成一个完整的月度调仓策略。我们来回顾一下整个流程：

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_65.png)

1.  **初始化**：在 `initialize` 函数中设置股票池、基准和调仓周期（每月）。
2.  **获取股票排名**：在 `get_stocks` 函数中，基于财务指标（如市盈率PE、市净率PB）对股票进行筛选和排序，返回排名靠前的股票列表。
3.  **获取当前持仓**：在 `get_holdings` 函数中，查询当前账户实际持有的股票。
4.  **生成交易信号**：比较新的股票排名和当前持仓，计算出需要买入 (`to_buy`) 和卖出 (`to_sell`) 的股票列表。
5.  **执行交易**：先卖出不再需要的股票，然后将可用资金平均买入新的目标股票。

![](img/ef4e0e12ce1d3ffb382ef4f34e7cf16e_67.png)

通过本节课的学习，你已经掌握了量化策略中核心的交易执行逻辑。从数据获取、信号生成到订单执行，这是一个策略从想法到实践的关键步骤。