# 金融数据分析：P21：3-策略初始化与数据读取 📊

![](img/2cdd6fc4c3c117db784733594490aa4a_1.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_3.png)

在本节课中，我们将学习如何为一个量化交易策略进行初始化，并读取所需的金融数据。我们将从定义股票池开始，设置调仓定时器，并编写核心函数来获取和准备用于因子打分的财务指标数据。

![](img/2cdd6fc4c3c117db784733594490aa4a_5.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现策略的初始化与数据获取模块。

![](img/2cdd6fc4c3c117db784733594490aa4a_7.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_9.png)

## 策略初始化

![](img/2cdd6fc4c3c117db784733594490aa4a_11.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_13.png)

首先，我们需要在策略的 `initialize` 函数中定义一些基础设置。这包括指定选股的范围（股票池）以及设定策略调仓的频率。

![](img/2cdd6fc4c3c117db784733594490aa4a_15.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_17.png)

以下是初始化步骤：

![](img/2cdd6fc4c3c117db784733594490aa4a_19.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_21.png)

1.  **指定股票池**：为了获得更稳定的回测效果，我们选择在沪深300指数的成分股中进行选股。这通过设置 `context.universe` 来实现。
    ```python
    context.universe = '沪深300'
    ```

![](img/2cdd6fc4c3c117db784733594490aa4a_23.png)

2.  **预留排名变量**：我们预先在 `context` 中定义一个变量 `context.rank`，用于存储后续计算出的股票排名结果。
    ```python
    context.rank = None
    ```

![](img/2cdd6fc4c3c117db784733594490aa4a_25.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_27.png)

3.  **设置调仓定时器**：我们设定策略按月进行调仓。使用 `run_monthly` 函数，并指定调仓时执行的函数 `rebalance`。
    ```python
    run_monthly(rebalance, 1)
    ```

## 定义调仓函数

![](img/2cdd6fc4c3c117db784733594490aa4a_29.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_31.png)

调仓函数 `rebalance` 是策略运行的核心，它将在每个调仓日被调用。其首要任务是获取经过评分排序后的股票列表。

我们首先在 `rebalance` 函数中调用一个自定义函数 `get_stocks` 来获取股票排名。
```python
def rebalance(context):
    # 获取评分后的股票列表
    stock_list = get_stocks(context)
    # ... 后续调仓逻辑（如下单等）将在这里添加
```

![](img/2cdd6fc4c3c117db784733594490aa4a_33.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_35.png)

## 构建数据获取函数

![](img/2cdd6fc4c3c117db784733594490aa4a_37.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_39.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_41.png)

`get_stocks` 函数负责具体的因子数据查询与处理工作。其目标是获取沪深300成分股的多个财务指标，为后续的因子打分做准备。

![](img/2cdd6fc4c3c117db784733594490aa4a_43.png)

![](img/2cdd6fc4c3c117db784733594490aa4a_45.png)

以下是数据获取的具体步骤：

1.  **构建查询语句 (Query)**：我们需要查询多个财务指标。首先，定义我们希望指标“越高越好”的查询。
    ```python
    # 查询“越高越好”的指标
    q_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)，越高越好
        fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)，越高越好
        fundamentals.financial_indicator.roa        # 总资产收益率 (ROA)，越高越好
    )
    ```
    同时，定义另一组我们希望指标“越低越好”的查询。
    ```python
    # 查询“越低越好”的指标
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率，越低越好
        fundamentals.eod_derivative_indicator.pb_ratio,       # 市净率 (PB)，越低越好
        fundamentals.valuation.market_cap                     # 总市值，越低越好（小市值效应）
    )
    ```

2.  **应用股票池过滤**：在查询语句中，我们需要限定股票范围在沪深300内。
    ```python
    # 为两个查询都添加过滤条件
    q_up = q_up.filter(fundamentals.stockcode.in_(context.universe))
    q_down = q_down.filter(fundamentals.stockcode.in_(context.universe))
    ```

3.  **执行查询并转换数据格式**：使用 `get_fundamentals` 执行查询。获取到的 `DataFrame` 通常需要转置（`.T`），使股票代码成为行索引，指标成为列，便于后续处理。
    ```python
    # 执行查询并转置数据
    df_up = get_fundamentals(q_up).T  # “越高越好”指标的数据框
    df_down = get_fundamentals(q_down).T # “越低越好”指标的数据框
    ```
    此时，`df_up` 和 `df_down` 都是形状约为 `(300, 3)` 的 `DataFrame`，分别包含了300只股票对应的3个财务指标。

本节课中我们一起学习了量化策略初始化的关键步骤：设置股票池和调仓频率，并深入实现了数据获取函数。我们通过构建复杂的查询语句，成功获取了沪深300成分股的多类财务指标（如EPS、ROE、PB等），并将其整理为便于后续分析的数据格式。下一节，我们将基于这些数据，开始进行因子的标准化与综合打分。