# 量化金融分析师.AQF：06：优矿策略_小市值因子策略

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_0.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_1.png)

## 概述
在本节课中，我们将学习如何在优矿平台上构建并回测一个经典的小市值因子策略。我们将从策略逻辑讲起，逐步拆解代码实现，并分析回测结果中可能遇到的问题。

## 策略逻辑与框架
上一节我们介绍了优矿平台的基本功能，本节中我们来看看如何实现一个具体的策略。小市值策略的核心逻辑非常简单：**每月初，买入全A股市场中市值最小的20只股票，并等权重持有（每只股票占5%的仓位），每月调仓一次**。

这个策略在历史上（如2013-2014年）曾非常有效，因为小市值股票往往能获得显著的超额收益（Alpha）。

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_3.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_5.png)

我们的策略代码主要包含以下几个部分：
1.  **参数设置**：定义回测时间、股票池、基准、调仓频率等。
2.  **账户配置**：设置初始资金、手续费等。
3.  **策略初始化**：通常用于定义全局变量，本例中无需特殊操作。
4.  **策略主逻辑**：在每次调仓日执行的买卖操作。

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_7.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_9.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_11.png)

以下是策略的核心代码框架：
```python
# 1. 参数设置
start = ‘2013-01-01’
end = ‘2014-01-01’
universe = DynamicUniverse(‘A’)
# 筛选市值最小的20只股票
universe = universe.apply_filter(Factor(‘market_cap’).nsmall(20))
benchmark = ‘HS300’
freq = ‘d’
refresh_rate = Monthly(1)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_13.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_15.png)

# 2. 账户配置
accounts = {
    ‘fantasy_account’: AccountConfig(account_type=‘security’, capital_base=1000000)
}

# 3. 策略初始化
def initialize(context):
    pass

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_17.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_19.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_21.png)

# 4. 策略主逻辑
def handle_data(context):
    # 获取账户和当前股票池
    account = context.get_account(‘fantasy_account’)
    current_universe = context.get_universe()
    # 清空现有持仓
    account.close_all_positions()
    # 等权重买入新股票池中的所有股票
    for stock in current_universe:
        account.order_percent(stock, 0.05)
```

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_23.png)

## 代码详解与注意事项
上一节我们展示了策略的整体框架，本节中我们来详细解读关键代码并讨论一些实践中的细节。

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_25.png)

### 动态股票池与因子筛选
策略的第一步是确定每月要交易的股票。我们使用 `DynamicUniverse(‘A’)` 来获取全A股动态股票池，它会自动处理新上市和退市的股票。接着，我们使用 `apply_filter` 方法结合市值因子进行筛选。

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_27.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_29.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_31.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_33.png)

以下是筛选市值最小20只股票的方法：
```python
universe = universe.apply_filter(Factor(‘market_cap’).nsmall(20))
```
这行代码意味着，在每个调仓日（每月初），系统会自动计算所有A股的市值，并选出最小的20只，构成当月的交易标的。

### 策略主逻辑执行
`handle_data` 函数是策略的核心，它根据 `refresh_rate` 设定的频率（本例为每月）被调用。其执行流程如下：
1.  **获取账户与股票池**：通过 `context.get_account` 和 `context.get_universe` 获取当前账户状态和本月筛选出的股票列表。
2.  **调仓操作**：先清空所有现有持仓 `account.close_all_positions()`，然后遍历新的股票列表，对每只股票执行 `order_percent(stock, 0.05)`，即买入至占总资产的5%。

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_35.png)

**关于 `order_percent` 与 `order_percent_to` 的选择**：
*   `order_percent(stock, 0.05)`：**买入**相当于总资产5%的该股票。
*   `order_percent_to(stock, 0.05)`：调整该股票的仓位**至**总资产的5%（可能涉及先卖后买）。
在简单的等权重月度换仓策略中，使用 `order_percent` 更为直观和稳健，可以避免因涨跌停、停牌导致 `order_percent_to` 产生大量不必要的微小调仓订单。

## 回测结果分析与常见问题
运行上述策略，在2013-01-01至2014-01-01的回测期内，该策略年化收益率可达63.3%，而同期沪深300基准收益为负，展现了显著的Alpha。

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_37.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_39.png)

然而，查看回测详情中的交易记录，会发现实际成交的股票数量有时并非精确的20只。这主要源于现实交易的限制：

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_41.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_43.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_45.png)

以下是导致成交数量波动的几个主要原因：
1.  **股票停牌**：在调仓日，如果股票停牌，则无法买入或卖出。
2.  **涨跌停限制**：想买入的股票涨停则无法买入，想卖出的股票跌停则无法卖出。
3.  **资金约束**：若上一期有股票因故未能卖出（如跌停），它仍占用部分资金，可能导致本期买入新股票时资金不足，无法完全成交。

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_47.png)

因此，在实际应用中，策略代码需要更健壮，例如：
*   在筛选股票池时预先剔除停牌股票。
*   对订单成交结果进行判断和处理。
*   考虑更优的换仓逻辑，如只交易发生变化的股票，以降低交易成本。

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_49.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_51.png)

![](img/586ecf3d7bef532047cdd7b7bd2de2b4_53.png)

## 总结
本节课中我们一起学习了如何在优矿平台上实现一个经典的小市值因子策略。我们从策略逻辑入手，逐步构建了代码，并分析了回测结果。虽然这个策略逻辑简单，但在历史回测中表现优异。更重要的是，我们通过这个实例，掌握了优矿策略开发的基本流程：**设置参数、筛选股票、编写主逻辑**。同时，我们也认识到将策略思想转化为实际代码时，必须考虑真实市场环境中的各种限制（如停牌、涨跌停），这对构建健壮的量化策略至关重要。