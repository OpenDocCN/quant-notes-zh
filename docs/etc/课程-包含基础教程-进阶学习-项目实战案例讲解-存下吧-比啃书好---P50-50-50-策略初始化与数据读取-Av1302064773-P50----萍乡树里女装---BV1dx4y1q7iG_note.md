# 量化交易教程：P50：策略初始化与数据读取 📊

![](img/c1b95a4b9f0d69c3b91821331855b278_1.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_2.png)

在本节课中，我们将学习如何为一个量化交易策略进行初始化，并读取用于因子打分选股所需的财务数据。我们将从定义股票池开始，逐步完成数据查询与获取的代码编写。

![](img/c1b95a4b9f0d69c3b91821331855b278_4.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_5.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_6.png)

## 策略初始化

![](img/c1b95a4b9f0d69c3b91821331855b278_8.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_9.png)

上一节我们介绍了因子打分选股的基本概念，本节中我们来看看如何为策略搭建基础框架。首先，我们需要在策略的 `initialize` 函数中指定股票池和调仓逻辑。

![](img/c1b95a4b9f0d69c3b91821331855b278_11.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_12.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_14.png)

以下是初始化步骤：

![](img/c1b95a4b9f0d69c3b91821331855b278_16.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_18.png)

1.  **指定股票池**：为了获得更好的策略效果，我们选择在沪深300指数的成分股中进行选股。
    ```python
    # 在initialize函数中指定股票池
    g.pool = '000300.XSHG'  # 沪深300指数代码
    ```

![](img/c1b95a4b9f0d69c3b91821331855b278_19.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_20.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_22.png)

2.  **定义排名存储变量**：创建一个变量，用于存储后续计算出的股票排名结果，以便在调仓时使用。
    ```python
    # 用于存储股票排名结果的变量
    g.rank = None
    ```

![](img/c1b95a4b9f0d69c3b91821331855b278_24.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_26.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_27.png)

3.  **设置定时调仓**：使用 `run_monthly` 函数设置按月调仓，并指定调仓时执行的函数。
    ```python
    # 设置按月调仓，每月第一个交易日执行rebalance函数
    run_monthly(rebalance, 1)
    ```

![](img/c1b95a4b9f0d69c3b91821331855b278_28.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_30.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_32.png)

## 数据读取与处理

![](img/c1b95a4b9f0d69c3b91821331855b278_34.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_35.png)

初始化框架搭建好后，接下来我们需要在调仓函数中获取数据。核心是编写一个 `get_stocks` 函数，用于查询并获取我们所需的财务指标数据。

![](img/c1b95a4b9f0d69c3b91821331855b278_36.png)

以下是数据读取步骤：

![](img/c1b95a4b9f0d69c3b91821331855b278_38.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_39.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_40.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_41.png)

1.  **构建查询语句 (Query)**：我们需要查询多个财务指标。首先，定义我们希望“数值越高越好”的指标。
    ```python
    # 查询“越高越好”的指标
    query_up = query(
        fundamentals.eod_derivative_indicator.eps,  # 每股收益 (EPS)，越高越好
        fundamentals.financial_indicator.roe,        # 净资产收益率 (ROE)，越高越好
        fundamentals.financial_indicator.roa         # 总资产收益率 (ROA)，越高越好
    )
    ```

![](img/c1b95a4b9f0d69c3b91821331855b278_43.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_44.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_45.png)

2.  **添加过滤条件**：在查询中，我们只关心属于我们股票池（沪深300）的股票。
    ```python
    # 添加过滤条件，仅选择股票池内的股票
    query_up = query_up.filter(
        fundamentals.income_statement.stockcode.in_(g.pool)
    )
    ```

![](img/c1b95a4b9f0d69c3b91821331855b278_46.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_47.png)

3.  **执行查询并转换数据格式**：使用 `get_fundamentals` 执行查询，并通过 `.T` 进行转置，使数据框的行是股票，列是指标，便于后续处理。
    ```python
    # 执行查询并转置DataFrame
    df_up = get_fundamentals(query_up).T
    ```

![](img/c1b95a4b9f0d69c3b91821331855b278_49.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_51.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_53.png)

4.  **查询“越低越好”的指标**：用同样的方法，查询我们希望“数值越低越好”的指标，例如估值和杠杆指标。
    ```python
    # 查询“越低越好”的指标
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset,    # 资产负债率，越低越好
        fundamentals.eod_derivative_indicator.pb_ratio,    # 市净率 (PB)，越低越好
        fundamentals.eod_derivative_indicator.market_cap   # 总市值，通常越小越灵活（视策略而定）
    ).filter(
        fundamentals.income_statement.stockcode.in_(g.pool)
    )
    df_down = get_fundamentals(query_down).T
    ```

![](img/c1b95a4b9f0d69c3b91821331855b278_54.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_56.png)

通过以上步骤，我们得到了两个 `DataFrame`：`df_up` 包含了越高越好的因子数据，`df_down` 包含了越低越好的因子数据。每个 `DataFrame` 的形状大致为 `(300, 3)`，代表约300支股票和3个因子。

![](img/c1b95a4b9f0d69c3b91821331855b278_58.png)

## 总结

![](img/c1b95a4b9f0d69c3b91821331855b278_59.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_61.png)

![](img/c1b95a4b9f0d69c3b91821331855b278_62.png)

本节课中我们一起学习了量化策略初始化的关键步骤和数据读取方法。我们首先在 `initialize` 中定义了股票池和调仓频率，然后重点构建了 `get_stocks` 函数，通过 `query` 和 `get_fundamentals` 接口，分别获取了“越高越好”和“越低越好”两类财务指标数据，为下一步的因子打分与股票排名做好了数据准备。