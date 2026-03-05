# 量化交易策略：P50：3-策略初始化与数据读取 📊

在本节课中，我们将学习如何初始化一个量化交易策略，并读取用于因子打分选股所需的财务数据。我们将从指定股票池开始，逐步完成数据查询和准备步骤。

![](img/25ecc4f6195ddcff47d053891d4e1b1f_1.png)

## 策略初始化

![](img/25ecc4f6195ddcff47d053891d4e1b1f_3.png)

首先，我们需要在策略的 `context` 对象中指定股票池。为了获得更好的回测效果，我们选择在沪深300指数的成分股中进行选股。

![](img/25ecc4f6195ddcff47d053891d4e1b1f_5.png)

```python
# 在context中指定股票池为沪深300
context.stock_pool = '沪深300'
```

接下来，我们需要定义一个变量来存储股票的排名结果，以便在调仓时使用。

```python
# 定义一个变量来存储股票排名
context.ranked_stocks = None
```

![](img/25ecc4f6195ddcff47d053891d4e1b1f_7.png)

## 设置调仓定时器

![](img/25ecc4f6195ddcff47d053891d4e1b1f_9.png)

![](img/25ecc4f6195ddcff47d053891d4e1b1f_11.png)

上一节我们指定了股票池，本节中我们来看看如何设置调仓频率。我们将使用一个定时器，按月执行调仓操作。

```python
# 设置按月调仓的定时器
schedule_function(rebalance, date_rule=monthly(), time_rule=market_open())
```

![](img/25ecc4f6195ddcff47d053891d4e1b1f_13.png)

这里，`rebalance` 是我们即将定义的核心调仓函数。

![](img/25ecc4f6195ddcff47d053891d4e1b1f_15.png)

![](img/25ecc4f6195ddcff47d053891d4e1b1f_17.png)

## 定义调仓函数

![](img/25ecc4f6195ddcff47d053891d4e1b1f_19.png)

现在，我们来定义 `rebalance` 函数。这个函数的核心任务是获取股票评分并据此进行调仓。

![](img/25ecc4f6195ddcff47d053891d4e1b1f_21.png)

```python
def rebalance(context, data):
    # 调用函数获取评分后的股票
    ranked_stocks = get_ranked_stocks(context)
    # 将排名结果存入context
    context.ranked_stocks = ranked_stocks
    # 后续可根据排名进行交易操作（此处省略）
```

![](img/25ecc4f6195ddcff47d053891d4e1b1f_23.png)

## 数据获取与处理

![](img/25ecc4f6195ddcff47d053891d4e1b1f_25.png)

调仓函数依赖于股票评分，而评分需要财务数据。因此，我们需要一个函数来获取和处理这些数据。

以下是获取财务数据的步骤，我们将分别获取“越高越好”和“越低越好”两类指标。

![](img/25ecc4f6195ddcff47d053891d4e1b1f_27.png)

### 获取“越高越好”的指标

![](img/25ecc4f6195ddcff47d053891d4e1b1f_29.png)

我们首先查询那些数值越大代表股票越好的财务指标。

![](img/25ecc4f6195ddcff47d053891d4e1b1f_31.png)

```python
def get_ranked_stocks(context):
    # 构建查询语句，获取“越高越好”的指标
    query_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)，越高越好
        fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)，越高越好
        fundamentals.financial_indicator.roa        # 总资产收益率 (ROA)，越高越好
    ).filter(
        fundamentals.stockcode.in_(context.stock_pool) # 过滤股票池
    )
    # 执行查询并转置DataFrame，使股票代码成为行索引
    df_up = get_fundamentals(query_up).T
```

### 获取“越低越好”的指标

![](img/25ecc4f6195ddcff47d053891d4e1b1f_33.png)

接下来，我们查询那些数值越小代表股票越好的财务指标。

![](img/25ecc4f6195ddcff47d053891d4e1b1f_35.png)

![](img/25ecc4f6195ddcff47d053891d4e1b1f_37.png)

```python
    # 构建查询语句，获取“越低越好”的指标
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset, # 资产负债率，越低越好
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率 (PB)，越低越好
        fundamentals.eod_derivative_indicator.market_cap # 市值，越低越好（小市值效应）
    ).filter(
        fundamentals.stockcode.in_(context.stock_pool) # 过滤股票池
    )
    # 执行查询并转置DataFrame
    df_down = get_fundamentals(query_down).T
```

![](img/25ecc4f6195ddcff47d053891d4e1b1f_39.png)

![](img/25ecc4f6195ddcff47d053891d4e1b1f_41.png)

### 数据整合

现在，我们得到了两个DataFrame：`df_up` 和 `df_down`。每个DataFrame的形状大致为 `(股票数量, 指标数量)`，例如 `(300, 3)`。这些数据将用于后续的因子打分计算。

```python
    # 此时，df_up 和 df_down 包含了我们所需的因子数据
    # df_up: 包含每股收益、净资产收益率、总资产收益率
    # df_down: 包含资产负债率、市净率、市值
    # 后续步骤将基于这些数据进行评分（本节课不展开）
    return df_up, df_down
```

![](img/25ecc4f6195ddcff47d053891d4e1b1f_43.png)

## 总结

![](img/25ecc4f6195ddcff47d053891d4e1b1f_45.png)

本节课中我们一起学习了量化策略初始化的关键步骤：
1.  **指定股票池**：确定了在沪深300成分股内进行选股。
2.  **设置调仓频率**：使用定时器设定了按月调仓的规则。
3.  **定义核心函数**：构建了 `rebalance` 调仓函数的框架。
4.  **获取财务数据**：成功查询并获取了用于因子打分的两大类（“越高越好”和“越低越好”）财务指标数据，并进行了适当的格式处理（转置）。

![](img/25ecc4f6195ddcff47d053891d4e1b1f_47.png)

![](img/25ecc4f6195ddcff47d053891d4e1b1f_49.png)

我们已经准备好了策略的骨架和所需的数据，为下一节课实现具体的因子打分逻辑打下了坚实的基础。