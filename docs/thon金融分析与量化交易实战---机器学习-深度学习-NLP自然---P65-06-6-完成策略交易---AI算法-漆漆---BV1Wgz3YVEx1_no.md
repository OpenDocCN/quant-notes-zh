# 人工智能金融量化交易：P65：06-6-完成策略交易展示结果 🚀

![](img/fe23f32c7744ea06205db5209d3c2915_1.png)

在本节课中，我们将继续完成量化交易策略的核心函数 `rebalance`。我们将学习如何根据最新的股票排名，计算需要买入和卖出的股票，并执行相应的交易操作。整个过程将涉及持仓管理、集合运算和订单执行。

![](img/fe23f32c7744ea06205db5209d3c2915_3.png)

---

![](img/fe23f32c7744ea06205db5209d3c2915_5.png)

上一节我们完成了股票排名筛选的逻辑。本节中，我们来看看如何根据新的排名结果，执行具体的调仓交易。

## 第一步：获取当前持仓股票 📊

首先，我们需要一个函数来获取当前投资组合中持有的所有股票。以下是该函数的实现逻辑。

![](img/fe23f32c7744ea06205db5209d3c2915_7.png)

```python
def get_holdings(context):
    """
    获取当前持仓的股票列表。
    参数:
        context: 包含投资组合信息的上下文对象。
    返回:
        一个列表，包含当前持有的所有股票代码。
    """
    holdings = []
    # 从context中获取当前所有持仓头寸
    positions = context.portfolio.positions
    for position in positions:
        # 检查该头寸的持有数量是否大于0
        if positions[position].quantity > 0:
            holdings.append(position)
    return holdings
```

## 第二步：计算买卖清单 🔄

获取了当前持仓和新一轮的股票排名后，我们需要计算两者之间的差异，以确定需要买入和卖出的股票。

![](img/fe23f32c7744ea06205db5209d3c2915_9.png)

以下是计算买卖清单的步骤：

![](img/fe23f32c7744ea06205db5209d3c2915_11.png)

1.  **获取新排名**：调用之前编写的排名函数，得到本轮选出的股票列表 `new_stocks`。
2.  **获取当前持仓**：调用 `get_holdings` 函数，得到 `current_holdings`。
3.  **计算差异**：
    *   **待买入清单 (`to_buy`)**：存在于新排名中但不在当前持仓里的股票。公式为：`to_buy = set(new_stocks) - set(current_holdings)`。
    *   **待卖出清单 (`to_sell`)**：存在于当前持仓中但不在新排名里的股票。公式为：`to_sell = set(current_holdings) - set(new_stocks)`。

## 第三步：执行交易订单 💹

计算出买卖清单后，接下来就是执行具体的交易操作。通常遵循“先卖后买”的原则，以释放资金。

以下是执行交易的代码逻辑：

```python
# 1. 执行卖出操作
for stock in to_sell:
    # 将目标股票的持仓比例调整为0%，即全部卖出
    order_target_percent(stock, 0)

![](img/fe23f32c7744ea06205db5209d3c2915_13.png)

# 2. 执行买入操作
# 首先检查是否有股票需要买入
if len(to_buy) == 0:
    return  # 没有需要买入的股票，直接返回

![](img/fe23f32c7744ea06205db5209d3c2915_15.png)

![](img/fe23f32c7744ea06205db5209d3c2915_17.png)

# 计算可用于投资的现金总额
total_cash = context.portfolio.cash
# 计算平均分配到每只待买入股票上的金额
cash_per_stock = total_cash / len(to_buy)

![](img/fe23f32c7744ea06205db5209d3c2915_19.png)

![](img/fe23f32c7744ea06205db5209d3c2915_21.png)

for stock in to_buy:
    # 按计算出的金额买入股票
    order_value(stock, cash_per_stock)
```

![](img/fe23f32c7744ea06205db5209d3c2915_23.png)

![](img/fe23f32c7744ea06205db5209d3c2915_25.png)

## 第四步：整合与调试 🛠️

![](img/fe23f32c7744ea06205db5209d3c2915_27.png)

现在，我们将所有步骤整合到 `rebalance` 函数中，并处理可能出现的错误。

![](img/fe23f32c7744ea06205db5209d3c2915_29.png)

在整合过程中，需要注意以下几点：
*   确保所有从数据接口获取的字段名（如 `fundamentals.eps`）拼写正确，且使用英文标点。
*   在进行集合差集运算时，确保操作对象是 `set` 类型。
*   设置合理的回测起始和结束时间。

![](img/fe23f32c7744ea06205db5209d3c2915_31.png)

![](img/fe23f32c7744ea06205db5209d3c2915_33.png)

一个常见的错误是字段名使用了中文逗号，例如 `fundamentals.eps，` 应该修正为 `fundamentals.eps,`。另一个错误是直接对列表进行减法操作，需要先转换为集合。

![](img/fe23f32c7744ea06205db5209d3c2915_35.png)

![](img/fe23f32c7744ea06205db5209d3c2915_37.png)

修正后的核心计算部分如下：
```python
# 将列表转换为集合，以便进行差集运算
new_stocks_set = set(new_stocks)
current_holdings_set = set(current_holdings)

![](img/fe23f32c7744ea06205db5209d3c2915_39.png)

![](img/fe23f32c7744ea06205db5209d3c2915_41.png)

to_buy = new_stocks_set - current_holdings_set
to_sell = current_holdings_set - new_stocks_set
```

![](img/fe23f32c7744ea06205db5209d3c2915_43.png)

---

![](img/fe23f32c7744ea06205db5209d3c2915_45.png)

本节课中我们一起学习了如何完成一个量化交易策略的调仓逻辑。我们首先编写了获取当前持仓的函数，然后通过集合运算计算出需要买卖的股票清单，最后实现了先卖后买的订单执行流程。整个过程涵盖了策略从决策到执行的关键步骤，是构建自动化交易系统的核心。