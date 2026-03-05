# Python金融量化与股票交易：P50：策略初始化与数据读取 📊

![](img/d46322adadfe8336c948909087cec5c3_1.png)

![](img/d46322adadfe8336c948909087cec5c3_3.png)

在本节课中，我们将学习如何为一个因子打分选股策略进行初始化，并读取所需的股票基本面数据。我们将从定义策略的触发条件、设定股票池开始，逐步完成数据查询与准备的核心步骤。

![](img/d46322adadfe8336c948909087cec5c3_5.png)

![](img/d46322adadfe8336c948909087cec5c3_7.png)

![](img/d46322adadfe8336c948909087cec5c3_9.png)

![](img/d46322adadfe8336c948909087cec5c3_11.png)

## 策略初始化

![](img/d46322adadfe8336c948909087cec5c3_13.png)

![](img/d46322adadfe8336c948909087cec5c3_15.png)

![](img/d46322adadfe8336c948909087cec5c3_17.png)

上一节我们介绍了因子打分选股的基本概念，本节中我们来看看如何为策略搭建基础框架。首先，我们需要在策略的 `context` 对象中定义一些关键变量。

![](img/d46322adadfe8336c948909087cec5c3_19.png)

![](img/d46322adadfe8336c948909087cec5c3_21.png)

以下是初始化步骤：

![](img/d46322adadfe8336c948909087cec5c3_23.png)

![](img/d46322adadfe8336c948909087cec5c3_25.png)

1.  **指定股票池**：为了获得更好的策略表现，我们将股票池限定在沪深300指数成分股中。这有助于我们聚焦于流动性较好、市值较大的股票。
    ```python
    # 在context中定义股票池
    context.hs300 = ‘沪深300’
    ```

![](img/d46322adadfe8336c948909087cec5c3_27.png)

![](img/d46322adadfe8336c948909087cec5c3_29.png)

![](img/d46322adadfe8336c948909087cec5c3_31.png)

2.  **定义排名变量**：我们需要一个变量来存储股票的排名结果，以便后续调仓时使用。
    ```python
    # 在context中定义用于存储排名的变量
    context.rank = None
    ```

![](img/d46322adadfe8336c948909087cec5c3_33.png)

![](img/d46322adadfe8336c948909087cec5c3_35.png)

3.  **设置定时调仓器**：策略需要定期运行选股和调仓逻辑。这里我们设置为每月调仓一次。
    ```python
    # 设置每月调仓的定时器
    scheduler.run_monthly(rebalance, 1)
    ```

![](img/d46322adadfe8336c948909087cec5c3_37.png)

![](img/d46322adadfe8336c948909087cec5c3_39.png)

![](img/d46322adadfe8336c948909087cec5c3_41.png)

## 定义调仓函数

![](img/d46322adadfe8336c948909087cec5c3_43.png)

![](img/d46322adadfe8336c948909087cec5c3_45.png)

初始化完成后，我们需要定义核心的调仓函数 `rebalance`。这个函数将在每个调仓日被触发，执行选股和交易操作。

![](img/d46322adadfe8336c948909087cec5c3_47.png)

![](img/d46322adadfe8336c948909087cec5c3_49.png)

以下是 `rebalance` 函数的框架：

![](img/d46322adadfe8336c948909087cec5c3_51.png)

![](img/d46322adadfe8336c948909087cec5c3_53.png)

![](img/d46322adadfe8336c948909087cec5c3_55.png)

![](img/d46322adadfe8336c948909087cec5c3_57.png)

```python
def rebalance(context):
    # 核心任务是获取评分后的股票列表
    ranked_stocks = get_stocks(context)
    # 后续将基于ranked_stocks进行交易
```

![](img/d46322adadfe8336c948909087cec5c3_59.png)

![](img/d46322adadfe8336c948909087cec5c3_61.png)

## 实现数据读取函数

![](img/d46322adadfe8336c948909087cec5c3_63.png)

![](img/d46322adadfe8336c948909087cec5c3_65.png)

调仓函数依赖于 `get_stocks` 函数来获取评分数据。现在，我们来实现这个函数，其核心是查询并处理股票的基本面数据。

![](img/d46322adadfe8336c948909087cec5c3_67.png)

![](img/d46322adadfe8336c948909087cec5c3_69.png)

![](img/d46322adadfe8336c948909087cec5c3_71.png)

以下是 `get_stocks` 函数的数据读取步骤：

![](img/d46322adadfe8336c948909087cec5c3_73.png)

1.  **构建查询语句**：我们需要查询多个基本面指标。根据之前的分析，我们选取了六个指标，分为“越高越好”和“越低越好”两类。
    ```python
    # 定义查询语句，选取多个基本面指标
    query = (
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (越高越好)
        fundamentals.financial_indicator.roe,       # 净资产收益率 (越高越好)
        fundamentals.financial_indicator.roa,       # 总资产收益率 (越高越好)
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率 (越低越好)
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率PB (越低越好)
        fundamentals.eod_derivative_indicator.market_cap # 总市值 (越低越好)
    )
    ```

![](img/d46322adadfe8336c948909087cec5c3_75.png)

![](img/d46322adadfe8336c948909087cec5c3_77.png)

![](img/d46322adadfe8336c948909087cec5c3_79.png)

2.  **应用股票池过滤**：我们只对沪深300成分股进行查询。
    ```python
    # 添加过滤器，限定股票池
    filter = fundamentals.stockcode.in_(context.hs300)
    ```

![](img/d46322adadfe8336c948909087cec5c3_81.png)

3.  **执行查询并处理数据**：使用 `get_fundamentals` 函数执行查询。查询返回的数据格式需要转置，以便股票代码作为行索引，指标作为列。
    ```python
    # 执行查询，获取“越高越好”的指标数据
    df_up = get_fundamentals(query, filter=filter).T
    # 执行查询，获取“越低越好”的指标数据
    df_down = get_fundamentals(query, filter=filter).T
    ```
    注意：`.T` 操作（转置）应在 `get_fundamentals` 返回 `DataFrame` 之后进行，以确保数据结构正确。

![](img/d46322adadfe8336c948909087cec5c3_83.png)

![](img/d46322adadfe8336c948909087cec5c3_85.png)

## 总结

![](img/d46322adadfe8336c948909087cec5c3_87.png)

![](img/d46322adadfe8336c948909087cec5c3_89.png)

本节课中我们一起学习了因子打分选股策略的初始化与数据读取流程。我们首先在 `context` 中定义了股票池和排名变量，并设置了定时调仓器。然后，我们构建了 `rebalance` 函数框架和核心的 `get_stocks` 函数。在 `get_stocks` 函数中，我们详细演示了如何构建查询语句、应用过滤器，并正确获取和转置基本面数据，为后续的因子评分计算做好了数据准备。