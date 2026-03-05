# 人工智能数据挖掘实战：P50：策略初始化与数据读取 📊

在本节课中，我们将学习如何初始化一个量化选股策略，并完成核心数据的读取与准备工作。我们将从指定股票池开始，逐步构建一个能够获取并处理多因子数据的框架。

![](img/5ec54c53ac401434475f5dbe3d207bec_1.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_2.png)

## 策略初始化

![](img/5ec54c53ac401434475f5dbe3d207bec_4.png)

首先，我们需要创建一个新的策略文件。策略的初始化工作主要在 `context` 对象中完成，它定义了策略运行的基本环境。

![](img/5ec54c53ac401434475f5dbe3d207bec_5.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_6.png)

上一节我们介绍了策略的基本概念，本节中我们来看看如何具体配置 `context`。

![](img/5ec54c53ac401434475f5dbe3d207bec_8.png)

以下是初始化 `context` 时需要完成的两个核心步骤：

![](img/5ec54c53ac401434475f5dbe3d207bec_9.png)

1.  **指定股票池**：我们需要确定策略从哪个股票集合中筛选股票。为了使策略表现更优，我们选择在沪深300指数的成分股中进行选股。
    ```python
    context.hs300 = ‘沪深300’
    ```

![](img/5ec54c53ac401434475f5dbe3d207bec_11.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_12.png)

2.  **定义排名存储变量**：为了在调仓时获取评分排名靠前的股票，我们需要在 `context` 中预先定义一个变量来存储排名结果。
    ```python
    context.rank = None  # 用于存储股票排名结果
    ```

![](img/5ec54c53ac401434475f5dbe3d207bec_14.png)

## 设置定时调仓器

![](img/5ec54c53ac401434475f5dbe3d207bec_16.png)

初始化环境后，我们需要设定策略的运行频率。这里我们使用按月调仓的方式。

![](img/5ec54c53ac401434475f5dbe3d207bec_18.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_19.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_20.png)

以下是设置定时器的代码，它指定了每月执行一次名为 `rebalance` 的函数来进行调仓操作。
```python
schedule_function(rebalance, date_rule=month_end())
```

![](img/5ec54c53ac401434475f5dbe3d207bec_22.png)

## 定义调仓函数

![](img/5ec54c53ac401434475f5dbe3d207bec_24.png)

既然我们已经设置好了按月调仓，接下来就需要定义具体的调仓函数 `rebalance`。这个函数是策略的核心，负责执行选股和换仓逻辑。

![](img/5ec54c53ac401434475f5dbe3d207bec_25.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_26.png)

在 `rebalance` 函数中，最核心的任务是根据因子对股票进行评分和排序。为此，我们首先需要获取排序后的股票列表。我们将创建一个名为 `get_stocks` 的辅助函数来完成实际的评分和选股工作。
```python
def rebalance(context, data):
    # 调用选股函数获取股票列表
    stock_list = get_stocks(context)
    # ... 后续的调仓逻辑（如下单交易）将在这里添加
```

![](img/5ec54c53ac401434475f5dbe3d207bec_28.png)

## 构建数据获取函数

![](img/5ec54c53ac401434475f5dbe3d207bec_30.png)

现在，让我们开始构建 `get_stocks` 函数。该函数的第一步是获取所需的因子数据。

![](img/5ec54c53ac401434475f5dbe3d207bec_32.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_33.png)

我们计划使用六个财务指标作为选股因子，并将其分为两类：“越高越好”的因子和“越低越好”的因子。

![](img/5ec54c53ac401434475f5dbe3d207bec_34.png)

以下是获取“越高越好”因子数据的查询语句构建过程。我们使用 `query` 函数来指定需要查询的指标。

![](img/5ec54c53ac401434475f5dbe3d207bec_36.png)

1.  **每股收益**：这是一个典型的“越高越好”的指标，反映了公司的盈利能力。
    ```python
    q = query(fundamentals.eod_derivative_indicator.eps)
    ```

![](img/5ec54c53ac401434475f5dbe3d207bec_37.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_38.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_39.png)

2.  **净资产收益率**：同样是“越高越好”，衡量公司运用自有资本的效率。
    ```python
    q = query(fundamentals.financial_indicator.return_on_equity)
    ```

3.  **投入资本回报率**：也是“越高越好”，评估公司投资资本的有效性。
    ```python
    q = query(fundamentals.financial_indicator.return_on_invested_capital)
    ```

![](img/5ec54c53ac401434475f5dbe3d207bec_41.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_42.png)

在构建查询时，我们还需要添加一个过滤器，确保只获取属于我们股票池（沪深300）的股票数据。
```python
q = q.filter(fundamentals.stockcode.in_(context.hs300))
```

![](img/5ec54c53ac401434475f5dbe3d207bec_43.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_44.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_45.png)

查询构建完成后，我们使用 `get_fundamentals` 函数执行查询，并对返回的 `DataFrame` 进行转置，使股票代码成为行索引，方便后续处理。
```python
df_up = get_fundamentals(q).T
```

## 获取“越低越好”的因子

![](img/5ec54c53ac401434475f5dbe3d207bec_47.png)

采用相同的逻辑，我们获取另一组“越低越好”的因子数据。

![](img/5ec54c53ac401434475f5dbe3d207bec_49.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_51.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_52.png)

以下是这组因子的构成：

![](img/5ec54c53ac401434475f5dbe3d207bec_54.png)

1.  **资产负债率**：该指标“越低越好”，过高的负债可能增加财务风险。
2.  **市净率的倒数**：我们查询原始的市净率值。因为市净率“越低越好”，所以其倒数就变成了“越高越好”。但在本组中，我们直接使用原始PB值，并将其归类为“越低越好”。
3.  **总市值**：通常认为小市值股票可能有更高的成长性，因此市值也属于“越低越好”的因子。

对应的查询代码如下：
```python
q_down = query(
    fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率
    fundamentals.eod_derivative_indicator.pb_ratio,       # 市净率
    fundamentals.eod_derivative_indicator.market_cap      # 总市值
).filter(fundamentals.stockcode.in_(context.hs300))

df_down = get_fundamentals(q_down).T
```

![](img/5ec54c53ac401434475f5dbe3d207bec_56.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_57.png)

执行完上述代码后，我们将得到两个 `DataFrame`：`df_up` 包含了三个“越高越好”的因子数据，`df_down` 包含了三个“越低越好”的因子数据。每个 `DataFrame` 的形状都是 `(300, 3)`，代表300支股票和3个因子。

![](img/5ec54c53ac401434475f5dbe3d207bec_59.png)

![](img/5ec54c53ac401434475f5dbe3d207bec_60.png)

本节课中我们一起学习了量化策略的初始化流程，包括设置股票池、定义调仓频率，并完成了多因子数据的获取与预处理。我们成功将财务指标分为“越高越好”和“越低越好”两类，为下一节课的因子评分与股票排名打下了坚实的数据基础。