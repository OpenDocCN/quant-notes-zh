# Python金融分析与量化交易实战课程：P45：3-策略初始化与数据读取 📊

在本节课中，我们将学习如何初始化一个量化交易策略，并读取用于因子打分选股所需的财务数据。我们将从指定股票池开始，逐步构建数据查询和预处理流程。

![](img/896c6c25566892eb3f5b0ed9c707b397_1.png)

## 策略初始化

首先，我们需要创建一个新的策略文件。在策略的 `initialize` 函数中，我们将进行一些基础设置。

![](img/896c6c25566892eb3f5b0ed9c707b397_3.png)

上一节我们介绍了策略文件的基本结构，本节中我们来看看如何具体配置策略的初始环境。

![](img/896c6c25566892eb3f5b0ed9c707b397_5.png)

### 指定股票池

![](img/896c6c25566892eb3f5b0ed9c707b397_7.png)

在 `initialize` 函数中，我们需要指定选股的范围，即“股票池”。为了获得更好的回测效果，我们选择在沪深300指数的成分股中进行选股。

![](img/896c6c25566892eb3f5b0ed9c707b397_9.png)

以下是具体的代码实现：

![](img/896c6c25566892eb3f5b0ed9c707b397_11.png)

```python
def initialize(context):
    # 指定选股范围为沪深300指数成分股
    g.pool = ‘沪深300’
```

这段代码将选股池设置为 `沪深300`，后续所有选股操作都将在这个范围内进行。

### 定义上下文变量

接下来，我们需要在上下文（`context` 或使用全局变量 `g`）中定义一些变量，用于存储后续计算得到的股票排名结果，以便在调仓时使用。

![](img/896c6c25566892eb3f5b0ed9c707b397_13.png)

```python
def initialize(context):
    g.pool = ‘沪深300’
    # 初始化一个变量，用于存储股票排名
    g.rank_list = None
```

![](img/896c6c25566892eb3f5b0ed9c707b397_15.png)

变量 `g.rank_list` 将在后续的评分函数中被赋值，用于记录股票的排序情况。

![](img/896c6c25566892eb3f5b0ed9c707b397_17.png)

![](img/896c6c25566892eb3f5b0ed9c707b397_19.png)

### 设置定时调仓

量化策略通常需要定期（如每月、每周）执行选股和调仓操作。我们将设置一个按月运行的定时器。

以下是设置定时调仓的代码：

![](img/896c6c25566892eb3f5b0ed9c707b397_21.png)

```python
def initialize(context):
    g.pool = ‘沪深300’
    g.rank_list = None

    # 设置按月调仓，每月第一个交易日运行 rebalance 函数
    run_monthly(rebalance, 1)
```

![](img/896c6c25566892eb3f5b0ed9c707b397_23.png)

![](img/896c6c25566892eb3f5b0ed9c707b397_25.png)

这里，`run_monthly(rebalance, 1)` 表示在每个月第一个交易日，系统将自动调用 `rebalance` 函数来执行调仓逻辑。

## 数据读取与因子定义

![](img/896c6c25566892eb3f5b0ed9c707b397_27.png)

在定义了策略框架后，核心任务是实现 `rebalance` 函数。该函数的第一步是获取股票数据并进行评分。为此，我们需要先编写一个数据获取函数 `get_stocks`。

![](img/896c6c25566892eb3f5b0ed9c707b397_29.png)

上一节我们设置了策略的调仓周期，本节中我们来看看如何获取并准备用于打分的因子数据。

![](img/896c6c25566892eb3f5b0ed9c707b397_31.png)

### 创建数据获取函数

![](img/896c6c25566892eb3f5b0ed9c707b397_33.png)

我们将创建一个名为 `get_stocks` 的函数，其核心任务是查询所需的财务指标数据。

以下是该函数的基本结构：

![](img/896c6c25566892eb3f5b0ed9c707b397_35.png)

```python
def get_stocks(context):
    # 此函数用于查询并返回股票因子数据
    pass
```

![](img/896c6c25566892eb3f5b0ed9c707b397_37.png)

### 构建查询语句

在 `get_stocks` 函数内部，我们需要使用 `query` 语句来获取具体的财务指标。本教程以六个常见指标为例，分为两类：“越高越好”型和“越低越好”型。

以下是构建查询语句的步骤：

![](img/896c6c25566892eb3f5b0ed9c707b397_39.png)

首先，我们查询“越高越好”的指标，包括每股收益（EPS）、净资产收益率（ROE）和投入资本回报率（ROIC）。

![](img/896c6c25566892eb3f5b0ed9c707b397_41.png)

![](img/896c6c25566892eb3f5b0ed9c707b397_43.png)

```python
# 构建查询语句，获取“越高越好”的因子
query_up = query(
    fundamentals.eod_derivative_indicator.eps, # 每股收益，越高越好
    fundamentals.financial_indicator.roe, # 净资产收益率，越高越好
    fundamentals.financial_indicator.roic # 投入资本回报率，越高越好
)
```

![](img/896c6c25566892eb3f5b0ed9c707b397_45.png)

接下来，我们查询“越低越好”的指标，包括资产负债率、市净率（PB）和市值。

```python
# 构建查询语句，获取“越低越好”的因子
query_down = query(
    fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率，越低越好
    fundamentals.eod_derivative_indicator.pb_ratio, # 市净率，越低越好
    fundamentals.eod_derivative_indicator.market_cap # 总市值，越低越好
)
```

![](img/896c6c25566892eb3f5b0ed9c707b397_47.png)

### 应用股票池过滤

查询时，我们需要确保只获取属于沪深300股票池的股票数据。这可以通过在查询中添加 `filter` 条件来实现。

![](img/896c6c25566892eb3f5b0ed9c707b397_49.png)

![](img/896c6c25566892eb3f5b0ed9c707b397_51.png)

```python
# 为两个查询都添加过滤条件，限定在沪深300股票池中
query_up = query_up.filter(fundamentals.stockcode.in_(g.pool))
query_down = query_down.filter(fundamentals.stockcode.in_(g.pool))
```

![](img/896c6c25566892eb3f5b0ed9c707b397_53.png)

### 执行查询并转换数据

![](img/896c6c25566892eb3f5b0ed9c707b397_55.png)

使用 `get_fundamentals` 函数执行查询，获取数据。需要注意的是，返回的 `DataFrame` 格式需要转置（`.T`），以便股票代码成为行索引，方便后续处理。

以下是获取并处理两类因子数据的完整代码：

```python
def get_stocks(context):
    # 1. 定义查询语句
    query_up = query(
        fundamentals.eod_derivative_indicator.eps,
        fundamentals.financial_indicator.roe,
        fundamentals.financial_indicator.roic
    ).filter(fundamentals.stockcode.in_(g.pool))

    query_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio,
        fundamentals.eod_derivative_indicator.pb_ratio,
        fundamentals.eod_derivative_indicator.market_cap
    ).filter(fundamentals.stockcode.in_(g.pool))

    # 2. 执行查询并转置数据
    df_up = get_fundamentals(query_up).T # “越高越好”因子数据
    df_down = get_fundamentals(query_down).T # “越低越好”因子数据

    # 3. 返回数据
    return df_up, df_down
```

执行后，`df_up` 和 `df_down` 将是形状约为 `(300, 3)` 的 `DataFrame`，分别包含300支股票的三项“高优”因子和三项目“低优”因子数据。

![](img/896c6c25566892eb3f5b0ed9c707b397_57.png)

## 总结

![](img/896c6c25566892eb3f5b0ed9c707b397_59.png)

本节课中我们一起学习了量化策略初始化的关键步骤：
1.  **策略初始化**：我们设置了股票池（沪深300），定义了存储排名的上下文变量，并建立了按月调仓的定时任务。
2.  **数据读取**：我们创建了 `get_stocks` 函数，通过构建 `query` 语句，分别获取了“越高越好”和“越低越好”两类共六个财务指标，并确保数据仅限于指定股票池，最后对数据格式进行了转置以便后续分析。

![](img/896c6c25566892eb3f5b0ed9c707b397_61.png)

至此，我们已经完成了策略的基础框架搭建和原始数据获取工作，为下一节课的因子打分与股票排名计算做好了准备。