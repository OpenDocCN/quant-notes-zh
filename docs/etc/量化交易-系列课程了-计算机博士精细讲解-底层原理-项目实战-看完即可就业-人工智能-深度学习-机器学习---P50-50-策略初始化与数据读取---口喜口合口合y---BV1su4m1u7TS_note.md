# Python金融分析+量化交易：P50：策略初始化与数据读取 📊

在本节课中，我们将学习如何为一个量化交易策略进行初始化，并读取所需的金融数据。我们将从定义股票池开始，逐步实现一个按月调仓的因子打分选股策略框架。

![](img/321dee099b493410f28aea3a127aef25_1.png)

## 概述 📋

![](img/321dee099b493410f28aea3a127aef25_3.png)

我们将创建一个新的策略文件，并按照步骤实现一个因子打分选股策略。核心步骤包括：指定选股池、设置调仓逻辑、定义数据查询函数以及获取用于打分的财务指标数据。

![](img/321dee099b493410f28aea3a127aef25_5.png)

![](img/321dee099b493410f28aea3a127aef25_7.png)

## 策略初始化

![](img/321dee099b493410f28aea3a127aef25_9.png)

首先，我们需要在策略的 `context` 对象中进行初始化设置。

![](img/321dee099b493410f28aea3a127aef25_11.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何具体配置。

![](img/321dee099b493410f28aea3a127aef25_13.png)

![](img/321dee099b493410f28aea3a127aef25_15.png)

### 指定选股池

![](img/321dee099b493410f28aea3a127aef25_17.png)

第一步是确定我们的选股范围。为了获得更好的演示效果，我们将直接在沪深300指数的成分股中进行选择。

![](img/321dee099b493410f28aea3a127aef25_19.png)

以下是具体的代码实现：

![](img/321dee099b493410f28aea3a127aef25_21.png)

```python
# 在context中指定选股池为沪深300指数成分股
context.hs300 = ‘沪深300’
```

![](img/321dee099b493410f28aea3a127aef25_23.png)

![](img/321dee099b493410f28aea3a127aef25_25.png)

这样，我们就定义好了策略运行的基础股票池。

![](img/321dee099b493410f28aea3a127aef25_27.png)

![](img/321dee099b493410f28aea3a127aef25_29.png)

### 设置调仓变量与定时器

![](img/321dee099b493410f28aea3a127aef25_31.png)

接下来，我们需要设置用于存储股票排名的变量，并定义一个定时调仓函数。

![](img/321dee099b493410f28aea3a127aef25_33.png)

以下是具体的设置步骤：

![](img/321dee099b493410f28aea3a127aef25_35.png)

![](img/321dee099b493410f28aea3a127aef25_37.png)

1.  **定义排名变量**：在 `context` 中预先定义一个变量，用于存储后续计算出的股票排名。
    ```python
    context.rank = None  # 用于存储股票排名情况
    ```

![](img/321dee099b493410f28aea3a127aef25_39.png)

2.  **设置按月调仓定时器**：使用 `run_monthly` 函数设置策略按月执行调仓操作。
    ```python
    # 设置按月调仓，每月执行一次 rebalance 函数
    run_monthly(rebalance, 1)
    ```

![](img/321dee099b493410f28aea3a127aef25_41.png)

## 数据读取与处理

![](img/321dee099b493410f28aea3a127aef25_43.png)

定义了调仓框架后，下一步是实现核心的 `rebalance` 函数以及数据获取逻辑。

![](img/321dee099b493410f28aea3a127aef25_45.png)

上一节我们完成了策略的初始化设置，本节中我们来看看如何获取和处理财务数据。

![](img/321dee099b493410f28aea3a127aef25_47.png)

![](img/321dee099b493410f28aea3a127aef25_49.png)

### 定义调仓函数

![](img/321dee099b493410f28aea3a127aef25_51.png)

`rebalance` 函数是策略调仓的核心。在这个函数中，我们需要调用一个自定义函数来获取经过评分排序后的股票列表。

![](img/321dee099b493410f28aea3a127aef25_53.png)

以下是 `rebalance` 函数的基本结构：

![](img/321dee099b493410f28aea3a127aef25_55.png)

![](img/321dee099b493410f28aea3a127aef25_57.png)

```python
def rebalance(context):
    # 调用函数获取评分后的股票列表
    stock_list = get_stocks(context)
    # ... 后续的调仓操作（如下单等）将在这里实现
```

![](img/321dee099b493410f28aea3a127aef25_59.png)

### 实现数据获取函数

![](img/321dee099b493410f28aea3a127aef25_61.png)

![](img/321dee099b493410f28aea3a127aef25_63.png)

现在，我们来创建 `get_stocks` 函数。这个函数负责查询财务数据，为后续的因子打分做准备。

![](img/321dee099b493410f28aea3a127aef25_65.png)

以下是该函数需要完成的任务列表：

1.  **构建查询语句**：使用 `query` 对象指定需要查询的财务指标。
2.  **应用股票池过滤**：确保只查询属于沪深300成分股的股票数据。
3.  **获取并处理数据**：执行查询，并对返回的数据进行转置，以便于后续分析。

![](img/321dee099b493410f28aea3a127aef25_67.png)

### 查询财务指标

![](img/321dee099b493410f28aea3a127aef25_69.png)

![](img/321dee099b493410f28aea3a127aef25_71.png)

我们以六个常见的财务指标为例进行说明。这些指标可分为两类：“越高越好”型和“越低越好”型。

![](img/321dee099b493410f28aea3a127aef25_73.png)

![](img/321dee099b493410f28aea3a127aef25_75.png)

以下是具体的指标查询代码：

```python
def get_stocks(context):
    # 1. 查询“越高越好”型指标
    q_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (越高越好)
        fundamentals.financial_indicator.roe,       # 净资产收益率 (越高越好)
        fundamentals.financial_indicator.roa        # 总资产收益率 (越高越好)
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 过滤股票池
    )
    df_up = get_fundamentals(q_up).T # 执行查询并转置DataFrame

    # 2. 查询“越低越好”型指标
    q_down = query(
        fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率 (越低越好)
        fundamentals.eod_derivative_indicator.pb_ratio,       # 市净率PB (越低越好)
        fundamentals.eod_derivative_indicator.market_cap      # 总市值 (越低越好)
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 过滤股票池
    )
    df_down = get_fundamentals(q_down).T # 执行查询并转置DataFrame

    # 此时，df_up 和 df_down 就是我们需要用于打分的因子数据
    # df_up.shape 和 df_down.shape 预期为 (股票数量, 3)
    # ... 后续的因子打分和排序逻辑将在这里添加
    return ranked_stocks
```

**关键点说明**：`get_fundamentals()` 函数返回的 `DataFrame` 需要经过转置（`.T`）操作，将股票代码从列索引转换为行索引，这样每一行就代表一只股票，每一列代表一个财务指标，便于后续计算。

![](img/321dee099b493410f28aea3a127aef25_77.png)

## 总结 🎯

![](img/321dee099b493410f28aea3a127aef25_79.png)

本节课中我们一起学习了量化交易策略初始化的关键步骤：
1.  **策略初始化**：在 `context` 中指定了沪深300作为选股池，并设置了按月调仓的定时器。
2.  **数据读取**：实现了 `get_stocks` 函数，通过 `query` 和 `get_fundamentals` 接口，成功获取了“每股收益”、“净资产收益率”等六项关键财务指标数据，并按要求进行了数据转置处理。

![](img/321dee099b493410f28aea3a127aef25_81.png)

![](img/321dee099b493410f28aea3a127aef25_83.png)

目前，我们已经准备好了用于因子打分的原始数据。在接下来的课程中，我们将学习如何对这些因子进行标准化处理和加权评分，最终完成选股逻辑。