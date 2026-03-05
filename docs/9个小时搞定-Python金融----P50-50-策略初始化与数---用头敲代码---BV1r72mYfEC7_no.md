# Python金融量化分析：P50：策略初始化与数据读取 📊

![](img/87f933e41da79bffbc812b8ec1608c02_0.png)

在本节课中，我们将学习如何初始化一个量化交易策略，并读取用于因子打分选股所需的财务数据。我们将从指定股票池开始，逐步完成数据查询和预处理工作。

![](img/87f933e41da79bffbc812b8ec1608c02_2.png)

![](img/87f933e41da79bffbc812b8ec1608c02_4.png)

## 策略初始化

![](img/87f933e41da79bffbc812b8ec1608c02_6.png)

![](img/87f933e41da79bffbc812b8ec1608c02_8.png)

![](img/87f933e41da79bffbc812b8ec1608c02_10.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何为一个具体的选股策略进行初始化设置。

首先，我们需要在策略的 `context` 中指定股票池。股票池定义了策略进行选股的范围。

![](img/87f933e41da79bffbc812b8ec1608c02_12.png)

![](img/87f933e41da79bffbc812b8ec1608c02_14.png)

以下是初始化步骤：

![](img/87f933e41da79bffbc812b8ec1608c02_16.png)

1.  **指定股票池**：为了获得更好的回测效果，我们选择在沪深300指数的成分股中进行选股。这可以通过设置 `context.hs300` 来实现。
    ```python
    context.hs300 = ‘沪深300’
    ```

![](img/87f933e41da79bffbc812b8ec1608c02_18.png)

![](img/87f933e41da79bffbc812b8ec1608c02_20.png)

2.  **预留排名变量**：我们需要一个变量来存储后续计算出的股票排名结果。在 `context` 中预先定义一个变量，例如 `context.rank`，用于存放这个排名列表。
    ```python
    context.rank = None  # 稍后将在此存储股票排名
    ```

![](img/87f933e41da79bffbc812b8ec1608c02_22.png)

![](img/87f933e41da79bffbc812b8ec1608c02_24.png)

![](img/87f933e41da79bffbc812b8ec1608c02_26.png)

## 设置定时调仓

![](img/87f933e41da79bffbc812b8ec1608c02_28.png)

策略初始化完成后，我们需要设定策略的运行频率，即何时触发选股和调仓逻辑。

![](img/87f933e41da79bffbc812b8ec1608c02_30.png)

![](img/87f933e41da79bffbc812b8ec1608c02_32.png)

这里我们使用一个定时器，设定策略按月运行。`run_monthly` 函数接受两个参数：要执行的函数名和执行日期（1代表每月第一个交易日）。

![](img/87f933e41da79bffbc812b8ec1608c02_34.png)

![](img/87f933e41da79bffbc812b8ec1608c02_36.png)

```python
run_monthly(rebalance, 1)
```

![](img/87f933e41da79bffbc812b8ec1608c02_38.png)

## 定义调仓函数

![](img/87f933e41da79bffbc812b8ec1608c02_40.png)

![](img/87f933e41da79bffbc812b8ec1608c02_42.png)

设置好定时器后，我们需要定义具体的调仓函数 `rebalance`。这个函数是策略的核心，负责在每次调仓日执行选股和交易操作。

![](img/87f933e41da79bffbc812b8ec1608c02_44.png)

![](img/87f933e41da79bffbc812b8ec1608c02_46.png)

在 `rebalance` 函数中，最核心的任务是根据财务指标对股票进行评分和排序。为此，我们将创建一个专门的函数 `get_stocks` 来获取并处理数据。

![](img/87f933e41da79bffbc812b8ec1608c02_48.png)

![](img/87f933e41da79bffbc812b8ec1608c02_50.png)

```python
def rebalance(context):
    # 调用选股函数，获取排名靠前的股票列表
    stock_list = get_stocks(context)
    # 后续将在此处添加调仓交易逻辑
    pass
```

![](img/87f933e41da79bffbc812b8ec1608c02_52.png)

## 数据读取与处理

![](img/87f933e41da79bffbc812b8ec1608c02_54.png)

![](img/87f933e41da79bffbc812b8ec1608c02_56.png)

现在，我们来重点构建 `get_stocks` 函数。该函数负责查询所需的财务数据，并进行初步整理，为后续的因子打分做准备。

![](img/87f933e41da79bffbc812b8ec1608c02_58.png)

![](img/87f933e41da79bffbc812b8ec1608c02_60.png)

以下是数据读取的具体步骤：

1.  **构建查询语句**：我们需要查询多个财务指标。首先，定义越高越好的指标（如每股收益、净资产收益率等）。
    ```python
    # 构建查询越高越好指标的语句
    query_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)，越高越好
        fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)，越高越好
        fundamentals.financial_indicator.roa        # 总资产收益率 (ROA)，越高越好
    )
    ```

![](img/87f933e41da79bffbc812b8ec1608c02_62.png)

![](img/87f933e41da79bffbc812b8ec1608c02_64.png)

2.  **添加筛选条件**：通过 `filter` 方法，将查询范围限定在我们指定的股票池（如沪深300）内。
    ```python
    query_up = query_up.filter(
        fundamentals.stockcode.in_(context.hs300)
    )
    ```

![](img/87f933e41da79bffbc812b8ec1608c02_66.png)

![](img/87f933e41da79bffbc812b8ec1608c02_68.png)

![](img/87f933e41da79bffbc812b8ec1608c02_70.png)

3.  **执行查询并转换数据格式**：使用 `get_fundamentals` 执行查询，并对返回的 `DataFrame` 进行转置（`.T`），使股票代码成为行索引，指标成为列，便于后续处理。
    ```python
    df_up = get_fundamentals(query_up).T
    ```

![](img/87f933e41da79bffbc812b8ec1608c02_72.png)

4.  **查询越低越好的指标**：用同样的方法，查询另一组我们希望其数值越低越好的指标（如市净率、资产负债率等）。
    ```python
    # 构建查询越低越好指标的语句
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率，越低越好
        fundamentals.eod_derivative_indicator.pb_ratio,       # 市净率 (PB)，越低越好
        fundamentals.eod_derivative_indicator.market_cap      # 总市值，越低越好
    ).filter(
        fundamentals.stockcode.in_(context.hs300)
    )
    df_down = get_fundamentals(query_down).T
    ```

完成以上步骤后，我们将得到两个 `DataFrame`：`df_up` 包含越高越好的指标数据，`df_down` 包含越低越好的指标数据。它们的形状都是 `(300, 3)`，即300支股票，每支股票3个指标。

![](img/87f933e41da79bffbc812b8ec1608c02_74.png)

![](img/87f933e41da79bffbc812b8ec1608c02_76.png)

## 总结

![](img/87f933e41da79bffbc812b8ec1608c02_78.png)

![](img/87f933e41da79bffbc812b8ec1608c02_80.png)

本节课中我们一起学习了量化策略初始化的关键步骤。我们首先在 `context` 中设定了股票池和排名变量，然后通过 `run_monthly` 设置了按月调仓的定时任务。最后，我们详细实现了数据读取函数 `get_stocks`，学会了如何查询特定的财务指标数据，并将其处理成便于后续分析的格式。下一节，我们将基于这些数据，进行因子打分和股票排名。