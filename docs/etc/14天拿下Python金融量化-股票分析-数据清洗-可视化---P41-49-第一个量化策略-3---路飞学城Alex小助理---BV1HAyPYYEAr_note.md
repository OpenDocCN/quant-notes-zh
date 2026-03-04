# Python金融量化：P41：第一个量化策略-3 🚀

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_1.png)

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_3.png)

在本节课中，我们将继续编写第一个量化策略。我们将实现一个简单的策略逻辑：当股价低于10元且当前不持仓时买入；当股价相比买入成本上涨25%时止盈卖出；当股价相比买入成本下跌10%时止损卖出。

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_5.png)

---

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_7.png)

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_9.png)

## 策略逻辑回顾与实现框架

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_11.png)

上一节我们介绍了策略的基本思路，本节中我们来看看如何用代码实现它。

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_13.png)

我们的策略操作对象是`security`列表中的300只股票。因此，我们需要遍历这个列表，对每只股票执行相同的逻辑判断。

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_15.png)

以下是实现策略的核心步骤：

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_17.png)

1.  **遍历股票池**：使用`for`循环处理`security`列表中的每一只股票。
2.  **获取关键数据**：对于每只股票，我们需要获取其当前价格和当前持仓数量。
3.  **判断买卖条件**：
    *   **买入**：价格低于10元且当前不持仓。
    *   **卖出（止盈）**：当前价格高于买入平均成本的125%。
    *   **卖出（止损）**：当前价格低于买入平均成本的90%。
4.  **执行交易**：根据判断结果，调用相应的下单函数。

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_19.png)

---

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_21.png)

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_23.png)

## 获取股票数据与账户信息

要实现上述逻辑，我们首先需要知道如何获取股票的价格和我们的持仓情况。

### 获取当前股价
我们可以使用`get_current_data()`函数来获取股票的最新数据。例如，获取某只股票今日的开盘价（作为当前价格参考）：
```python
P = get_current_data(stock).day_open
```
其中，`stock`是股票代码变量。

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_25.png)

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_27.png)

### 获取持仓信息
我们的账户信息存储在`handle_data`函数的`context`参数中。`context.portfolio`包含了账户的所有信息，如现金、持仓等。

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_29.png)

要获取某只股票的持仓数量，需要访问`context.portfolio.positions`。这是一个字典，键是股票代码，值是一个`Position`对象，包含了该股票的详细信息。

以下是获取持仓数量的方法：
```python
# 获取该股票的持仓对象
position = context.portfolio.positions[stock]
# 从持仓对象中获取总持仓数量
amount = position.total_amount
```
`total_amount`表示持有该股票的总股数。另一个属性`closable_amount`表示当天可卖出的股数，由于A股T+1制度，两者可能不同。

---

## 实现买入逻辑

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_31.png)

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_33.png)

在遍历每只股票时，我们先判断是否符合买入条件。

一个更合理的买入方式是：将所有可用资金平均分配给所有符合买入条件的股票。为此，我们需要：
1.  创建一个列表（例如`to_buy`），用于记录所有符合买入条件的股票代码。
2.  在循环中，如果股票满足`P <= 10 and amount == 0`，则将其代码加入`to_buy`列表。
3.  循环结束后，计算每只股票应分配的资金：`cash_per_stock = context.portfolio.available_cash / len(to_buy)`。
4.  最后，遍历`to_buy`列表，为每只股票执行买入操作。

以下是买入部分的代码框架：
```python
to_buy = []
for stock in security:
    P = get_current_data(stock).day_open
    amount = context.portfolio.positions[stock].total_amount
    if P <= 10 and amount == 0:
        to_buy.append(stock)

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_35.png)

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_37.png)

if len(to_buy) > 0:
    cash_per_stock = context.portfolio.available_cash / len(to_buy)
    for stock in to_buy:
        order_value(stock, cash_per_stock)
```
这里使用`order_value(stock, cash_per_stock)`函数，意为买入价值约为`cash_per_stock`元的该股票，系统会自动计算股数。

---

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_39.png)

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_41.png)

## 实现卖出逻辑

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_43.png)

通常情况下，在`handle_data`函数中我们会先处理卖出，再处理买入，这样卖出股票回收的资金可以用于当天的买入操作。

对于卖出，我们需要获取买入该股票的平均成本（开仓均价），它存储在`Position`对象的`avg_cost`属性中。

以下是卖出（止盈/止损）的判断与执行：
```python
for stock in security:
    P = get_current_data(stock).day_open
    position = context.portfolio.positions[stock]
    amount = position.total_amount
    cost = position.avg_cost # 平均持仓成本

    if amount > 0:
        # 止盈：当前价格 >= 成本 * 125%
        if P >= cost * 1.25:
            order_target(stock, 0) # 卖出全部持仓，将目标数量设为0
        # 止损：当前价格 <= 成本 * 90%
        elif P <= cost * 0.90:
            order_target(stock, 0)
```
`order_target(stock, 0)`函数会将该股票的持仓调整到0股，即清仓卖出。

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_45.png)

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_47.png)

---

## 策略回测与基准收益

将买入和卖出逻辑组合到`handle_data`函数中，就完成了策略的第一个版本。进行回测时，我们通常需要设置一个**基准收益（Benchmark）** 来评估策略表现。

基准收益就像一把尺子，用来衡量策略的优劣。如果策略收益曲线（蓝线）持续高于基准线（红线），说明策略跑赢了市场（或参照物）；反之则说明跑输。

设置基准通常在`initialize`函数中完成：
```python
def initialize(context):
    ...
    set_benchmark('000300.XSHG') # 设置基准为沪深300指数
```
基准的含义是：假设在回测期初，将所有资金按指数成分股权重（或平均买入指数成分股）买入并一直持有，得到的收益曲线。它代表了市场整体的平均表现。

---

## 代码优化与注意事项

在策略实现和回测中，还有一些细节需要注意：

1.  **处理未持仓股票**：当尝试从`context.portfolio.positions`中获取一个未持仓的股票信息时，可能会引发警告或错误。更稳健的写法是先判断股票是否在持仓字典中：
    ```python
    if stock in context.portfolio.positions:
        amount = context.portfolio.positions[stock].total_amount
    else:
        amount = 0
    ```
2.  **T+1制度影响**：我们的简单策略每天只交易一次，且卖出的是之前持有的股票，所以`total_amount`和`closable_amount`在此场景下一致。若策略涉及日内多次交易，则需仔细处理`closable_amount`。
3.  **策略评估**：一个策略不能仅凭短期回测结果判断好坏。需要在不同市场环境（牛市、熊市、震荡市）和不同时间周期（长、中、短线）中进行全面测试，观察其是否持续稳定地跑赢基准。

---

## 总结与进一步学习

本节课中我们一起学习了如何将一个简单的量化策略思路转化为可执行的代码。我们实现了基于价格和成本的买入、止盈、止损逻辑，并了解了如何获取行情数据、账户持仓信息以及如何执行交易订单。

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_49.png)

我们还介绍了基准收益的概念及其重要性，它是衡量策略超额收益的关键工具。

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_51.png)

这个策略虽然简单，但涵盖了量化策略开发的基本流程：**逻辑定义 -> 数据获取 -> 条件判断 -> 订单执行 -> 回测分析**。要深入学习和开发更有效的策略，你需要：
*   仔细阅读平台的API文档，熟悉所有可用的数据和函数。
*   学习基础的金融知识，理解更多科学的策略思想（如均值回归、动量效应等）。
*   进行更全面、更严谨的回测，理解策略在不同场景下的表现。

![](img/9082b2d6c97c73a37c9742a2e4aa7e92_53.png)

希望你能动手实践这个策略，并通过修改参数和逻辑来加深理解。