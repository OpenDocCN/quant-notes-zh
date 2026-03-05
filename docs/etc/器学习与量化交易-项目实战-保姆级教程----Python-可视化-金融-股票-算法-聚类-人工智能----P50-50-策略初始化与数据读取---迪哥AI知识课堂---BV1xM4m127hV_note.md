# 机器学习与量化交易：P50：策略初始化与数据读取 📊

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_0.png)

在本节课中，我们将学习如何为一个量化交易策略进行初始化，并读取所需的股票基本面数据。我们将从定义选股池开始，逐步构建数据获取函数，为后续的因子评分和选股打下基础。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_2.png)

## 策略初始化

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_4.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_6.png)

首先，我们需要在新的策略文件中创建一个触发方法，并在 `context` 对象中定义策略运行所需的核心参数。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_8.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_10.png)

上一节我们介绍了策略文件的创建，本节中我们来看看如何设置初始参数。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_12.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_14.png)

### 定义股票池

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_16.png)

为了获得更好的策略表现，我们将选股范围限定在沪深300指数的成分股中。这有助于我们聚焦于流动性较好、市值较大的股票。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_18.png)

在 `context` 中，我们指定 `hs300` 作为我们的股票池。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_20.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_22.png)

```python
# 在 initialize 函数中定义
def initialize(context):
    # 指定选股池为沪深300成分股
    context.hs300 = index_components('000300.XSHG')
```

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_24.png)

### 定义排名变量

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_26.png)

接下来，我们需要一个变量来存储股票的排名结果，以便在调仓时选择排名靠前的股票。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_28.png)

在 `context` 中，我们定义一个变量 `stock_rank` 来存放排名情况。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_30.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_32.png)

```python
def initialize(context):
    context.hs300 = index_components('000300.XSHG')
    # 初始化一个变量，用于存储股票排名
    context.stock_rank = None
```

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_34.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_36.png)

### 设置定时调仓器

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_38.png)

策略需要定期运行以调整持仓。我们将设置一个按月调仓的定时器。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_40.png)

以下是设置定时器的方法，它将在每月触发 `rebalance` 函数。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_42.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_44.png)

```python
def initialize(context):
    context.hs300 = index_components('000300.XSHG')
    context.stock_rank = None
    # 设置按月调仓，每月第一个交易日运行 rebalance 函数
    run_monthly(rebalance, 1)
```

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_46.png)

## 数据读取与处理

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_48.png)

定义了策略框架后，下一步是获取用于评分的股票基本面数据。这将在 `rebalance` 函数调用的 `get_stocks` 函数中完成。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_50.png)

上一节我们设置了调仓计划，本节中我们来看看如何获取和准备数据。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_52.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_54.png)

### 创建数据获取函数

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_56.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_58.png)

我们创建一个名为 `get_stocks` 的函数，其核心任务是查询并返回用于评分的股票因子数据。

以下是 `get_stocks` 函数的基本结构。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_60.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_62.png)

```python
def get_stocks(context):
    # 此函数将查询并返回处理好的因子数据
    pass
```

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_64.png)

### 构建查询语句

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_66.png)

我们将根据选定的六个基本面指标来构建查询。这些指标分为两类：“越高越好”型和“越低越好”型。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_68.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_70.png)

以下是需要查询的指标列表及其属性。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_72.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_74.png)

1.  **每股收益**：越高越好。
2.  **净资产收益率**：越高越好。
3.  **投入资本回报率**：越高越好。
4.  **资产负债率**：越低越好。
5.  **市净率**：越低越好。
6.  **总市值**：越低越好。

在代码中，我们使用 `query` 对象来封装这些查询条件，并通过 `filter` 将股票限定在沪深300池子中。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_76.png)

```python
def get_stocks(context):
    # 查询“越高越好”的指标
    q_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益
        fundamentals.financial_indicator.roe, # 净资产收益率
        fundamentals.financial_indicator.roic # 投入资本回报率
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 限定在沪深300内
    )
    
    # 查询“越低越好”的指标
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率
        fundamentals.eod_derivative_indicator.market_cap # 总市值
    ).filter(
        fundamentals.stockcode.in_(context.hs300)
    )
```

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_78.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_80.png)

### 执行查询与数据转换

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_82.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_84.png)

使用 `get_fundamentals` 函数执行查询。获取到的数据格式需要进行转置，以便股票代码成为行索引，方便后续处理。

以下是执行查询并进行数据格式转换的代码。

```python
def get_stocks(context):
    q_up = query(...).filter(...)
    q_down = query(...).filter(...)
    
    # 获取数据
    df_up = get_fundamentals(q_up)
    df_down = get_fundamentals(q_down)
    
    # 转换数据格式：将股票代码作为行索引
    df_up = df_up.T
    df_down = df_down.T
    
    # 为数据框命名以便区分
    df_up.name = ‘越高越好因子‘
    df_down.name = ‘越低越好因子‘
    
    return df_up, df_down
```

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_86.png)

执行后，`df_up` 和 `df_down` 将是形状为 `(300, 3)` 的 `DataFrame`，分别包含三类“越高越好”和三类“越低越好”的因子数据。

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_88.png)

## 总结

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_90.png)

![](img/e9dd2d2ff6b1965906e2ff955cfdad20_92.png)

本节课中我们一起学习了量化交易策略初始化的关键步骤。
我们首先在 `context` 中定义了股票池和排名变量，并设置了按月调仓的定时器。
接着，我们构建了 `get_stocks` 函数，详细说明了如何查询每股收益、净资产收益率等六个基本面指标，并将数据转换为便于后续评分处理的格式。
这些工作为下一阶段——基于这些因子对股票进行综合评分和筛选——做好了充分的数据准备。