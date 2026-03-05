# Python金融分析与量化交易实战教程：P45：3-策略初始化与数据读取

![](img/a608f210eb1ad859f34861668a9921e9_1.png)

## 概述
在本节课中，我们将学习如何初始化一个量化交易策略并读取所需数据。我们将从指定股票池开始，设置调仓定时器，并编写核心函数来获取和准备用于选股的财务指标数据。

![](img/a608f210eb1ad859f34861668a9921e9_3.png)

## 策略初始化

![](img/a608f210eb1ad859f34861668a9921e9_5.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何具体初始化策略参数。

![](img/a608f210eb1ad859f34861668a9921e9_7.png)

![](img/a608f210eb1ad859f34861668a9921e9_9.png)

首先，我们需要在策略的 `initialize` 方法中指定选股范围。为了获得更直观的收益表现，我们选择在沪深300指数的成分股中进行选股。

![](img/a608f210eb1ad859f34861668a9921e9_11.png)

```python
def initialize(context):
    # 指定选股池为沪深300指数成分股
    g.pool = '000300.XSHG'  # 沪深300指数代码
```

接下来，我们需要定义一个变量来存储股票的排名结果，以便在调仓时使用。

```python
    # 初始化一个变量，用于存储股票排名
    g.rank = None
```

![](img/a608f210eb1ad859f34861668a9921e9_13.png)

![](img/a608f210eb1ad859f34861668a9921e9_15.png)

## 设置调仓定时器

![](img/a608f210eb1ad859f34861668a9921e9_17.png)

![](img/a608f210eb1ad859f34861668a9921e9_19.png)

设置好股票池后，我们需要确定调仓的频率。这里我们选择按月进行调仓。

![](img/a608f210eb1ad859f34861668a9921e9_21.png)

以下是设置定时调仓的方法：

![](img/a608f210eb1ad859f34861668a9921e9_23.png)

![](img/a608f210eb1ad859f34861668a9921e9_25.png)

```python
    # 设置按月调仓，每月第一个交易日执行 rebalance 函数
    run_monthly(rebalance, 1)
```

![](img/a608f210eb1ad859f34861668a9921e9_27.png)

## 数据读取与处理

![](img/a608f210eb1ad859f34861668a9921e9_29.png)

定义了调仓函数后，我们需要在调仓时获取股票数据并进行评分。本节我们将重点编写数据获取函数 `get_stocks`。

![](img/a608f210eb1ad859f34861668a9921e9_31.png)

![](img/a608f210eb1ad859f34861668a9921e9_33.png)

在 `get_stocks` 函数中，我们的核心任务是查询所需的财务指标数据。我们将使用六个关键指标，并将其分为“越高越好”和“越低越好”两类。

![](img/a608f210eb1ad859f34861668a9921e9_35.png)

![](img/a608f210eb1ad859f34861668a9921e9_37.png)

### 构建查询语句

首先，我们构建查询语句来获取“越高越好”的指标，包括每股收益、净资产收益率和投入资本回报率。

![](img/a608f210eb1ad859f34861668a9921e9_39.png)

```python
def get_stocks(context):
    # 构建查询语句：获取“越高越好”的指标
    query_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)，越高越好
        fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)，越高越好
        fundamentals.financial_indicator.roic       # 投入资本回报率 (ROIC)，越高越好
    )
```

![](img/a608f210eb1ad859f34861668a9921e9_41.png)

![](img/a608f210eb1ad859f34861668a9921e9_43.png)

接着，我们构建另一个查询语句来获取“越低越好”的指标，包括资产负债率、市净率（PB）和总市值。

![](img/a608f210eb1ad859f34861668a9921e9_45.png)

![](img/a608f210eb1ad859f34861668a9921e9_47.png)

```python
    # 构建查询语句：获取“越低越好”的指标
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset, # 资产负债率，越低越好
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率 (PB)，越低越好
        fundamentals.eod_derivative_indicator.market_cap # 总市值，越低越好
    )
```

### 应用股票池过滤

![](img/a608f210eb1ad859f34861668a9921e9_49.png)

两个查询语句都需要限定在我们之前指定的股票池（沪深300）中。

![](img/a608f210eb1ad859f34861668a9921e9_51.png)

![](img/a608f210eb1ad859f34861668a9921e9_53.png)

```python
    # 为两个查询都添加过滤器，限定在沪深300股票池中
    query_up = query_up.filter(fundamentals.stockcode.in_(g.pool))
    query_down = query_down.filter(fundamentals.stockcode.in_(g.pool))
```

![](img/a608f210eb1ad859f34861668a9921e9_55.png)

![](img/a608f210eb1ad859f34861668a9921e9_57.png)

### 执行查询与数据转换

最后，我们执行查询，并将返回的数据进行转置，使股票代码成为行索引，指标成为列，便于后续处理。

![](img/a608f210eb1ad859f34861668a9921e9_59.png)

```python
    # 执行查询，获取DataFrame，并进行转置
    df_up = get_fundamentals(query_up).T
    df_down = get_fundamentals(query_down).T

    # 打印数据形状以供检查（实际运行时可以注释掉）
    # print(f"‘越高越好’指标数据形状：{df_up.shape}")
    # print(f"‘越低越好’指标数据形状：{df_down.shape}")

    return df_up, df_down
```

![](img/a608f210eb1ad859f34861668a9921e9_61.png)

执行以上步骤后，我们将得到两个 `DataFrame`：`df_up` 包含三个“越高越好”的指标，`df_down` 包含三个“越低越好”的指标。每个 `DataFrame` 的形状都是 `(300, 3)`，代表300支股票和3个指标。

![](img/a608f210eb1ad859f34861668a9921e9_63.png)

![](img/a608f210eb1ad859f34861668a9921e9_65.png)

## 总结
本节课中我们一起学习了量化交易策略的初始化和数据读取步骤。我们首先指定了沪深300作为选股池，并设置了按月调仓的定时器。然后，我们详细编写了 `get_stocks` 函数，通过构建查询语句获取了六类关键的财务指标数据，并将其分为“越高越好”和“越低越好”两组，为后续的因子打分和选股做好了数据准备。