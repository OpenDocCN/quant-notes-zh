# 量化交易实战：P50：策略初始化与数据读取 📊

![](img/a55fe7fd3baf983e05179bf3828319cf_1.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_3.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_5.png)

在本节课中，我们将学习如何为一个量化交易策略进行初始化，并读取所需的股票基本面数据。这是构建一个基于因子打分选股策略的第一步。

![](img/a55fe7fd3baf983e05179bf3828319cf_7.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_9.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_11.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_13.png)

## 策略初始化

![](img/a55fe7fd3baf983e05179bf3828319cf_15.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_17.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_19.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何具体实现初始化步骤。首先，我们需要在策略的 `initialize` 函数中定义一些核心参数和设置。

![](img/a55fe7fd3baf983e05179bf3828319cf_21.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_23.png)

以下是初始化步骤的具体内容：

![](img/a55fe7fd3baf983e05179bf3828319cf_25.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_27.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_29.png)

*   **设定股票池**：为了获得更好的回测效果，我们将选股范围限定在沪深300指数的成分股中。这通过设置 `context.hs300` 变量来实现。
    ```python
    # 指定选股的大池子为沪深300成分股
    context.hs300 = index_components('000300.XSHG')
    ```
*   **定义排名变量**：我们创建一个变量 `context.rank_stocks` 来存储后续通过因子打分得到的股票排名结果，以便在调仓时使用。
    ```python
    # 初始化一个变量，用于存储股票的排名情况
    context.rank_stocks = None
    ```
*   **设置定时调仓**：使用 `run_monthly` 函数设置按月调仓。这里指定在每月第一个交易日运行名为 `rebalance` 的调仓函数。
    ```python
    # 设置按月调仓，每月第一个交易日执行rebalance函数
    run_monthly(rebalance, 1)
    ```

![](img/a55fe7fd3baf983e05179bf3828319cf_31.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_33.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_35.png)

## 定义调仓函数框架

![](img/a55fe7fd3baf983e05179bf3828319cf_37.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_39.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_41.png)

初始化完成后，我们需要定义核心的调仓函数 `rebalance`。这个函数的核心任务是获取经过评分排序后的股票列表。

![](img/a55fe7fd3baf983e05179bf3828319cf_43.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_45.png)

以下是调仓函数的初步框架：

![](img/a55fe7fd3baf983e05179bf3828319cf_47.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_49.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_51.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_53.png)

```python
def rebalance(context):
    # 调仓函数的核心是获取评分后的股票列表
    # 我们将通过一个自定义函数 get_stocks 来实现
    context.rank_stocks = get_stocks(context)
```

![](img/a55fe7fd3baf983e05179bf3828319cf_55.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_57.png)

## 构建数据获取函数

![](img/a55fe7fd3baf983e05179bf3828319cf_59.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_61.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_63.png)

上一节我们搭建了调仓函数的框架，本节中我们来看看如何构建 `get_stocks` 函数来获取和准备因子数据。这是因子打分模型的数据基础。

![](img/a55fe7fd3baf983e05179bf3828319cf_65.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_67.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_69.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_71.png)

我们首先需要从数据库中查询所需的股票基本面指标。我们将指标分为两类：“越高越好”型（如每股收益）和“越低越好”型（如市净率）。

![](img/a55fe7fd3baf983e05179bf3828319cf_73.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_75.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_77.png)

以下是数据查询的具体步骤：

![](img/a55fe7fd3baf983e05179bf3828319cf_79.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_81.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_83.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_85.png)

*   **查询“越高越好”型指标**：我们查询每股收益（EPS）、净资产收益率（ROE）和投入资本回报率（ROIC）。这些指标值越大，通常代表公司基本面越好。
    ```python
    # 构建查询语句，获取越高越好的指标
    q_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)
        fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)
        fundamentals.financial_indicator.roic       # 投入资本回报率 (ROIC)
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 过滤条件：股票在沪深300池子中
    )
    # 执行查询并转换数据格式（行转列，方便处理）
    df_up = get_fundamentals(q_up).T
    ```
*   **查询“越低越好”型指标**：我们查询资产负债率、市净率（PB）和总市值。对于估值和风险类指标，通常值越小越有吸引力。
    ```python
    # 构建查询语句，获取越低越好的指标
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio,       # 市净率 (PB)
        fundamentals.eod_derivative_indicator.market_cap      # 总市值
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 过滤条件：股票在沪深300池子中
    )
    # 执行查询并转换数据格式
    df_down = get_fundamentals(q_down).T
    ```

![](img/a55fe7fd3baf983e05179bf3828319cf_87.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_89.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_91.png)

**请注意一个关键细节**：` .T ` 操作（矩阵转置）应在 `get_fundamentals` 函数执行之后进行，目的是将返回的 `DataFrame` 的行列互换，使得每一行对应一只股票，每一列对应一个指标，这样更便于后续的排序和计算操作。

![](img/a55fe7fd3baf983e05179bf3828319cf_93.png)

此时，我们得到了两个 `DataFrame`：
*   `df_up`：形状约为 `(300, 3)`，包含300只股票的3个“高优”指标。
*   `df_down`：形状约为 `(300, 3)`，包含300只股票的3个“低优”指标。

![](img/a55fe7fd3baf983e05179bf3828319cf_95.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_97.png)

![](img/a55fe7fd3baf983e05179bf3828319cf_99.png)

本节课中我们一起学习了量化策略的初始化流程，包括设置股票池、定义调仓周期，以及如何从数据库查询并预处理多类基本面因子数据。我们已经成功获取了用于后续打分的原始数据，为下一步的因子标准化和综合评分打下了基础。