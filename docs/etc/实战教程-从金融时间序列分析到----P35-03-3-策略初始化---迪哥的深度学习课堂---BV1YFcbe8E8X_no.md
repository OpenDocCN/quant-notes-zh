# Python金融分析与量化交易实战教程：P35：03-3-策略初始化与数据读取 📊

在本节课中，我们将学习如何初始化一个量化交易策略，并读取用于因子选股的基础金融数据。我们将从定义股票池开始，逐步完成策略的框架搭建和数据获取。

![](img/988092a1ee83e018e953ec143a3b81b1_1.png)

![](img/988092a1ee83e018e953ec143a3b81b1_3.png)

## 策略初始化

![](img/988092a1ee83e018e953ec143a3b81b1_5.png)

![](img/988092a1ee83e018e953ec143a3b81b1_7.png)

上一节我们介绍了策略的整体思路，本节中我们来看看如何初始化策略环境。首先，我们需要在策略的 `initialize` 方法中定义一些基础设置。

以下是初始化步骤：

![](img/988092a1ee83e018e953ec143a3b81b1_9.png)

1.  **指定股票池**：为了获得更好的演示效果，我们选择在沪深300指数的成分股中进行选股。
    ```python
    # 在initialize方法中定义股票池
    g.pool = '000300.XSHG'  # 沪深300指数代码
    ```

![](img/988092a1ee83e018e953ec143a3b81b1_11.png)

2.  **定义存储变量**：我们需要一个变量来存储后续计算出的股票排名。
    ```python
    # 用于存储股票排名结果的变量
    g.rank_stocks = None
    ```

![](img/988092a1ee83e018e953ec143a3b81b1_13.png)

![](img/988092a1ee83e018e953ec143a3b81b1_15.png)

3.  **设置调仓定时器**：我们设定策略按月进行调仓。`run_monthly` 函数用于设置定时任务。
    ```python
    # 设置按月调仓，每月第一个交易日运行rebalance函数
    run_monthly(rebalance, 1)
    ```

## 数据读取与处理

![](img/988092a1ee83e018e953ec143a3b81b1_17.png)

![](img/988092a1ee83e018e953ec143a3b81b1_19.png)

初始化完成后，我们需要在调仓函数中读取和处理数据。本节我们将重点实现数据获取部分。

![](img/988092a1ee83e018e953ec143a3b81b1_21.png)

以下是数据读取的核心步骤：

![](img/988092a1ee83e018e953ec143a3b81b1_23.png)

1.  **定义数据获取函数**：我们创建一个名为 `get_stocks` 的函数，专门用于获取和预处理因子数据。
    ```python
    def get_stocks(context):
        # 此函数用于获取并处理因子数据
    ```

![](img/988092a1ee83e018e953ec143a3b81b1_25.png)

![](img/988092a1ee83e018e953ec143a3b81b1_27.png)

2.  **构建查询语句 (Query)**：我们需要查询多个基本面指标。这里以三个“越高越好”的因子和三个“越低越好”的因子为例。
    ```python
    # 构建查询语句，获取“越高越好”的因子
    q_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)
        fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)
        fundamentals.financial_indicator.roa        # 总资产收益率 (ROA)
    ).filter(
        fundamentals.stockcode.in_(g.pool) # 过滤，只选择股票池中的股票
    )
    ```

![](img/988092a1ee83e018e953ec143a3b81b1_29.png)

3.  **执行查询并转换数据**：使用 `get_fundamentals` 执行查询，并对返回的数据进行转置，以便股票代码成为行索引。
    ```python
    # 获取数据并转置，使每一行代表一只股票
    df_up = get_fundamentals(q_up).T
    ```

![](img/988092a1ee83e018e953ec143a3b81b1_31.png)

4.  **获取“越低越好”的因子**：用同样的方法获取另一组因子，例如资产负债率、市净率(PB)和市值。
    ```python
    # 构建查询语句，获取“越低越好”的因子
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率 (PB)
        fundamentals.eod_derivative_indicator.market_cap # 总市值
    ).filter(
        fundamentals.stockcode.in_(g.pool)
    )
    df_down = get_fundamentals(q_down).T
    ```

![](img/988092a1ee83e018e953ec143a3b81b1_33.png)

5.  **数据初步观察**：此时，`df_up` 和 `df_down` 都是 `DataFrame`，其形状应为 `(300, 3)`，代表300只股票和3个因子。

![](img/988092a1ee83e018e953ec143a3b81b1_35.png)

## 函数定义与整合

![](img/988092a1ee83e018e953ec143a3b81b1_37.png)

现在，我们将上述步骤整合到 `get_stocks` 函数和调仓函数 `rebalance` 的框架中。

![](img/988092a1ee83e018e953ec143a3b81b1_39.png)

![](img/988092a1ee83e018e953ec143a3b81b1_41.png)

以下是 `get_stocks` 函数的完整代码框架：

![](img/988092a1ee83e018e953ec143a3b81b1_43.png)

```python
def get_stocks(context):
    """
    获取并返回处理好的因子数据。
    返回值: df_up (越高越好因子), df_down (越低越好因子)
    """
    # 1. 查询“越高越好”的因子
    q_up = query(
        fundamentals.eod_derivative_indicator.eps,
        fundamentals.financial_indicator.roe,
        fundamentals.financial_indicator.roa
    ).filter(
        fundamentals.stockcode.in_(g.pool)
    )
    df_up = get_fundamentals(q_up).T

    # 2. 查询“越低越好”的因子
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset,
        fundamentals.eod_derivative_indicator.pb_ratio,
        fundamentals.eod_derivative_indicator.market_cap
    ).filter(
        fundamentals.stockcode.in_(g.pool)
    )
    df_down = get_fundamentals(q_down).T

    return df_up, df_down
```

在 `rebalance` 函数中，我们将调用这个函数来获取数据。

![](img/988092a1ee83e018e953ec143a3b81b1_45.png)

```python
def rebalance(context):
    """
    月度调仓函数
    """
    # 调用函数获取因子数据
    df_up, df_down = get_stocks(context)
    # 后续将在这里进行因子打分和选股逻辑
    # ...
```

![](img/988092a1ee83e018e953ec143a3b81b1_47.png)

![](img/988092a1ee83e018e953ec143a3b81b1_49.png)

本节课中我们一起学习了量化策略的初始化步骤，包括设置股票池、定义变量、安排调仓周期，并实现了读取多维度基本面因子数据的功能。我们已经成功获取了用于后续打分的原始数据，为下一节的因子综合评分打下了基础。