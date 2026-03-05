# Python金融分析与量化交易实战课程：P50：策略初始化与数据读取 📊

![](img/9dd217e2ed634f51a4a9c0cbe094a442_1.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_3.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_5.png)

在本节课中，我们将学习如何为一个量化交易策略进行初始化，并读取所需的股票基本面数据。我们将从定义选股池和调仓周期开始，逐步构建一个用于因子打分的数据获取函数。

![](img/9dd217e2ed634f51a4a9c0cbe094a442_7.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_9.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_11.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_13.png)

## 策略初始化

![](img/9dd217e2ed634f51a4a9c0cbe094a442_15.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_17.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何为一个具体的选股策略进行初始化设置。

![](img/9dd217e2ed634f51a4a9c0cbe094a442_19.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_21.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_23.png)

首先，我们需要在策略的 `context` 对象中定义一些全局变量。这些变量将在整个策略运行过程中被使用。

![](img/9dd217e2ed634f51a4a9c0cbe094a442_25.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_27.png)

以下是初始化步骤：

![](img/9dd217e2ed634f51a4a9c0cbe094a442_29.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_31.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_33.png)

1.  **定义选股池**：为了获得更好的回测效果，我们选择在沪深300指数的成分股中进行选股。这指定了策略筛选股票的范围。
    ```python
    # 在context中定义选股池为沪深300成分股
    context.hs300 = ‘沪深300’
    ```

![](img/9dd217e2ed634f51a4a9c0cbe094a442_35.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_37.png)

2.  **预留排名存储变量**：我们需要一个变量来存储根据因子打分得到的股票排名结果，以便在调仓时使用。
    ```python
    # 定义一个变量，用于后续存储股票的排名情况
    context.rank = None
    ```

![](img/9dd217e2ed634f51a4a9c0cbe094a442_39.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_41.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_43.png)

3.  **设置调仓定时器**：策略需要定期执行调仓操作。我们设置一个按月调仓的定时器。
    ```python
    # 设置按月调仓，每月第一个交易日执行rebalance函数
    scheduler.run_monthly(rebalance, 1)
    ```

![](img/9dd217e2ed634f51a4a9c0cbe094a442_45.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_47.png)

## 构建数据读取函数

![](img/9dd217e2ed634f51a4a9c0cbe094a442_49.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_51.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_53.png)

初始化完成后，我们需要编写核心的调仓函数 `rebalance`。这个函数的核心任务是根据因子对股票进行评分和排序。为此，我们首先需要获取股票的相关数据。

![](img/9dd217e2ed634f51a4a9c0cbe094a442_55.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_57.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_59.png)

我们创建一个名为 `get_stocks` 的函数来专门负责数据获取工作。

![](img/9dd217e2ed634f51a4a9c0cbe094a442_61.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_63.png)

以下是 `get_stocks` 函数的数据查询步骤：

![](img/9dd217e2ed634f51a4a9c0cbe094a442_65.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_67.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_69.png)

1.  **构建查询语句 (Query)**：我们需要查询多只股票的多个基本面指标。这里以六个常用因子为例，分为两类：“越高越好”的因子和“越低越好”的因子。
    ```python
    # 构建查询语句，获取“越高越好”的因子数据
    query_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)，越高越好
        fundamentals.financial_indicator.return_on_equity, # 净资产收益率 (ROE)，越高越好
        fundamentals.financial_indicator.return_on_invested_capital # 投入资本回报率 (ROIC)，越高越好
    )
    ```

![](img/9dd217e2ed634f51a4a9c0cbe094a442_71.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_73.png)

2.  **应用股票池过滤**：确保我们只查询在沪深300池子内的股票，以缩小数据范围。
    ```python
    # 添加过滤器，只查询属于context.hs300池子的股票
    query_up = query_up.filter(fundamentals.stockcode.in_(context.hs300))
    ```

![](img/9dd217e2ed634f51a4a9c0cbe094a442_75.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_77.png)

3.  **执行查询并转换数据格式**：使用 `get_fundamentals` 执行查询。返回的数据通常需要转置（`.T`），使股票代码成为行索引，指标成为列，便于后续处理。
    ```python
    # 执行查询，并对结果进行转置，方便按股票进行处理
    df_up = get_fundamentals(query_up).T
    ```

![](img/9dd217e2ed634f51a4a9c0cbe094a442_79.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_81.png)

4.  **获取“越低越好”的因子数据**：用同样的方法获取另一类因子数据，例如资产负债率、市净率（PB）和市值。
    ```python
    # 构建查询语句，获取“越低越好”的因子数据
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率，越低越好
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率 (PB)，越低越好
        fundamentals.eod_derivative_indicator.market_cap # 市值，越低越好
    ).filter(fundamentals.stockcode.in_(context.hs300))
    
    df_down = get_fundamentals(query_down).T
    ```

![](img/9dd217e2ed634f51a4a9c0cbe094a442_83.png)

完成以上步骤后，`df_up` 和 `df_down` 这两个 `DataFrame` 就包含了我们所需的所有因子数据。它们的形状预计是 `(300, 3)`，表示300只股票和3个因子。

![](img/9dd217e2ed634f51a4a9c0cbe094a442_85.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_87.png)

![](img/9dd217e2ed634f51a4a9c0cbe094a442_89.png)

本节课中我们一起学习了量化策略的初始化流程，包括设置选股池、调仓周期，并重点构建了用于获取股票多维度基本面数据的函数。我们成功将“越高越好”和“越低越好”两类因子数据分别查询并整理成结构化的表格，为下一步的因子综合打分做好了数据准备。