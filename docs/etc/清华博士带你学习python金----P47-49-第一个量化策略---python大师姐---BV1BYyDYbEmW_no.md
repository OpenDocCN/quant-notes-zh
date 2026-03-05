# Python金融量化投资：P47：第一个量化策略-3 🚀

![](img/4bee95dba0a649a0928239495d997e6c_1.png)

![](img/4bee95dba0a649a0928239495d997e6c_3.png)

在本节课中，我们将学习如何将之前讨论的简单量化策略（股价低于10元买入，上涨25%止盈，下跌10%止损）转化为实际的Python代码。我们将使用量化交易平台提供的API，逐步构建一个完整的策略函数。

![](img/4bee95dba0a649a0928239495d997e6c_5.png)

---

![](img/4bee95dba0a649a0928239495d997e6c_7.png)

![](img/4bee95dba0a649a0928239495d997e6c_9.png)

## 策略逻辑回顾与代码框架

![](img/4bee95dba0a649a0928239495d997e6c_11.png)

上一节我们介绍了策略的核心逻辑。本节中，我们来看看如何用代码实现它。我们的策略将操作一个股票池（例如沪深300成分股），并对每只股票执行以下操作：
1.  如果股价低于10元且当前未持有该股票，则买入。
2.  如果当前持有某股票，且其价格相比平均成本上涨了25%，则清仓止盈。
3.  如果当前持有某股票，且其价格相比平均成本下跌了10%，则清仓止损。

![](img/4bee95dba0a649a0928239495d997e6c_13.png)

![](img/4bee95dba0a649a0928239495d997e6c_15.png)

以下是实现该策略的核心代码框架：

```python
def handle_data(context):
    # 初始化一个列表，用于记录本轮计划买入的股票
    to_buy = []

    # 遍历股票池中的每一只股票
    for stock in context.universe:
        # 获取当前股票的价格和持仓数量
        current_price = get_current_data(stock).day_open
        # 获取持仓信息，如果未持仓则返回0
        amount = context.portfolio.positions.get(stock, Position()).total_amount

        # 1. 卖出逻辑：先检查是否达到止盈或止损条件
        if amount > 0:
            # 获取该股票的平均持仓成本
            avg_cost = context.portfolio.positions[stock].avg_cost
            # 止盈条件：当前价格 >= 成本价 * 1.25
            if current_price >= avg_cost * 1.25:
                order_target(stock, 0)  # 清仓
            # 止损条件：当前价格 <= 成本价 * 0.9
            elif current_price <= avg_cost * 0.9:
                order_target(stock, 0)  # 清仓

        # 2. 买入逻辑：记录符合买入条件的股票
        if current_price <= 10 and amount == 0:
            to_buy.append(stock)

    # 3. 执行买入操作
    if len(to_buy) > 0:
        # 计算每只计划买入的股票应分配的资金
        cash_per_stock = context.portfolio.available_cash / len(to_buy)
        for stock in to_buy:
            # 按金额下单，平台会自动计算股数
            order_value(stock, cash_per_stock)
```

![](img/4bee95dba0a649a0928239495d997e6c_17.png)

![](img/4bee95dba0a649a0928239495d997e6c_19.png)

---

![](img/4bee95dba0a649a0928239495d997e6c_21.png)

## 核心概念与API详解

![](img/4bee95dba0a649a0928239495d997e6c_23.png)

为了理解上述代码，我们需要掌握几个关键概念和API的用法。

### 1. 账户与持仓信息 (`context.portfolio`)

![](img/4bee95dba0a649a0928239495d997e6c_25.png)

![](img/4bee95dba0a649a0928239495d997e6c_27.png)

在 `handle_data` 函数中，`context` 参数包含了当前策略运行的所有上下文信息，其中 `context.portfolio` 存储了账户信息。

![](img/4bee95dba0a649a0928239495d997e6c_29.png)

以下是 `context.portfolio` 中常用的属性：
*   `available_cash`：可用资金，即当前可以用于购买股票的资金。
*   `positions`：一个字典，存储所有持仓信息。键（Key）是股票代码，值（Value）是一个 `Position` 对象。

### 2. 持仓对象 (`Position`)

`Position` 对象代表对某一只股票的持仓详情，通过 `context.portfolio.positions[stock_code]` 获取。

以下是 `Position` 对象中常用的属性：
*   `total_amount`：总持仓数量（包含当日买入的、因T+1规则暂不可卖的部分）。
*   `closable_amount`：可卖出的持仓数量（不包含当日买入的部分）。
*   `avg_cost`：平均持仓成本，即买入该股票每股的平均花费。这是计算止盈止损的关键数据。

![](img/4bee95dba0a649a0928239495d997e6c_31.png)

![](img/4bee95dba0a649a0928239495d997e6c_33.png)

**公式**：
*   止盈条件：`当前价格 (P) >= 平均成本 (avg_cost) * 1.25`
*   止损条件：`当前价格 (P) <= 平均成本 (avg_cost) * 0.9`

### 3. 交易订单函数

![](img/4bee95dba0a649a0928239495d997e6c_35.png)

我们使用了两种下单函数：
*   `order_target(security, amount)`：调整股票 `security` 的持仓数量至 `amount` 股。设置 `amount=0` 即表示清仓。
*   `order_value(security, value)`：对股票 `security` 下达价值为 `value` 元的订单（买入或卖出）。平台会自动计算对应的股数。这在我们想按金额分配资金时非常方便。

![](img/4bee95dba0a649a0928239495d997e6c_37.png)

---

![](img/4bee95dba0a649a0928239495d997e6c_39.png)

## 策略执行顺序与注意事项

![](img/4bee95dba0a649a0928239495d997e6c_41.png)

![](img/4bee95dba0a649a0928239495d997e6c_43.png)

在编写策略时，执行顺序和一些市场规则需要特别注意。

### 买卖顺序：先卖后买

一般情况下，我们建议在 `handle_data` 函数中**先执行卖出逻辑，再执行买入逻辑**。这样做的好处是，卖出股票后回笼的资金可以在当天的买入操作中立即使用，从而更有效地利用资金。

![](img/4bee95dba0a649a0928239495d997e6c_45.png)

![](img/4bee95dba0a649a0928239495d997e6c_47.png)

### 处理T+1交易制度

A股实行T+1制度，即当日买入的股票，下一个交易日才能卖出。在我们的策略中，`amount`（总持仓）可能包含当日买入的部分。但在卖出判断时，我们依赖的是 `avg_cost`（平均成本），这个成本不会因为当日买入而立即变化，并且卖出操作 `order_target(stock, 0)` 会由平台自动处理可卖数量限制，因此代码在这个简单策略中是安全的。在更复杂的频繁交易策略中，则需要仔细管理 `closable_amount`。

### 处理未持仓的股票

当尝试从 `context.portfolio.positions` 字典中获取一个未持仓的股票信息时，会引发 `KeyError`。因此，我们使用 `.get(stock, Position())` 方法。如果股票不在持仓字典中，则返回一个空的 `Position` 对象，其 `total_amount` 属性为0，从而安全地进行判断。

---

## 设置基准收益

为了客观评价策略的表现，我们需要一个比较基准。通常，我们会将策略收益与大盘指数（如沪深300）的收益进行对比。

在策略的初始化函数 `initialize` 中，我们可以设置基准：

```python
def initialize(context):
    # ... 其他初始化代码 ...
    # 设置基准为沪深300指数
    set_benchmark('000300.XSHG')
```

设置后，回测结果图中会出现一条**红线**（基准线）。它表示：如果从回测期开始，将全部资金平均买入基准指数（或对应的一篮子股票）并一直持有，所获得的收益曲线。如果策略的收益曲线（蓝线）持续在基准线上方，说明策略跑赢了大盘。

---

![](img/4bee95dba0a649a0928239495d997e6c_49.png)

## 总结

![](img/4bee95dba0a649a0928239495d997e6c_51.png)

本节课中我们一起学习了如何将简单的交易逻辑转化为可执行的量化策略代码。我们掌握了：
1.  使用 `context.portfolio` 和 `Position` 对象获取账户和持仓信息。
2.  利用 `avg_cost` 和当前价格计算止盈止损点，并使用 `order_target` 函数执行清仓操作。
3.  通过先筛选符合条件的股票，再平均分配资金的方式，使用 `order_value` 函数执行买入操作。
4.  理解了策略中“先卖后买”的顺序以及T+1制度的影响。
5.  学会了通过 `set_benchmark` 设置基准收益，以更科学地评估策略表现。

![](img/4bee95dba0a649a0928239495d997e6c_53.png)

这个策略虽然简单，但涵盖了量化策略开发的基本流程：逻辑定义、数据获取、条件判断、订单执行和绩效评估。大家可以通过修改价格条件、止损止盈比例或加入更多因子来优化它，并尝试在不同的市场阶段进行回测，以深入理解策略的特性。