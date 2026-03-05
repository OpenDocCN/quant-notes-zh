# 量化交易实战课程：P45：策略初始化与数据读取

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_0.png)

## 概述
在本节课中，我们将学习如何为一个量化选股策略进行初始化，并读取所需的财务数据。我们将构建一个基于因子打分的选股策略框架，从指定股票池开始，到获取并处理关键的财务指标数据。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_2.png)

## 策略初始化

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_4.png)

上一节我们介绍了量化策略的基本概念，本节中我们来看看如何为一个具体的选股策略进行初始化设置。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_6.png)

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_8.png)

首先，我们需要在策略的 `context` 对象中指定选股的范围。为了获得更好的回测效果，我们选择在沪深300指数的成分股中进行选股。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_10.png)

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_12.png)

```python
# 在context中指定选股池为沪深300指数成分股
context.hs300 = ‘沪深300’
```

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_14.png)

接下来，我们需要定义一个变量来存储股票的排名结果，这个结果将在后续的调仓函数中使用。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_16.png)

```python
# 定义一个变量来存储股票排名
context.rank = None
```

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_18.png)

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_20.png)

## 设置定时调仓

初始化选股池后，我们需要设置一个定时器来执行周期性的调仓操作。这里我们选择按月进行调仓。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_22.png)

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_24.png)

以下是设置定时调仓的步骤：

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_26.png)

1.  使用 `run_monthly` 函数设置按月调仓。
2.  指定调仓时执行的函数名，这里我们命名为 `rebalance`。
3.  设置调仓日期为每月第一个交易日。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_28.png)

```python
# 设置按月调仓，在每月第一个交易日执行rebalance函数
run_monthly(rebalance, 1)
```

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_30.png)

## 定义调仓函数

设置好定时器后，我们需要定义具体的调仓函数 `rebalance`。这个函数的核心任务是获取股票评分并据此调整持仓。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_32.png)

```python
def rebalance(context):
    # 调仓函数的主体逻辑
    # 核心是调用get_stocks函数获取评分后的股票列表
    context.rank = get_stocks()
```

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_34.png)

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_36.png)

## 构建数据获取函数

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_38.png)

调仓函数依赖于一个名为 `get_stocks` 的函数来获取并评分股票。现在，我们来构建这个函数。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_40.png)

在 `get_stocks` 函数中，第一步是查询所需的财务指标数据。我们将使用 `get_fundamentals` 函数进行查询。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_42.png)

以下是构建查询语句的步骤：

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_44.png)

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_46.png)

1.  在 `query` 中指定需要查询的财务指标。
2.  我们选取六个关键指标作为例子，分为“越高越好”和“越低越好”两类。
3.  使用 `filter` 条件将查询范围限定在之前指定的沪深300股票池内。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_48.png)

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_50.png)

首先，查询“越高越好”的指标，包括每股收益（EPS）、净资产收益率（ROE）和投入资本回报率（ROIC）。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_52.png)

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_54.png)

```python
# 构建查询“越高越好”指标的query
query_up = query(
    fundamentals.eod_derivative_indicator.eps, # 每股收益，越高越好
    fundamentals.financial_indicator.roe, # 净资产收益率，越高越好
    fundamentals.financial_indicator.roic # 投入资本回报率，越高越好
).filter(
    fundamentals.stockcode.in_(context.hs300) # 过滤条件：股票在沪深300池中
)
```

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_56.png)

接着，查询“越低越好”的指标，包括资产负债率、市净率（PB）和总市值。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_58.png)

```python
# 构建查询“越低越好”指标的query
query_down = query(
    fundamentals.financial_indicator.debt_to_asset, # 资产负债率，越低越好
    fundamentals.eod_derivative_indicator.pb_ratio, # 市净率，越低越好
    fundamentals.eod_derivative_indicator.market_cap # 总市值，越低越好
).filter(
    fundamentals.stockcode.in_(context.hs300) # 过滤条件：股票在沪深300池中
)
```

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_60.png)

## 执行查询与数据处理

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_62.png)

构建好查询语句后，我们执行查询并获取数据。获取到的 `DataFrame` 需要进行转置操作，以便股票代码成为索引，方便后续处理。

```python
# 执行查询，获取“越高越好”指标的数据，并进行转置
df_up = get_fundamentals(query_up).T

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_64.png)

# 执行查询，获取“越低越好”指标的数据，并进行转置
df_down = get_fundamentals(query_down).T
```

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_66.png)

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_68.png)

执行完上述代码后，我们将得到两个 `DataFrame`：`df_up` 和 `df_down`。它们的形状都是（股票数量， 指标数量），例如对于沪深300，形状约为（300， 3）。这两个对象包含了我们进行因子打分所需的所有原始数据。

![](img/1c6ec1b7b51d9112a6e07538a4bb1687_70.png)

## 总结
本节课中我们一起学习了量化选股策略的初始化与数据读取流程。我们首先在 `context` 中设定了沪深300作为选股池，并设置了按月调仓的定时任务。然后，我们详细定义了调仓函数 `rebalance` 和数据获取函数 `get_stocks` 的框架。最后，我们使用 `get_fundamentals` 函数成功查询到了“越高越好”和“越低越好”两类共六个关键财务指标的数据，并为后续的因子评分做好了数据准备。