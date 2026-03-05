# Python金融量化交易：P50：策略初始化与数据读取 📊

![](img/aa8646706d61e7b3010a942af04a96a1_0.png)

![](img/aa8646706d61e7b3010a942af04a96a1_2.png)

在本节课中，我们将学习如何为一个因子打分选股策略进行初始化，并读取所需的股票基本面数据。我们将从定义选股池和调仓周期开始，逐步构建数据查询函数，为后续的因子计算与股票排名打下基础。

![](img/aa8646706d61e7b3010a942af04a96a1_4.png)

## 策略初始化

![](img/aa8646706d61e7b3010a942af04a96a1_6.png)

![](img/aa8646706d61e7b3010a942af04a96a1_8.png)

![](img/aa8646706d61e7b3010a942af04a96a1_10.png)

首先，我们需要在新的策略文件中进行初始化设置。这包括指定选股范围、定义存储排名的变量以及设置调仓定时器。

![](img/aa8646706d61e7b3010a942af04a96a1_12.png)

![](img/aa8646706d61e7b3010a942af04a96a1_14.png)

### 定义选股池与变量

![](img/aa8646706d61e7b3010a942af04a96a1_16.png)

![](img/aa8646706d61e7b3010a942af04a96a1_18.png)

在策略的 `context` 对象中，我们首先需要指定选股的范围。为了使策略效果更明显，我们选择在沪深300指数的成分股中进行选股。

![](img/aa8646706d61e7b3010a942af04a96a1_20.png)

```python
# 在context中指定选股池为沪深300指数成分股
context.hs300 = ‘沪深300’
```

![](img/aa8646706d61e7b3010a942af04a96a1_22.png)

![](img/aa8646706d61e7b3010a942af04a96a1_24.png)

接下来，我们需要定义一个变量，用于存储后续计算得到的股票排名结果。

![](img/aa8646706d61e7b3010a942af04a96a1_26.png)

![](img/aa8646706d61e7b3010a942af04a96a1_28.png)

```python
# 在context中定义一个变量，用于存储股票排名
context.rank = None
```

![](img/aa8646706d61e7b3010a942af04a96a1_30.png)

### 设置调仓定时器

![](img/aa8646706d61e7b3010a942af04a96a1_32.png)

![](img/aa8646706d61e7b3010a942af04a96a1_34.png)

![](img/aa8646706d61e7b3010a942af04a96a1_36.png)

然后，我们需要设置一个定时器，来定期执行调仓操作。这里我们选择按月进行调仓。

![](img/aa8646706d61e7b3010a942af04a96a1_38.png)

```python
# 设置按月调仓的定时器，指定调仓函数为REBALANCE
schedule_function(rebalance, date_rule=month_end(), time_rule=market_open())
```

![](img/aa8646706d61e7b3010a942af04a96a1_40.png)

![](img/aa8646706d61e7b3010a942af04a96a1_42.png)

## 构建数据查询函数

![](img/aa8646706d61e7b3010a942af04a96a1_44.png)

![](img/aa8646706d61e7b3010a942af04a96a1_46.png)

上一节我们完成了策略的基本框架设置，本节中我们来看看如何构建核心的数据查询函数。这个函数将负责获取我们进行因子打分所需的所有基本面数据。

![](img/aa8646706d61e7b3010a942af04a96a1_48.png)

### 定义数据获取函数

![](img/aa8646706d61e7b3010a942af04a96a1_50.png)

我们首先定义一个名为 `get_stocks` 的函数。这个函数的核心任务是查询并返回我们所需的股票基本面指标数据。

![](img/aa8646706d61e7b3010a942af04a96a1_52.png)

![](img/aa8646706d61e7b3010a942af04a96a1_54.png)

```python
def get_stocks(context):
    # 此函数将执行数据查询与处理
    pass
```

![](img/aa8646706d61e7b3010a942af04a96a1_56.png)

![](img/aa8646706d61e7b3010a942af04a96a1_58.png)

![](img/aa8646706d61e7b3010a942af04a96a1_60.png)

### 构建查询语句

![](img/aa8646706d61e7b3010a942af04a96a1_62.png)

在 `get_stocks` 函数内部，第一步是构建查询语句。我们将查询多个基本面指标，并根据其特性分为“越高越好”和“越低越好”两类。

![](img/aa8646706d61e7b3010a942af04a96a1_64.png)

![](img/aa8646706d61e7b3010a942af04a96a1_66.png)

以下是构建查询语句的步骤：

![](img/aa8646706d61e7b3010a942af04a96a1_68.png)

1.  **查询“越高越好”的指标**：我们首先查询那些数值越大代表公司质量越好的指标。
    *   **每股收益**：`fundamentals.eod_derivative_indicator.eps`
    *   **净资产收益率**：`fundamentals.financial_indicator.roe`
    *   **投入资本回报率**：`fundamentals.financial_indicator.roic`

![](img/aa8646706d61e7b3010a942af04a96a1_70.png)

![](img/aa8646706d61e7b3010a942af04a96a1_72.png)

![](img/aa8646706d61e7b3010a942af04a96a1_74.png)

2.  **应用筛选条件**：我们通过 `filter` 方法，将查询范围限定在之前定义的沪深300选股池内。
    *   `fundamentals.stockcode.in_(context.hs300)`

![](img/aa8646706d61e7b3010a942af04a96a1_76.png)

3.  **执行查询并转换数据格式**：使用 `get_fundamentals` 执行查询。查询返回的数据通常需要进行转置，以便股票代码成为行索引，方便后续处理。
    *   `get_fundamentals(query).T`

![](img/aa8646706d61e7b3010a942af04a96a1_78.png)

4.  **查询“越低越好”的指标**：用同样的方法，查询那些数值越小越好的指标。
    *   **资产负债率**：`fundamentals.financial_indicator.debt_to_asset`
    *   **市净率**：`fundamentals.eod_derivative_indicator.pb_ratio`
    *   **总市值**：`fundamentals.eod_derivative_indicator.market_cap`

![](img/aa8646706d61e7b3010a942af04a96a1_80.png)

![](img/aa8646706d61e7b3010a942af04a96a1_82.png)

### 函数实现代码

![](img/aa8646706d61e7b3010a942af04a96a1_84.png)

![](img/aa8646706d61e7b3010a942af04a96a1_86.png)

将以上步骤整合，`get_stocks` 函数的实现代码如下：

```python
def get_stocks(context):
    # 1. 查询“越高越好”的指标
    query_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益
        fundamentals.financial_indicator.roe,       # 净资产收益率
        fundamentals.financial_indicator.roic       # 投入资本回报率
    ).filter(
        fundamentals.stockcode.in_(context.hs300)   # 筛选沪深300成分股
    )
    df_up = get_fundamentals(query_up).T # 执行查询并转置DataFrame

    # 2. 查询“越低越好”的指标
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率
        fundamentals.eod_derivative_indicator.market_cap # 总市值
    ).filter(
        fundamentals.stockcode.in_(context.hs300)        # 筛选沪深300成分股
    )
    df_down = get_fundamentals(query_down).T # 执行查询并转置DataFrame

    # 返回两个DataFrame，供后续评分使用
    return df_up, df_down
```

![](img/aa8646706d61e7b3010a942af04a96a1_88.png)

执行此函数后，我们将得到两个 `DataFrame`：
*   `df_up`：形状约为 `(300, 3)`，包含300支股票的三个“越高越好”型指标。
*   `df_down`：形状约为 `(300, 3)`，包含300支股票的三个“越低越好”型指标。

![](img/aa8646706d61e7b3010a942af04a96a1_90.png)

## 总结

![](img/aa8646706d61e7b3010a942af04a96a1_92.png)

![](img/aa8646706d61e7b3010a942af04a96a1_94.png)

本节课中我们一起学习了量化策略初始化的关键步骤。我们首先在 `context` 中定义了选股池和排名变量，并设置了按月调仓的定时器。随后，我们构建了核心的 `get_stocks` 数据查询函数，该函数能够从沪深300成分股中，分别查询出“越高越好”和“越低越好”两类共六个基本面指标，并将数据整理成便于后续处理的 `DataFrame` 格式。至此，我们已经准备好了策略运行所需的基础框架和原始数据。在接下来的课程中，我们将基于这些数据，实现因子打分和股票排名的逻辑。