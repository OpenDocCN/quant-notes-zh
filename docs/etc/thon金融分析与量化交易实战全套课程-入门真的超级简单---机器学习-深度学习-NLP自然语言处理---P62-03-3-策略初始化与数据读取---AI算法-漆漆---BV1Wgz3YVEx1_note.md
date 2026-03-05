# 人工智能金融量化实战：P62：03-3-策略初始化与数据读取 📊

![](img/bcbc44442f768f53ff66e2ca00a79949_1.png)

在本节课中，我们将学习如何为一个因子打分选股策略进行初始化，并读取所需的财务数据。我们将从定义股票池开始，逐步完成策略框架的搭建和数据获取。

![](img/bcbc44442f768f53ff66e2ca00a79949_3.png)

## 策略初始化

![](img/bcbc44442f768f53ff66e2ca00a79949_5.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_7.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_9.png)

上一节我们介绍了策略的基本概念，本节中我们来看看如何为策略设置初始参数。

![](img/bcbc44442f768f53ff66e2ca00a79949_11.png)

首先，我们需要在策略的 `initialize` 方法中指定选股的范围，即“股票池”。为了使策略表现更直观，我们选择在沪深300指数的成分股中进行选股。

```python
def initialize(context):
    # 指定选股池为沪深300指数成分股
    context.hs300 = index_components('000300.XSHG')
```

接下来，我们需要定义一个变量，用于存储后续计算出的股票排名结果，以便在调仓时使用。

![](img/bcbc44442f768f53ff66e2ca00a79949_13.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_15.png)

```python
def initialize(context):
    context.hs300 = index_components('000300.XSHG')
    # 初始化一个变量，用于存储股票排名
    context.rank_list = None
```

![](img/bcbc44442f768f53ff66e2ca00a79949_17.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_19.png)

然后，我们设置一个定时器，规定策略调仓的频率。这里我们选择按月进行调仓。

![](img/bcbc44442f768f53ff66e2ca00a79949_21.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_23.png)

```python
def initialize(context):
    context.hs300 = index_components('000300.XSHG')
    context.rank_list = None
    # 设置按月调仓，每月第一个交易日运行 rebalance 函数
    run_monthly(rebalance, 1)
```

![](img/bcbc44442f768f53ff66e2ca00a79949_25.png)

既然我们设置了调仓函数 `rebalance`，接下来就需要定义它。这个函数是策略的核心，负责执行选股和调仓逻辑。

![](img/bcbc44442f768f53ff66e2ca00a79949_27.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_29.png)

```python
def rebalance(context):
    # 调仓逻辑将在此实现
    pass
```

![](img/bcbc44442f768f53ff66e2ca00a79949_31.png)

## 数据读取与因子定义

![](img/bcbc44442f768f53ff66e2ca00a79949_33.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_35.png)

在 `rebalance` 函数中，最核心的一步是根据因子对股票进行评分。为此，我们需要先获取股票的相关财务数据。我们将创建一个专门的函数 `get_stocks` 来完成数据获取和初步处理。

![](img/bcbc44442f768f53ff66e2ca00a79949_37.png)

以下是 `get_stocks` 函数中获取数据的具体步骤。

![](img/bcbc44442f768f53ff66e2ca00a79949_39.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_41.png)

第一步，我们需要构建查询语句（query），以获取所需的财务指标。我们以六个常见指标为例，分为两类：“越高越好”的指标和“越低越好”的指标。

![](img/bcbc44442f768f53ff66e2ca00a79949_43.png)

```python
def get_stocks(context):
    # 构建查询语句，获取“越高越好”的因子
    q_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)，越高越好
        fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)，越高越好
        fundamentals.financial_indicator.roa        # 总资产收益率 (ROA)，越高越好
    )
```

![](img/bcbc44442f768f53ff66e2ca00a79949_45.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_47.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_49.png)

同时，我们还需要获取另一类“越低越好”的财务指标。

```python
    # 构建查询语句，获取“越低越好”的因子
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset, # 资产负债率，越低越好
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率 (PB)，越低越好
        fundamentals.eod_derivative_indicator.market_cap # 总市值，越低越好
    )
```

![](img/bcbc44442f768f53ff66e2ca00a79949_51.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_53.png)

查询语句构建好后，我们需要添加一个过滤器（filter），将股票范围限定在我们之前设定的沪深300股票池内。

![](img/bcbc44442f768f53ff66e2ca00a79949_55.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_57.png)

```python
    # 为两个查询添加过滤器，限定股票范围
    q_up = q_up.filter(fundamentals.stockcode.in_(context.hs300))
    q_down = q_down.filter(fundamentals.stockcode.in_(context.hs300))
```

![](img/bcbc44442f768f53ff66e2ca00a79949_59.png)

现在，我们可以执行查询，获取原始的 `DataFrame` 格式数据。获取数据后，为了便于后续按股票代码进行索引和计算，我们需要对数据进行转置操作。

```python
    # 执行查询并获取数据，然后进行转置
    df_up = get_fundamentals(q_up).T
    df_down = get_fundamentals(q_down).T

    # 返回两类因子的数据
    return df_up, df_down
```
至此，`get_stocks` 函数会返回两个 `DataFrame`：`df_up` 包含越高越好的因子数据，`df_down` 包含越低越好的因子数据。每个 `DataFrame` 的形状预期为 `(300, 3)`，即300支股票，每支股票对应3个因子值。

![](img/bcbc44442f768f53ff66e2ca00a79949_61.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_63.png)

## 总结

![](img/bcbc44442f768f53ff66e2ca00a79949_65.png)

![](img/bcbc44442f768f53ff66e2ca00a79949_67.png)

本节课中我们一起学习了量化策略初始化的关键步骤。我们首先定义了沪深300股票池，并设置了按月调仓的定时器。接着，我们重点讲解了如何通过构建查询语句来获取所需的财务因子数据，并将数据按“越高越好”和“越低越好”进行分类处理，为后续的因子打分做好了数据准备。