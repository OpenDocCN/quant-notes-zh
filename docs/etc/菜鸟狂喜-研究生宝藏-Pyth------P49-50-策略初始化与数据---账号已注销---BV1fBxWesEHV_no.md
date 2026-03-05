# Python金融量化分析：P49：策略初始化与数据读取 📊

![](img/30e7d42730829e088e16b7c0a34deebe_1.png)

![](img/30e7d42730829e088e16b7c0a34deebe_3.png)

![](img/30e7d42730829e088e16b7c0a34deebe_5.png)

在本节课中，我们将学习如何为一个量化选股策略进行初始化，并读取所需的财务数据。我们将从定义股票池开始，逐步构建数据获取函数，为后续的因子评分和选股打下基础。

![](img/30e7d42730829e088e16b7c0a34deebe_7.png)

![](img/30e7d42730829e088e16b7c0a34deebe_9.png)

![](img/30e7d42730829e088e16b7c0a34deebe_11.png)

## 定义策略上下文与股票池

![](img/30e7d42730829e088e16b7c0a34deebe_13.png)

![](img/30e7d42730829e088e16b7c0a34deebe_15.png)

![](img/30e7d42730829e088e16b7c0a34deebe_17.png)

首先，我们需要在策略的 `context` 对象中定义一些关键变量。这些变量将贯穿整个策略的运行过程。

![](img/30e7d42730829e088e16b7c0a34deebe_19.png)

![](img/30e7d42730829e088e16b7c0a34deebe_21.png)

以下是需要在 `context` 中初始化的变量列表：

![](img/30e7d42730829e088e16b7c0a34deebe_23.png)

![](img/30e7d42730829e088e16b7c0a34deebe_25.png)

*   **股票池 (`context.hs300`)**：指定策略选股的范围。为了使策略表现更稳健，我们选择沪深300指数成分股作为初始股票池。
*   **股票排名 (`context.rank_stocks`)**：定义一个变量，用于存储后续根据因子评分得到的股票排名结果。这个列表将用于最终的调仓操作。

![](img/30e7d42730829e088e16b7c0a34deebe_27.png)

![](img/30e7d42730829e088e16b7c0a34deebe_29.png)

![](img/30e7d42730829e088e16b7c0a34deebe_31.png)

```python
# 在 initialize 函数中定义
def initialize(context):
    # 指定选股池为沪深300成分股
    context.hs300 = index_components('000300.XSHG')
    # 初始化一个列表，用于存放排名后的股票
    context.rank_stocks = []
```

![](img/30e7d42730829e088e16b7c0a34deebe_33.png)

![](img/30e7d42730829e088e16b7c0a34deebe_35.png)

## 设置定时调仓函数

![](img/30e7d42730829e088e16b7c0a34deebe_37.png)

![](img/30e7d42730829e088e16b7c0a34deebe_39.png)

上一节我们定义了策略的“原料”（股票池和存储变量），本节中我们来看看如何设置策略的“发动机”——定时调仓机制。

![](img/30e7d42730829e088e16b7c0a34deebe_41.png)

![](img/30e7d42730829e088e16b7c0a34deebe_43.png)

我们需要设置一个定时器，让策略在固定的时间点（例如每月初）执行选股和调仓逻辑。这里我们使用 `run_monthly` 函数。

![](img/30e7d42730829e088e16b7c0a34deebe_45.png)

![](img/30e7d42730829e088e16b7c0a34deebe_47.png)

```python
# 在 initialize 函数中继续设置定时器
def initialize(context):
    context.hs300 = index_components('000300.XSHG')
    context.rank_stocks = []

    # 设置每月第一个交易日执行 rebalance 函数
    run_monthly(rebalance, 1)
```

![](img/30e7d42730829e088e16b7c0a34deebe_49.png)

![](img/30e7d42730829e088e16b7c0a34deebe_51.png)

![](img/30e7d42730829e088e16b7c0a34deebe_53.png)

![](img/30e7d42730829e088e16b7c0a34deebe_55.png)

## 构建数据获取函数

![](img/30e7d42730829e088e16b7c0a34deebe_57.png)

现在，策略框架已经搭建好。接下来，我们将进入核心部分：编写一个函数来获取用于评分的财务数据。这个函数 `get_stocks` 将在每次调仓时被调用。

![](img/30e7d42730829e088e16b7c0a34deebe_59.png)

![](img/30e7d42730829e088e16b7c0a34deebe_61.png)

![](img/30e7d42730829e088e16b7c0a34deebe_63.png)

以下是 `get_stocks` 函数的主要步骤：

![](img/30e7d42730829e088e16b7c0a34deebe_65.png)

![](img/30e7d42730829e088e16b7c0a34deebe_67.png)

![](img/30e7d42730829e088e16b7c0a34deebe_69.png)

1.  **构建查询语句**：明确我们需要获取哪些财务指标。
2.  **执行数据查询**：从数据库获取指定股票池的这些指标数据。
3.  **数据整理**：将获取的数据转换为便于处理的格式（如DataFrame）。

![](img/30e7d42730829e088e16b7c0a34deebe_71.png)

我们将获取两类共六个财务指标：
*   **越高越好型指标**：例如每股收益、净资产收益率等。
*   **越低越好型指标**：例如资产负债率、市净率(PB)等。

![](img/30e7d42730829e088e16b7c0a34deebe_73.png)

![](img/30e7d42730829e088e16b7c0a34deebe_75.png)

![](img/30e7d42730829e088e16b7c0a34deebe_77.png)

```python
def get_stocks(context):
    # 1. 构建查询语句 (Query)
    # 查询“越高越好”的指标
    q_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益
        fundamentals.financial_indicator.roe,       # 净资产收益率
        fundamentals.financial_indicator.roa        # 总资产收益率
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 过滤条件：股票在沪深300池中
    )

    # 查询“越低越好”的指标
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio,       # 市净率(PB)
        fundamentals.eod_derivative_indicator.market_cap      # 总市值
    ).filter(
        fundamentals.stockcode.in_(context.hs300)
    )

    # 2. 执行查询并获取数据
    fund_up = get_fundamentals(q_up)  # 获取“越高越好”指标的数据
    fund_down = get_fundamentals(q_down) # 获取“越低越好”指标的数据

    # 3. 数据整理：转置DataFrame，使股票代码成为行索引，指标成为列
    fund_up = fund_up.T
    fund_down = fund_down.T

    # 返回处理好的两组数据
    return fund_up, fund_down
```

![](img/30e7d42730829e088e16b7c0a34deebe_79.png)

![](img/30e7d42730829e088e16b7c0a34deebe_81.png)

![](img/30e7d42730829e088e16b7c0a34deebe_83.png)

**代码说明**：
*   `query(...)`：用于构建数据查询语句。
*   `.filter(...)`：用于添加过滤条件，这里确保只获取股票池内的数据。
*   `get_fundamentals(...)`：执行查询，返回一个`DataFrame`。
*   `.T`：对`DataFrame`进行转置操作。默认返回的数据以指标为行、股票为列，转置后更符合我们的分析习惯（每行代表一只股票）。

![](img/30e7d42730829e088e16b7c0a34deebe_85.png)

## 总结

![](img/30e7d42730829e088e16b7c0a34deebe_87.png)

![](img/30e7d42730829e088e16b7c0a34deebe_89.png)

本节课中我们一起学习了量化策略初始化的关键步骤：
1.  我们在 `context` 中定义了策略的股票池 (`hs300`) 和用于存储排名的变量 (`rank_stocks`)。
2.  我们使用 `run_monthly` 函数设置了按月调仓的定时任务。
3.  我们构建了核心的 `get_stocks` 函数，它通过 `query` 和 `get_fundamentals` 接口，成功获取了沪深300成分股的六项关键财务数据，并将其分为“越高越好”和“越低越好”两类，为下一步的因子评分做好了数据准备。

![](img/30e7d42730829e088e16b7c0a34deebe_91.png)

![](img/30e7d42730829e088e16b7c0a34deebe_93.png)

![](img/30e7d42730829e088e16b7c0a34deebe_95.png)

至此，策略的数据基础已经搭建完毕。在下一节课中，我们将利用这些数据，对每只股票进行综合评分和排名。