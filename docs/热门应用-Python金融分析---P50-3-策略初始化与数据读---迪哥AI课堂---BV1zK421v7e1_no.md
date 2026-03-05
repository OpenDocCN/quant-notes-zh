# Python金融分析量化交易：P50：策略初始化与数据读取 📊

![](img/8596d324626ef69f880f3fd43e1ad625_1.png)

在本节课中，我们将学习如何为一个因子打分选股策略进行初始化，并读取所需的基础数据。我们将从定义选股池开始，逐步完成定时调仓函数的框架搭建，并重点讲解如何查询和获取关键的财务指标数据。

![](img/8596d324626ef69f880f3fd43e1ad625_3.png)

## 策略初始化

![](img/8596d324626ef69f880f3fd43e1ad625_5.png)

![](img/8596d324626ef69f880f3fd43e1ad625_7.png)

上一节我们介绍了策略的整体思路，本节中我们来看看如何为策略设置初始环境。

![](img/8596d324626ef69f880f3fd43e1ad625_9.png)

![](img/8596d324626ef69f880f3fd43e1ad625_11.png)

首先，我们需要在策略的 `context` 对象中指定选股范围。为了获得更好的演示效果，我们选择在沪深300指数的成分股中进行筛选。

![](img/8596d324626ef69f880f3fd43e1ad625_13.png)

```python
# 在context中指定选股池为沪深300
context.hs300 = '沪深300'
```

![](img/8596d324626ef69f880f3fd43e1ad625_15.png)

![](img/8596d324626ef69f880f3fd43e1ad625_17.png)

接下来，我们需要定义一个变量来存储股票的排名结果，以便在调仓时使用。

![](img/8596d324626ef69f880f3fd43e1ad625_19.png)

```python
# 定义一个变量来存储股票排名
context.rank = None
```

![](img/8596d324626ef69f880f3fd43e1ad625_21.png)

## 设置定时调仓

![](img/8596d324626ef69f880f3fd43e1ad625_23.png)

![](img/8596d324626ef69f880f3fd43e1ad625_25.png)

设置好选股池后，我们需要建立一个定时调仓机制。这里我们选择按月进行调仓。

![](img/8596d324626ef69f880f3fd43e1ad625_27.png)

![](img/8596d324626ef69f880f3fd43e1ad625_29.png)

以下是设置定时调仓的代码：

![](img/8596d324626ef69f880f3fd43e1ad625_31.png)

```python
# 设置按月调仓的定时器
run_monthly(rebalance, 1)
```

![](img/8596d324626ef69f880f3fd43e1ad625_33.png)

![](img/8596d324626ef69f880f3fd43e1ad625_35.png)

这里，`rebalance` 是我们即将定义的核心调仓函数，数字 `1` 表示在每个月的第一个交易日执行。

![](img/8596d324626ef69f880f3fd43e1ad625_37.png)

## 定义核心调仓函数

![](img/8596d324626ef69f880f3fd43e1ad625_39.png)

既然我们已经设置了定时器，接下来就需要定义 `rebalance` 函数。这个函数将负责执行每月一次的选股和调仓操作。

![](img/8596d324626ef69f880f3fd43e1ad625_41.png)

![](img/8596d324626ef69f880f3fd43e1ad625_43.png)

在 `rebalance` 函数中，最核心的任务是根据财务指标对股票进行评分和排序。为此，我们需要先获取股票的排名列表。

![](img/8596d324626ef69f880f3fd43e1ad625_45.png)

![](img/8596d324626ef69f880f3fd43e1ad625_47.png)

![](img/8596d324626ef69f880f3fd43e1ad625_49.png)

```python
def rebalance(context):
    # 调用函数获取评分后的股票排名
    ranked_stocks = get_ranked_stocks(context)
    # ... 后续的调仓操作将基于这个排名
```

![](img/8596d324626ef69f880f3fd43e1ad625_51.png)

## 实现股票评分函数

![](img/8596d324626ef69f880f3fd43e1ad625_53.png)

![](img/8596d324626ef69f880f3fd43e1ad625_55.png)

上一节我们搭建了调仓函数的框架，本节中我们来实现具体的股票评分函数 `get_ranked_stocks`。

![](img/8596d324626ef69f880f3fd43e1ad625_57.png)

这个函数的主要任务是查询财务数据，并根据预设的指标对股票进行打分。我们首先需要进行数据查询。

![](img/8596d324626ef69f880f3fd43e1ad625_59.png)

![](img/8596d324626ef69f880f3fd43e1ad625_61.png)

以下是查询财务数据的步骤：

![](img/8596d324626ef69f880f3fd43e1ad625_63.png)

1.  **构建查询语句**：我们需要指定要查询哪些财务指标。
2.  **应用筛选器**：将查询范围限定在我们之前指定的沪深300股票池中。
3.  **获取并处理数据**：执行查询，并对返回的数据格式进行转置，以便后续处理。

![](img/8596d324626ef69f880f3fd43e1ad625_65.png)

我们首先查询一组“越高越好”的指标，例如每股收益（EPS）、净资产收益率（ROE）和投入资本回报率（ROIC）。

![](img/8596d324626ef69f880f3fd43e1ad625_67.png)

![](img/8596d324626ef69f880f3fd43e1ad625_69.png)

```python
def get_ranked_stocks(context):
    # 1. 构建查询语句（越高越好的指标）
    query_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)，越高越好
        fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)，越高越好
        fundamentals.financial_indicator.roic       # 投入资本回报率 (ROIC)，越高越好
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 2. 筛选沪深300股票池
    )
    # 3. 获取数据并转置格式
    df_up = get_fundamentals(query_up).T
```

![](img/8596d324626ef69f880f3fd43e1ad625_71.png)

![](img/8596d324626ef69f880f3fd43e1ad625_73.png)

同样地，我们还需要查询一组“越低越好”的指标，例如资产负债率、市净率（PB）和市值。

```python
    # 查询另一组“越低越好”的指标
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset, # 资产负债率，越低越好
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率 (PB)，越低越好
        fundamentals.eod_derivative_indicator.market_cap # 市值，越低越好
    ).filter(
        fundamentals.stockcode.in_(context.hs300)
    )
    df_down = get_fundamentals(query_down).T
```

![](img/8596d324626ef69f880f3fd43e1ad625_75.png)

**请注意一个关键点**：数据转置操作 `.T` 应该在 `get_fundamentals()` 函数执行之后，对返回的 `DataFrame` 进行操作，而不是包含在查询语句内部。上面的代码已进行正确放置。

![](img/8596d324626ef69f880f3fd43e1ad625_77.png)

至此，我们就得到了两个 `DataFrame`：`df_up` 包含越高越好的指标数据，`df_down` 包含越低越好的指标数据。每个 `DataFrame` 的形状大致是（股票数量， 指标数量），例如（300， 3）。

![](img/8596d324626ef69f880f3fd43e1ad625_79.png)

![](img/8596d324626ef69f880f3fd43e1ad625_81.png)

本节课中我们一起学习了量化策略的初始化步骤，包括设置选股池、建立定时调仓机制，并实现了财务数据查询函数的核心部分。我们成功获取了用于后续打分的“越高越好”和“越低越好”两类指标数据。在下一节中，我们将基于这些数据，对股票进行综合评分和排序。