# Python金融量化投资分析与股票交易：P52：因子选股策略-2 🧠

![](img/cb881689e43df265e7e501ce2db86815_1.png)

在本节课中，我们将学习如何实现一个具体的因子选股策略，即“小市值策略”。我们将使用Python获取财务数据，根据市值因子筛选股票，并实现定期调仓的逻辑。

![](img/cb881689e43df265e7e501ce2db86815_3.png)

## 概述

上一节我们介绍了因子选股的基本概念。本节中，我们将动手实现一个基于市值因子的选股策略。核心思路是：定期（如每月）查询所有股票的市值数据，买入市值最小的N只股票，并卖出不再符合持仓条件的股票。

## 实现步骤

### 1. 获取财务数据

![](img/cb881689e43df265e7e501ce2db86815_5.png)

![](img/cb881689e43df265e7e501ce2db86815_7.png)

因子选股策略需要基于财务数据（如市值）进行决策。与获取价格数据不同，财务数据需要通过专门的函数查询。

以下是查询财务数据的方法。我们使用 `get_fundamental` 函数，其核心参数是一个 `query` 对象，用于从数据库中筛选所需数据。市值数据存储在 `valuation` 表的 `market_cap` 列中。

```python
# 定义查询对象，筛选股票池中股票的代码和市值
q = query(
    valuation.code,
    valuation.market_cap
).filter(
    valuation.code.in_(get_index_stocks(‘000300.XSHG‘))
)
```

![](img/cb881689e43df265e7e501ce2db86815_9.png)

![](img/cb881689e43df265e7e501ce2db86815_11.png)

### 2. 定期执行调仓

![](img/cb881689e43df265e7e501ce2db86815_13.png)

![](img/cb881689e43df265e7e501ce2db86815_15.png)

策略不需要每天运行，而是定期（如每月）执行一次选股和调仓。这里介绍两种实现定期调仓的方法。

![](img/cb881689e43df265e7e501ce2db86815_17.png)

![](img/cb881689e43df265e7e501ce2db86815_19.png)

**方法一：使用计数器**
在 `handle_data` 函数中设置一个计数器，每运行一次加1，当计数值达到特定周期（如30天）时执行调仓逻辑。

![](img/cb881689e43df265e7e501ce2db86815_21.png)

**方法二：使用定时运行函数**
平台提供了 `run_monthly`、`run_weekly`、`run_daily` 等函数，可以更方便地设置定期任务。以下代码演示了如何每月在第一个交易日执行名为 `handle` 的函数。

```python
# 在 initialize 函数中绑定定期执行函数
run_monthly(handle, 1)

def handle(context):
    # 这里是每月调仓的逻辑
    log.info(‘Hello‘)
```

### 3. 执行选股与调仓逻辑

在定期执行的函数中，我们需要完成获取数据、排序选股、调整持仓等一系列操作。

以下是核心的调仓逻辑步骤。首先获取市值数据并排序，选出市值最小的N只股票作为目标持仓。然后，对比当前持仓与目标持仓，卖出不在目标中的股票，买入新增的股票。

1.  **获取并处理数据**：使用 `get_fundamental` 执行查询，返回一个DataFrame，包含股票代码和市值。
    ```python
    df = get_fundamentals(q)
    df = df[[‘code‘, ‘market_cap‘]]
    ```
2.  **排序并选择股票**：对DataFrame按市值升序排列（注意：平台使用的pandas版本较老，排序函数为 `sort` 而非 `sort_values`），并选取前N行。
    ```python
    df = df.sort(‘market_cap‘)
    df = df.iloc[:context.N]  # 假设 context.N = 20
    ```
3.  **确定调仓名单**：
    *   `to_hold`: 目标持仓的股票代码列表。
    *   遍历当前持仓 `context.portfolio.positions`，卖出不在 `to_hold` 列表中的股票。
    *   `to_buy`: 在 `to_hold` 中但当前未持有的股票列表，即需要买入的股票。
4.  **执行买卖操作**：
    *   将可用现金平均分配给 `to_buy` 列表中的每只股票。
    *   遍历 `to_buy` 列表，使用 `order_value` 函数买入相应金额的股票。
    ```python
    if len(to_buy) > 0:
        cash_per_stock = context.portfolio.available_cash / len(to_buy)
        for stock in to_buy:
            order_value(stock, cash_per_stock)
    ```

### 4. 策略优化与注意事项

![](img/cb881689e43df265e7e501ce2db86815_23.png)

实现基础策略后，还可以从多个角度进行优化，并注意处理实际交易中的细节问题。

![](img/cb881689e43df265e7e501ce2db86815_25.png)

以下是一些可能的优化方向和注意事项。通过调整参数和因子，可以进一步提升策略表现，同时需要处理如股票停牌等边界情况。

![](img/cb881689e43df265e7e501ce2db86815_27.png)

![](img/cb881689e43df265e7e501ce2db86815_29.png)

*   **参数调优**：尝试调整持仓股票数量 `N`（如10、20、30），观察不同参数对策略年化收益率、最大回撤等指标的影响。
*   **因子扩展**：除了市值，可以尝试其他财务指标（如市盈率PE、市净率PB）作为选股因子。
*   **股票池选择**：不仅可以在沪深300成分股中选股，也可以尝试在全A股范围内进行筛选（例如使用指数代码 `‘000002.XSHG‘`）。
*   **处理停牌**：在买入股票前，应检查该股票是否停牌。可以使用 `get_current_data` 获取股票的 `paused` 状态进行过滤，避免下单失败。
    ```python
    current_data = get_current_data()
    # 如果股票停牌，则跳过
    if current_data[stock].paused:
        continue
    ```

## 总结

![](img/cb881689e43df265e7e501ce2db86815_31.png)

![](img/cb881689e43df265e7e501ce2db86815_33.png)

本节课中我们一起学习了如何实现一个完整的因子选股策略。我们从获取财务数据开始，实现了定期调仓的机制，并完成了“买入市值最小N只股票”的核心逻辑。最后，我们还探讨了策略的优化方向和需要注意的实际交易问题，例如参数调优和处理股票停牌。这个“小市值策略”虽然逻辑简单，但历史回测表现出了不错的潜力，是学习量化策略的一个很好起点。