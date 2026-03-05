# Python金融量化：P50：策略初始化与数据读取 📊

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_1.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_3.png)

在本节课中，我们将学习如何为一个量化交易策略进行初始化，并读取所需的金融数据。我们将从定义股票池开始，逐步构建一个包含数据查询和处理的完整函数框架。

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_5.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_7.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_9.png)

## 策略初始化

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_11.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_13.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_15.png)

首先，我们需要在策略的 `context` 对象中定义一些基础设置。这包括指定选股的范围和存储后续计算结果的变量。

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_17.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_19.png)

以下是初始化步骤：

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_21.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_23.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_25.png)

1.  **指定股票池**：为了获得更好的回测效果，我们将选股范围限定在沪深300指数成分股中。这定义了我们筛选股票的大本营。
    ```python
    context.hs300 = get_index_stocks('000300.XSHG')
    ```

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_27.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_29.png)

2.  **定义排名变量**：我们需要一个变量来存储根据因子评分计算出的股票排名结果，以便在调仓时使用。
    ```python
    context.rank = None
    ```

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_31.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_33.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_35.png)

3.  **设置定时调仓**：我们使用定时器来设定策略的执行频率。这里设置为每月调仓一次，并指定调仓时执行的函数为 `rebalance`。
    ```python
    run_monthly(rebalance, 1)
    ```

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_37.png)

上一节我们完成了策略的框架初始化，本节中我们来看看核心的调仓函数 `rebalance` 如何实现。

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_39.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_41.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_43.png)

## 构建调仓函数

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_45.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_47.png)

`rebalance` 函数是策略执行的核心，它负责在指定的时间点根据因子评分结果调整持仓。其最核心的任务是获取经过评分和排序后的股票列表。

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_49.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_51.png)

我们首先定义 `rebalance` 函数，并在其中调用一个自定义的 `get_stocks` 函数来获取评分后的股票。
```python
def rebalance(context):
    # 获取评分后的股票列表
    stock_list = get_stocks(context)
    # ... 后续的调仓操作（如卖出旧持仓、买入新股票）将在这里进行
```

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_53.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_55.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_57.png)

接下来，我们将重点实现 `get_stocks` 函数，它负责具体的因子数据查询与评分计算。

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_59.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_61.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_63.png)

## 实现数据查询与评分函数

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_65.png)

在 `get_stocks` 函数中，我们需要完成以下工作：查询所需的财务指标数据，并对这些数据进行处理，为后续的评分排序做准备。

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_67.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_69.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_71.png)

以下是 `get_stocks` 函数的核心步骤：

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_73.png)

1.  **构建查询语句 (Query)**：我们需要明确要查询哪些财务指标。这里以三个“越高越好”的指标和三个“越低越好”的指标为例。
    *   **越高越好型指标**：例如每股收益 (EPS)、净资产收益率 (ROE)、投入资本回报率 (ROIC)。我们希望这些值越大，股票得分越高。
    *   **越低越好型指标**：例如资产负债率、市净率 (PB)、市值。我们希望这些值越小，股票得分越高。

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_75.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_77.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_79.png)

2.  **执行数据查询**：使用 `get_fundamentals` 函数执行查询，并添加过滤器，确保只查询我们股票池（沪深300）中的股票。

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_81.png)

3.  **数据格式转换**：查询返回的 `DataFrame` 通常需要转置（`.T`），以便让股票代码成为行索引，指标成为列，方便后续按股票进行横向计算。

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_83.png)

让我们开始编写代码。首先，查询“越高越好”的指标组：
```python
def get_stocks(context):
    # 1. 查询“越高越好”的指标
    query_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益
        fundamentals.financial_indicator.roe,       # 净资产收益率
        fundamentals.financial_indicator.roic       # 投入资本回报率
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 过滤：仅在沪深300内
    )
    df_up = get_fundamentals(query_up).T # 执行查询并转置数据框

    # 2. 查询“越低越好”的指标
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio,       # 市净率(PB)
        fundamentals.eod_derivative_indicator.market_cap      # 总市值
    ).filter(
        fundamentals.stockcode.in_(context.hs300)
    )
    df_down = get_fundamentals(query_down).T

    # 此时，df_up 和 df_down 是两个DataFrame，行索引是股票代码，列是指标值。
    # ... 后续将在这里进行指标标准化、加权求和等评分操作
    return ranked_stocks # 返回排序后的股票列表
```
**注意**：`get_fundamentals(query).T` 是一个关键操作。`get_fundamentals` 默认返回的格式是“指标×股票”，转置后变成“股票×指标”，这更符合我们按股票进行分析的习惯。

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_85.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_87.png)

![](img/ffcde1c9e7ed4aa17d7e4097cf0e6542_89.png)

本节课中我们一起学习了量化策略初始化的关键步骤，包括设置股票池、定义调仓频率，并实现了数据查询函数的核心部分。我们掌握了如何使用 `query` 和 `get_fundamentals` 来获取特定的财务指标数据，并理解了数据转置的重要性。在接下来的课程中，我们将基于这些数据，完成因子标准化、加权评分以及最终的股票排序逻辑。