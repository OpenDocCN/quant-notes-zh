# Python金融量化交易：P50：策略初始化与数据读取 📊

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_0.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_2.png)

在本节课中，我们将学习如何为一个量化交易策略进行初始化，并读取所需的股票基本面数据。这是构建一个基于因子打分选股策略的第一步。

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_4.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_6.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_8.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_10.png)

## 策略初始化

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_12.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_14.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_16.png)

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何为一个具体的选股策略搭建框架。首先，我们需要创建一个新的策略文件，并开始编写代码。

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_18.png)

### 定义策略池与变量

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_20.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_22.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_24.png)

在策略的 `context` 对象中，我们需要指定股票池和用于存储排名的变量。

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_26.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_28.png)

*   **指定股票池**：为了获得更好的演示效果，我们选择在沪深300指数的成分股中进行选股。这通常能带来更稳定的收益表现。
    ```python
    # 在context中定义股票池
    context.hs300 = '沪深300'
    ```
*   **定义排名变量**：我们需要一个变量来存储根据因子打分得到的股票排名，以便后续调仓时使用。
    ```python
    # 在context中定义用于存储排名的变量
    context.rank = None
    ```

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_30.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_32.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_34.png)

### 设置定时调仓

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_36.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_38.png)

接下来，我们需要设置一个定时器，来定期执行我们的选股和调仓逻辑。这里我们选择按月调仓。

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_40.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_42.png)

以下是设置定时器的步骤：
1.  使用 `run_monthly` 函数设置按月调仓。
2.  指定调仓时执行的函数，这里我们命名为 `rebalance`。
3.  参数 `1` 表示在每月第一个交易日执行。
    ```python
    # 设置按月调仓的定时器
    run_monthly(rebalance, 1)
    ```

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_44.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_46.png)

## 数据读取与处理

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_48.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_50.png)

上一节我们完成了策略的框架搭建，本节中我们来看看核心部分：如何读取并准备用于打分的因子数据。这将在 `rebalance` 函数中调用一个自定义函数来完成。

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_52.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_54.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_56.png)

### 构建数据查询

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_58.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_60.png)

我们首先定义一个名为 `get_stocks` 的函数，其核心任务是查询股票的基本面数据。我们将使用六个常见的财务指标作为选股因子。

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_62.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_64.png)

以下是需要查询的六个指标，分为两类：
*   **越高越好型因子**：
    1.  **每股收益 (EPS)**：`fundamentals.eod_derivative_indicator.eps`
    2.  **净资产收益率 (ROE)**：`fundamentals.financial_indicator.return_on_equity`
    3.  **投入资本回报率 (ROIC)**：`fundamentals.financial_indicator.return_on_invested_capital`
*   **越低越好型因子**：
    4.  **资产负债率**：`fundamentals.financial_indicator.debt_to_asset`
    5.  **市净率 (PB)**：`fundamentals.eod_derivative_indicator.pb_ratio`
    6.  **总市值**：`fundamentals.eod_derivative_indicator.market_cap`

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_66.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_68.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_70.png)

在查询时，我们需要使用 `filter` 条件将股票范围限定在之前定义的沪深300池子中。

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_72.png)

### 执行查询并转换数据

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_74.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_76.png)

构建好查询语句后，我们使用 `get_fundamentals` 函数来获取数据。获取到的数据需要经过转置处理，以便每一行代表一只股票，每一列代表一个因子，方便后续计算。

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_78.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_80.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_82.png)

以下是数据查询与处理的核心代码逻辑：
```python
def get_stocks(context):
    # 1. 构建查询语句（Query）
    # 查询越高越好的因子
    q_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益
        fundamentals.financial_indicator.return_on_equity, # 净资产收益率
        fundamentals.financial_indicator.return_on_invested_capital # 投入资本回报率
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 过滤条件：股票在沪深300中
    )
    # 查询越低越好的因子
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率
        fundamentals.eod_derivative_indicator.market_cap # 总市值
    ).filter(
        fundamentals.stockcode.in_(context.hs300)
    )

    # 2. 执行查询并获取DataFrame
    df_up = get_fundamentals(q_up) # 获取越高越好因子的数据
    df_down = get_fundamentals(q_down) # 获取越低越好因子的数据

    # 3. 转置DataFrame，使股票为行，因子为列
    df_up = df_up.T
    df_down = df_down.T

    # 此时，df_up 和 df_down 是形状为 (300, 3) 的DataFrame
    # 分别包含了三组因子数据
    return df_up, df_down
```

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_84.png)

## 总结

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_86.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_88.png)

本节课中我们一起学习了量化交易策略初始化的关键步骤。
1.  我们首先在 `context` 中定义了**股票池（沪深300）**和存储排名的**变量**。
2.  接着，我们使用 `run_monthly` 函数设置了**定时调仓机制**。
3.  最后，我们深入讲解了在 `get_stocks` 函数中如何**构建查询**、**获取基本面数据**并进行**数据转置**，为下一步的因子打分做好了数据准备。

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_90.png)

![](img/bcbf1052a50ccaa6ae9c3ceaa73b2fb1_92.png)

至此，策略的“骨架”和“数据源”已经搭建完毕。在接下来的课程中，我们将基于这些数据，实现具体的因子打分与选股逻辑。