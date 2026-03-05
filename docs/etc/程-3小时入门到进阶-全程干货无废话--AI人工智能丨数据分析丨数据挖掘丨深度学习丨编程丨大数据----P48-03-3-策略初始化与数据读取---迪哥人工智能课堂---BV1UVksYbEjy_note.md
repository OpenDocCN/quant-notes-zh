# Python金融分析与量化交易实战教程：P48：03-3-策略初始化与数据读取 📊

在本节课中，我们将学习如何初始化一个量化交易策略，并读取用于因子打分选股所需的财务数据。我们将从指定股票池开始，逐步完成数据查询与准备。

![](img/6a83057247f5db4542e6f3701c6d1b6a_1.png)

## 策略初始化

![](img/6a83057247f5db4542e6f3701c6d1b6a_3.png)

首先，我们需要在策略的 `initialize` 函数中完成一些基础设置。上一节我们介绍了策略的基本框架，本节中我们来看看如何具体配置。

![](img/6a83057247f5db4542e6f3701c6d1b6a_5.png)

![](img/6a83057247f5db4542e6f3701c6d1b6a_7.png)

### 指定股票池

![](img/6a83057247f5db4542e6f3701c6d1b6a_9.png)

第一步是确定选股的范围。为了获得更理想的效果，我们将选股范围限定在沪深300指数的成分股中。

![](img/6a83057247f5db4542e6f3701c6d1b6a_11.png)

```python
# 在initialize函数中指定股票池
g.pool = '沪深300'
```

此代码定义了一个全局变量 `g.pool`，用于存储我们的选股范围。

### 初始化排名变量

![](img/6a83057247f5db4542e6f3701c6d1b6a_13.png)

接下来，我们需要定义一个变量，用于存储后续计算出的股票排名结果。

![](img/6a83057247f5db4542e6f3701c6d1b6a_15.png)

![](img/6a83057247f5db4542e6f3701c6d1b6a_17.png)

```python
# 初始化一个变量来存储股票排名
g.rank = None
```

![](img/6a83057247f5db4542e6f3701c6d1b6a_19.png)

变量 `g.rank` 将在调仓函数中被赋值，用于筛选排名靠前的股票。

![](img/6a83057247f5db4542e6f3701c6d1b6a_21.png)

### 设置定时调仓

![](img/6a83057247f5db4542e6f3701c6d1b6a_23.png)

![](img/6a83057247f5db4542e6f3701c6d1b6a_25.png)

量化策略需要定期执行调仓操作。我们将设置一个按月调仓的定时器。

![](img/6a83057247f5db4542e6f3701c6d1b6a_27.png)

```python
# 设置按月调仓
run_monthly(rebalance, 1)
```

![](img/6a83057247f5db4542e6f3701c6d1b6a_29.png)

这里，`run_monthly` 是一个定时器函数，它会在每个月第一个交易日调用我们自定义的 `rebalance` 函数。

![](img/6a83057247f5db4542e6f3701c6d1b6a_31.png)

## 定义调仓函数

![](img/6a83057247f5db4542e6f3701c6d1b6a_33.png)

设置好定时器后，我们需要定义具体的调仓函数 `rebalance`。该函数的核心任务是获取股票数据并进行评分。

![](img/6a83057247f5db4542e6f3701c6d1b6a_35.png)

![](img/6a83057247f5db4542e6f3701c6d1b6a_37.png)

```python
def rebalance(context):
    # 调仓函数，核心是获取评分并选股
    g.rank = get_stocks()
```

在 `rebalance` 函数中，我们调用 `get_stocks` 函数来获取经过评分的股票排名。

![](img/6a83057247f5db4542e6f3701c6d1b6a_39.png)

## 数据读取与处理

![](img/6a83057247f5db4542e6f3701c6d1b6a_41.png)

现在，我们来构建最关键的 `get_stocks` 函数，它负责查询财务数据并进行初步处理。

![](img/6a83057247f5db4542e6f3701c6d1b6a_43.png)

![](img/6a83057247f5db4542e6f3701c6d1b6a_45.png)

### 构建查询语句

![](img/6a83057247f5db4542e6f3701c6d1b6a_47.png)

该函数的第一步是构建查询语句，以获取我们所需的财务指标。以下是需要查询的指标列表：

*   **越高越好型指标**：这类指标数值越大，通常代表公司基本面越好。
    *   每股收益
    *   净资产收益率
    *   投入资本回报率
*   **越低越好型指标**：这类指标数值越小，通常代表公司估值越低或风险越小。
    *   资产负债率
    *   市净率
    *   总市值

![](img/6a83057247f5db4542e6f3701c6d1b6a_49.png)

我们分别构建两个查询来获取这两类数据。

![](img/6a83057247f5db4542e6f3701c6d1b6a_51.png)

![](img/6a83057247f5db4542e6f3701c6d1b6a_53.png)

```python
def get_stocks():
    # 查询越高越好的指标
    q_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益
        fundamentals.financial_indicator.roe, # 净资产收益率
        fundamentals.financial_indicator.roic # 投入资本回报率
    ).filter(
        fundamentals.stockcode.in_(g.pool) # 限定在股票池中
    )

    # 查询越低越好的指标
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率
        fundamentals.stock_valuation.market_cap # 总市值
    ).filter(
        fundamentals.stockcode.in_(g.pool) # 限定在股票池中
    )
```

![](img/6a83057247f5db4542e6f3701c6d1b6a_55.png)

![](img/6a83057247f5db4542e6f3701c6d1b6a_57.png)

### 执行查询并转换数据

构建好查询语句后，我们使用 `get_fundamentals` 函数执行查询。获取到的数据格式需要进行转置，以便股票代码作为行索引，指标作为列索引。

```python
    # 执行查询，获取DataFrame并转置
    df_up = get_fundamentals(q_up).T
    df_down = get_fundamentals(q_down).T

    # 打印数据形状以确认
    log.info(‘越高越好指标数据形状：‘, df_up.shape) # 预期为 (300, 3)
    log.info(‘越低越好指标数据形状：‘, df_down.shape) # 预期为 (300, 3)

    return df_up, df_down
```

![](img/6a83057247f5db4542e6f3701c6d1b6a_59.png)

执行后，`df_up` 和 `df_down` 将是两个 `DataFrame`，其中包含了沪深300成分股对应的六项财务指标数据，为下一步的因子打分做好了准备。

![](img/6a83057247f5db4542e6f3701c6d1b6a_61.png)

## 总结

![](img/6a83057247f5db4542e6f3701c6d1b6a_63.png)

![](img/6a83057247f5db4542e6f3701c6d1b6a_65.png)

本节课中我们一起学习了量化交易策略的初始化与数据读取步骤。我们首先在 `initialize` 函数中设定了股票池、排名变量和调仓频率。然后，我们定义了核心的 `get_stocks` 函数，通过构建查询语句从财务数据库中获取“越高越好”和“越低越好”两类关键指标，并对数据进行了转置处理，为后续的因子综合评分奠定了数据基础。