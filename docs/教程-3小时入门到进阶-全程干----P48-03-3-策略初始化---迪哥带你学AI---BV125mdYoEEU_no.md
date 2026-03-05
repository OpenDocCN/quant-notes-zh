# Python金融分析与量化交易实战教程：P48：03-3-策略初始化与数据读取 📊

在本节课中，我们将学习如何初始化一个量化交易策略，并读取用于因子打分选股所需的财务数据。我们将从指定股票池开始，逐步完成策略的框架搭建和数据获取。

![](img/1d0ebeef50c375da3a5fd925de87542b_1.png)

## 策略初始化

首先，我们需要创建一个新的策略文件。在策略的 `initialize` 函数中，我们将进行一些基础设置。

![](img/1d0ebeef50c375da3a5fd925de87542b_3.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何具体配置。

![](img/1d0ebeef50c375da3a5fd925de87542b_5.png)

### 指定股票池

![](img/1d0ebeef50c375da3a5fd925de87542b_7.png)

在 `initialize` 函数中，我们需要指定选股的范围，即“股票池”。为了获得更好的演示效果，我们将选择沪深300指数成分股作为我们的股票池。

![](img/1d0ebeef50c375da3a5fd925de87542b_9.png)

以下是具体的代码实现：

![](img/1d0ebeef50c375da3a5fd925de87542b_11.png)

```python
def initialize(context):
    # 指定选股范围为沪深300指数成分股
    g.pool = ‘沪深300’
```

这样，我们就完成了股票池的指定。

### 定义调仓变量

接下来，我们需要定义一个变量来存储股票的排名结果，以便在调仓时使用。

以下是具体的代码实现：

![](img/1d0ebeef50c375da3a5fd925de87542b_13.png)

```python
def initialize(context):
    g.pool = ‘沪深300’
    # 定义一个变量，用于存储股票的排名情况
    g.rank_stocks = None
```

![](img/1d0ebeef50c375da3a5fd925de87542b_15.png)

![](img/1d0ebeef50c375da3a5fd925de87542b_17.png)

### 设置定时调仓

![](img/1d0ebeef50c375da3a5fd925de87542b_19.png)

量化策略通常需要定期（例如每月）调整持仓。我们将设置一个每月运行的定时器，并指定调仓时执行的函数。

以下是具体的代码实现：

![](img/1d0ebeef50c375da3a5fd925de87542b_21.png)

```python
def initialize(context):
    g.pool = ‘沪深300’
    g.rank_stocks = None
    # 设置按月调仓，每月运行一次 rebalance 函数
    run_monthly(rebalance, 1)
```

![](img/1d0ebeef50c375da3a5fd925de87542b_23.png)

## 数据读取与因子定义

![](img/1d0ebeef50c375da3a5fd925de87542b_25.png)

设置好策略框架后，下一步是获取用于评价股票的财务数据，即“因子”。

![](img/1d0ebeef50c375da3a5fd925de87542b_27.png)

上一节我们完成了策略的初始化，本节中我们来看看如何获取和准备因子数据。

![](img/1d0ebeef50c375da3a5fd925de87542b_29.png)

### 定义调仓函数

我们首先定义调仓函数 `rebalance` 的框架。其核心任务是获取经过评分排序后的股票列表。

![](img/1d0ebeef50c375da3a5fd925de87542b_31.png)

以下是具体的代码实现：

![](img/1d0ebeef50c375da3a5fd925de87542b_33.png)

```python
def rebalance(context):
    # 调用函数获取评分后的股票排名
    g.rank_stocks = get_stocks()
```

![](img/1d0ebeef50c375da3a5fd925de87542b_35.png)

### 构建数据查询函数

我们将创建一个名为 `get_stocks` 的函数，专门负责查询财务数据并对股票进行评分。

![](img/1d0ebeef50c375da3a5fd925de87542b_37.png)

以下是该函数的初始框架：

```python
def get_stocks():
    # 此函数将查询数据并计算股票评分
    pass
```

![](img/1d0ebeef50c375da3a5fd925de87542b_39.png)

### 查询财务指标

![](img/1d0ebeef50c375da3a5fd925de87542b_41.png)

在 `get_stocks` 函数中，第一步是查询我们选定的财务指标。我们将使用六个指标作为例子，它们分为两类：“越高越好”型和“越低越好”型。

![](img/1d0ebeef50c375da3a5fd925de87542b_43.png)

以下是查询“越高越好”型指标的代码实现。我们查询每股收益（EPS）、净资产收益率（ROE）和投入资本回报率（ROIC）。

![](img/1d0ebeef50c375da3a5fd925de87542b_45.png)

```python
def get_stocks():
    # 构建查询语句，获取“越高越好”的指标
    q = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益，越高越好
        fundamentals.financial_indicator.roe, # 净资产收益率，越高越好
        fundamentals.financial_indicator.roic # 投入资本回报率，越高越好
    )
```

以下是查询“越低越好”型指标的代码实现。我们查询资产负债率、市净率（PB）和总市值。

![](img/1d0ebeef50c375da3a5fd925de87542b_47.png)

```python
    # 继续构建查询，获取“越低越好”的指标
    q = q.query(
        fundamentals.financial_indicator.debt_to_asset, # 资产负债率，越低越好
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率，越低越好
        fundamentals.eod_derivative_indicator.market_cap # 总市值，越低越好
    )
```

### 筛选股票池

![](img/1d0ebeef50c375da3a5fd925de87542b_49.png)

![](img/1d0ebeef50c375da3a5fd925de87542b_51.png)

查询时，我们需要将股票范围限定在之前设定的沪深300股票池内。

![](img/1d0ebeef50c375da3a5fd925de87542b_53.png)

以下是具体的代码实现：

![](img/1d0ebeef50c375da3a5fd925de87542b_55.png)

```python
    # 添加过滤器，只选择在沪深300股票池中的股票
    q = q.filter(fundamentals.stockcode.in_(g.pool))
```

### 执行查询并处理数据

最后，我们执行查询，并将返回的数据进行转置，以便后续处理（使股票代码成为行索引）。

以下是具体的代码实现：

![](img/1d0ebeef50c375da3a5fd925de87542b_57.png)

```python
    # 执行查询，获取财务数据
    fund_data = get_fundamentals(q)
    # 转置DataFrame，使股票代码成为行索引
    fund_data = fund_data.T
    return fund_data
```

![](img/1d0ebeef50c375da3a5fd925de87542b_59.png)

**注意**：`get_fundamentals(q)` 返回一个 `DataFrame`，其中原始的行是股票代码，列是指标。通过 `.T` 操作进行转置后，行变成了指标，列变成了股票代码。这通常是为了方便后续按股票进行数据分析和评分计算。最终返回的 `fund_data` 是一个形状为 `(指标数量, 股票数量)` 的 `DataFrame`。

![](img/1d0ebeef50c375da3a5fd925de87542b_61.png)

本节课中我们一起学习了量化策略的初始化步骤，包括设置股票池、定义调仓周期，以及如何查询和获取用于多因子选股的财务数据。我们构建了策略的基本骨架，并准备好了原始数据，为下一节课的因子打分和股票排序奠定了基础。