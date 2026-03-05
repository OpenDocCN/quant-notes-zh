# 量化投资入门：P47：策略初始化与数据读取 📊

![](img/479097f736a98fa3445d41ea44f848d7_1.png)

在本节课中，我们将学习如何初始化一个量化选股策略，并完成核心数据的读取与准备工作。我们将从指定股票池开始，逐步构建一个能够按月调仓、基于多因子打分的选股框架。

![](img/479097f736a98fa3445d41ea44f848d7_3.png)

## 策略初始化

![](img/479097f736a98fa3445d41ea44f848d7_5.png)

首先，我们需要创建一个新的策略文件，并开始编写代码。第一步是在策略的 `initialize` 函数中，定义我们的选股范围和后续会用到的变量。

![](img/479097f736a98fa3445d41ea44f848d7_7.png)

### 指定股票池

![](img/479097f736a98fa3445d41ea44f848d7_9.png)

![](img/479097f736a98fa3445d41ea44f848d7_11.png)

为了获得更稳定的回测效果，我们选择在沪深300指数的成分股范围内进行选股。这可以通过在 `context` 对象中设置 `universe` 属性来实现。

```python
# 在 initialize 函数中指定选股池
context.universe = ‘沪深300’
```

通过以上代码，我们设定了策略的选股范围，即只在沪深300成分股中进行筛选。

### 定义排名变量

![](img/479097f736a98fa3445d41ea44f848d7_13.png)

![](img/479097f736a98fa3445d41ea44f848d7_15.png)

在调仓时，我们需要根据因子评分对股票进行排序，并选择排名靠前的股票。因此，我们需要在 `context` 中预先定义一个变量，用于存储每次调仓时的股票排名结果。

![](img/479097f736a98fa3445d41ea44f848d7_17.png)

![](img/479097f736a98fa3445d41ea44f848d7_19.png)

```python
# 在 context 中预定义排名变量
context.rank = None
```

这样，在后续的调仓函数中，我们就可以将计算好的排名结果赋值给 `context.rank`，方便随时调用。

![](img/479097f736a98fa3445d41ea44f848d7_21.png)

![](img/479097f736a98fa3445d41ea44f848d7_23.png)

## 设置定时调仓

![](img/479097f736a98fa3445d41ea44f848d7_25.png)

策略需要定期执行选股和调仓操作。接下来，我们设置一个按月运行的定时器，它会在每个月的指定日期触发我们的调仓函数。

![](img/479097f736a98fa3445d41ea44f848d7_27.png)

![](img/479097f736a98fa3445d41ea44f848d7_29.png)

以下是设置定时器的代码：

```python
# 设置按月调仓的定时器
schedule_function(rebalance, date_rule=month_end(), time_rule=market_open())
```

![](img/479097f736a98fa3445d41ea44f848d7_31.png)

![](img/479097f736a98fa3445d41ea44f848d7_33.png)

这里，`rebalance` 是我们即将定义的核心调仓函数。`month_end()` 规则确保函数在每月最后一个交易日运行，`market_open()` 规则确保在开盘时执行。

![](img/479097f736a98fa3445d41ea44f848d7_35.png)

## 构建数据获取函数

![](img/479097f736a98fa3445d41ea44f848d7_37.png)

调仓的核心是依据因子对股票进行评分。为此，我们需要一个专门的函数来获取所需的财务数据。下面，我们来定义这个名为 `get_stocks` 的函数。

### 查询财务指标

![](img/479097f736a98fa3445d41ea44f848d7_39.png)

![](img/479097f736a98fa3445d41ea44f848d7_41.png)

在 `get_stocks` 函数内部，我们首先需要使用 `get_fundamentals` 接口查询股票的财务数据。我们将查询两类因子：**越高越好型**和**越低越好型**。

![](img/479097f736a98fa3445d41ea44f848d7_43.png)

![](img/479097f736a98fa3445d41ea44f848d7_45.png)

以下是构建查询语句（query）的步骤：

1.  **越高越好型因子**：这类因子数值越大，代表股票基本面越好。我们选取以下三个指标：
    *   每股收益（EPS）
    *   净资产收益率（ROE）
    *   投入资本回报率（ROIC）

![](img/479097f736a98fa3445d41ea44f848d7_47.png)

2.  **越低越好型因子**：这类因子数值越小，代表股票风险可能越低或估值越有吸引力。我们同样选取三个指标：
    *   资产负债率
    *   市净率（PB）
    *   总市值

![](img/479097f736a98fa3445d41ea44f848d7_49.png)

具体的查询代码如下所示。我们分别构建两个查询，并使用 `filter` 条件将结果限定在之前设定的沪深300股票池内。

![](img/479097f736a98fa3445d41ea44f848d7_51.png)

![](img/479097f736a98fa3445d41ea44f848d7_53.png)

```python
def get_stocks(context):
    # 1. 查询“越高越好”型因子
    query_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益
        fundamentals.financial_indicator.roe, # 净资产收益率
        fundamentals.financial_indicator.roic # 投入资本回报率
    ).filter(
        fundamentals.stockcode.in_(context.universe)
    )
    df_up = get_fundamentals(query_up).T # 获取数据并转置，方便处理

    # 2. 查询“越低越好”型因子
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率
        fundamentals.eod_derivative_indicator.market_cap # 总市值
    ).filter(
        fundamentals.stockcode.in_(context.universe)
    )
    df_down = get_fundamentals(query_down).T # 获取数据并转置

    return df_up, df_down
```

![](img/479097f736a98fa3445d41ea44f848d7_55.png)

代码说明：
*   `query(...)` 用于指定要查询的财务指标字段。
*   `.filter(...)` 用于过滤股票，确保只获取沪深300成分股的数据。
*   `get_fundamentals(...)` 执行查询并返回一个 `DataFrame`。
*   `.T` 是对 `DataFrame` 进行转置操作，将股票代码作为行索引，因子作为列，这样更便于后续的排序和计算。

执行完上述查询后，`df_up` 和 `df_down` 将是两个 `DataFrame`，其形状为 `(300, 3)`，即300支股票，每支股票对应3个因子值。

![](img/479097f736a98fa3445d41ea44f848d7_57.png)

## 总结

![](img/479097f736a98fa3445d41ea44f848d7_59.png)

本节课中，我们一起完成了量化选股策略的初始化工作：
1.  **设定股票池**：将选股范围限定在沪深300成分股，以提升策略的稳定性。
2.  **定义排名变量**：在 `context` 中预留了存储股票排名的位置。
3.  **设置调仓周期**：通过 `schedule_function` 建立了按月调仓的机制。
4.  **构建数据获取函数**：编写了 `get_stocks` 函数，成功查询了6个关键财务因子数据，并将其分为“越高越好”和“越低越好”两类，为下一步的因子评分做好了数据准备。

![](img/479097f736a98fa3445d41ea44f848d7_61.png)

![](img/479097f736a98fa3445d41ea44f848d7_63.png)

在下一节中，我们将基于本节获取到的因子数据，学习如何对股票进行综合评分与排序，最终完成选股逻辑。