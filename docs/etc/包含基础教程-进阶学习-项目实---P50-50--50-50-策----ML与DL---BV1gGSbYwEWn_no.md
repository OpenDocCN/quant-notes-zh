# 量化交易教程：P50：策略初始化与数据读取 📊

![](img/6a8c372a22e0871f6998bf376204b1f6_1.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_3.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_5.png)

在本节课中，我们将学习如何为一个量化选股策略进行初始化，并读取所需的基础财务数据。这是构建一个完整策略的第一步。

![](img/6a8c372a22e0871f6998bf376204b1f6_7.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_9.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_11.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_13.png)

## 策略初始化

![](img/6a8c372a22e0871f6998bf376204b1f6_15.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_17.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何在一个新的策略文件中进行初始化设置。

![](img/6a8c372a22e0871f6998bf376204b1f6_19.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_21.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_23.png)

首先，我们需要在策略的 `initialize` 函数中定义策略的上下文和基本参数。

![](img/6a8c372a22e0871f6998bf376204b1f6_25.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_27.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_29.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_31.png)

以下是初始化步骤：

![](img/6a8c372a22e0871f6998bf376204b1f6_33.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_35.png)

1.  **指定股票池**：我们需要确定从哪个股票集合中筛选股票。为了使策略表现更直观，我们选择沪深300指数成分股作为初始股票池。
    ```python
    def initialize(context):
        # 设定股票池为沪深300指数成分股
        g.pool = '000300.XSHG'
    ```

![](img/6a8c372a22e0871f6998bf376204b1f6_37.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_39.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_41.png)

2.  **定义存储变量**：为了在后续调仓时使用，我们需要在上下文（`context` 或 `g`）中预先定义一个变量，用于存储股票的排名结果。
    ```python
        # 预定义变量，用于存储股票排名
        g.rank_list = None
    ```

![](img/6a8c372a22e0871f6998bf376204b1f6_43.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_45.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_47.png)

3.  **设置定时调仓**：我们需要设定策略调仓的频率。这里我们选择按月调仓，并指定调仓时运行的函数。
    ```python
        # 设置按月调仓，每月第一个交易日运行 rebalance 函数
        run_monthly(rebalance, 1)
    ```

![](img/6a8c372a22e0871f6998bf376204b1f6_49.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_51.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_53.png)

## 数据读取与处理

![](img/6a8c372a22e0871f6998bf376204b1f6_55.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_57.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_59.png)

初始化完成后，下一步是定义调仓函数 `rebalance` 以及其中获取和准备数据的过程。

![](img/6a8c372a22e0871f6998bf376204b1f6_61.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_63.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_65.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_67.png)

在 `rebalance` 函数中，最核心的任务是根据财务指标对股票进行评分。为此，我们首先需要获取这些指标数据。

![](img/6a8c372a22e0871f6998bf376204b1f6_69.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_71.png)

以下是数据读取步骤：

![](img/6a8c372a22e0871f6998bf376204b1f6_73.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_75.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_77.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_79.png)

1.  **定义数据获取函数**：我们创建一个名为 `get_stocks` 的函数，专门用于查询和获取所需的财务数据。
    ```python
    def get_stocks(context):
        # 此函数用于获取并处理财务数据
        ...
    ```

![](img/6a8c372a22e0871f6998bf376204b1f6_81.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_83.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_85.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_87.png)

2.  **构建查询语句 (Query)**：在 `get_stocks` 函数内部，我们使用 `query` 语句来指定需要查询的财务指标。我们以六个常见指标为例，分为两类：
    *   **越高越好型指标**：例如每股收益 (EPS)、净资产收益率 (ROE)、投入资本回报率 (ROIC)。
    *   **越低越好型指标**：例如资产负债率、市净率 (PB)、总市值。

    对应的查询代码结构如下：
    ```python
    # 查询“越高越好”的指标
    q_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益
        fundamentals.financial_indicator.roe,       # 净资产收益率
        fundamentals.financial_indicator.roic       # 投入资本回报率
    ).filter(
        fundamentals.stockcode.in_(g.pool) # 过滤股票池
    )
    
    # 查询“越低越好”的指标
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio,       # 市净率(PB)
        fundamentals.eod_derivative_indicator.market_cap      # 总市值
    ).filter(
        fundamentals.stockcode.in_(g.pool) # 过滤股票池
    )
    ```

![](img/6a8c372a22e0871f6998bf376204b1f6_89.png)

3.  **执行查询并转换数据**：使用 `get_fundamentals` 函数执行查询，并对返回的 `DataFrame` 进行转置，使股票代码成为行索引，指标成为列，便于后续处理。
    ```python
    # 获取数据并转置
    df_up = get_fundamentals(q_up).T
    df_down = get_fundamentals(q_down).T
    ```
    此时，`df_up` 和 `df_down` 是两个 `DataFrame`，其形状大致为 `(300, 3)`，分别包含了沪深300成分股的三项“高优”指标和三项目“低优”指标。

![](img/6a8c372a22e0871f6998bf376204b1f6_91.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_93.png)

![](img/6a8c372a22e0871f6998bf376204b1f6_95.png)

本节课中我们一起学习了量化策略的初始化设置与财务数据读取。我们完成了指定股票池、设定调仓频率、以及通过 `query` 和 `get_fundamentals` 获取关键财务数据的过程。这些数据是后续进行因子打分和选股的基础。下一节，我们将基于这些数据，开始构建具体的股票评分模型。