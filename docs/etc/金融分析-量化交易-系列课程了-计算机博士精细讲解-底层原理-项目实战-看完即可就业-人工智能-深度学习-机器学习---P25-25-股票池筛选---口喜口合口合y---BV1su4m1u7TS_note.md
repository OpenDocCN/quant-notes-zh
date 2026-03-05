# Python金融分析+量化交易：P25：股票池筛选 📊

## 概述
在本节课中，我们将学习如何在量化交易策略中构建并筛选股票池。我们将使用一个具体的例子，从沪深300指数成分股中，根据财务指标（如营业收入）筛选出排名靠前的股票，为后续的交易决策做准备。

## 策略初始化与股票池定义
上一节我们介绍了策略的基本框架，本节中我们来看看如何定义初始的股票池。

在策略的构造函数中，我们需要指定要交易的股票范围。这里我们选择沪深300指数作为我们的初始股票池。你可以使用指数名称或代码来定义。

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    set_universe(['沪深300'])
```

![](img/3332f446f90315d300bbddab2000eb90_1.png)

打印信息在此处不需要，因此可以移除。定义好股票池后，接下来我们需要在每日交易开始前对股票进行预处理和筛选。

## 数据查询与预处理
在每日交易开始前（`before_trading`函数中），我们需要获取股票的财务数据并进行筛选。量化交易的核心之一是数据挖掘，平台提供了丰富的财务和指标数据供我们查询。

![](img/3332f446f90315d300bbddab2000eb90_3.png)

以下是查询数据的关键步骤，我们需要借助平台的帮助文档来了解可用字段和函数用法。

![](img/3332f446f90315d300bbddab2000eb90_1.png)

所有可用的指标和数据字段都在官方文档中有详细说明，查询函数的使用方法也需参考文档。

![](img/3332f446f90315d300bbddab2000eb90_5.png)

![](img/3332f446f90315d300bbddab2000eb90_3.png)

例如，要查询财务数据，可以使用 `query` 函数。该函数主要接受两个参数：`query` 用于指定要查询的指标，`filter` 用于过滤条件。

![](img/3332f446f90315d300bbddab2000eb90_5.png)

根据我们的需求，我们将查询基础财务数据中的营业收入指标，并从我们定义的股票池中筛选股票。

![](img/3332f446f90315d300bbddab2000eb90_7.png)

![](img/3332f446f90315d300bbddab2000eb90_7.png)

首先，我们从文档中找到营业收入对应的字段。在基础财务数据中，我们可以找到“营业总收入”这个指标。

![](img/3332f446f90315d300bbddab2000eb90_9.png)

![](img/3332f446f90315d300bbddab2000eb90_9.png)

文档中会列出所有可用的数据字段，方便我们选择和引用。

![](img/3332f446f90315d300bbddab2000eb90_11.png)

![](img/3332f446f90315d300bbddab2000eb90_11.png)

![](img/3332f446f90315d300bbddab2000eb90_13.png)

![](img/3332f446f90315d300bbddab2000eb90_13.png)

![](img/3332f446f90315d300bbddab2000eb90_15.png)

![](img/3332f446f90315d300bbddab2000eb90_15.png)

![](img/3332f446f90315d300bbddab2000eb90_17.png)

![](img/3332f446f90315d300bbddab2000eb90_17.png)

## 构建筛选逻辑
现在，我们开始编写具体的筛选代码。逻辑是：查询沪深300成分股的营业收入，然后按营收降序排列，最后选取排名前十的股票。

以下是完整的筛选流程：

1.  **执行查询**：使用 `query` 函数指定查询“营业总收入”指标。
2.  **应用过滤**：使用 `filter` 确保只查询属于沪深300指数成分股的股票。
3.  **结果排序**：使用 `order_by` 将结果按照营业收入降序排列。
4.  **限制数量**：使用 `limit` 只选取排名前10的股票。

将查询结果存储到一个 `DataFrame` 中，并放入 `context` 上下文对象中，以便在策略的其他部分使用。

```python
def before_trading(context):
    # 构建查询：获取营业总收入
    q = query(
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        # 过滤条件：股票代码在沪深300成分股中
        fundamentals.financial_indicator.stockcode.in_(get_index_stocks('沪深300'))
    ).order_by(
        # 按营业总收入降序排列
        fundamentals.financial_indicator.operating_revenue.desc()
    ).limit(
        # 只取前10名
        10
    )
    
    # 执行查询，将结果存入context
    context.filtered_stocks = get_fundamentals(q)
    
    # 打印结果以供检查
    print(context.filtered_stocks)
```

其他暂时不用的函数（如 `handle_data`）可以先使用 `pass` 语句跳过。

![](img/3332f446f90315d300bbddab2000eb90_19.png)

![](img/3332f446f90315d300bbddab2000eb90_21.png)

## 运行调试与问题排查
编写完代码后，我们运行回测以检查筛选结果。

![](img/3332f446f90315d300bbddab2000eb90_23.png)

`before_trading` 函数会在每个交易日执行，因此会每天打印结果。

![](img/3332f446f90315d300bbddab2000eb90_25.png)

检查日志发现，打印的结果为空。这表示没有查询到符合条件的股票。

![](img/3332f446f90315d300bbddab2000eb90_27.png)

我们需要排查问题。回顾代码，查询指标和过滤逻辑似乎正确。问题可能出在股票池的定义上。我们最初在 `set_universe` 中直接写了 `‘沪深300’`，但这可能不正确。我们需要使用专门的函数来获取指数成分股。

在平台的API文档中，可以找到获取指数成分股的函数。

![](img/3332f446f90315d300bbddab2000eb90_19.png)

![](img/3332f446f90315d300bbddab2000eb90_29.png)

![](img/3332f446f90315d300bbddab2000eb90_21.png)

![](img/3332f446f90315d300bbddab2000eb90_31.png)

![](img/3332f446f90315d300bbddab2000eb90_23.png)

正确的做法是使用 `get_index_stocks(‘沪深300’)` 来获取股票代码列表。

![](img/3332f446f90315d300bbddab2000eb90_25.png)

![](img/3332f446f90315d300bbddab2000eb90_33.png)

![](img/3332f446f90315d300bbddab2000eb90_35.png)

![](img/3332f446f90315d300bbddab2000eb90_27.png)

修正构造函数中的股票池定义：

```python
def initialize(context):
    # 正确获取沪深300指数成分股列表作为股票池
    set_universe(get_index_stocks('沪深300'))
```

修正过滤条件中的股票池引用：

```python
).filter(
    fundamentals.financial_indicator.stockcode.in_(get_index_stocks('沪深300'))
)
```

![](img/3332f446f90315d300bbddab2000eb90_29.png)

## 验证结果
修正代码后，重新运行回测。

![](img/3332f446f90315d300bbddab2000eb90_37.png)

![](img/3332f446f90315d300bbddab2000eb90_31.png)

再次查看输出日志。

![](img/3332f446f90315d300bbddab2000eb90_33.png)

![](img/3332f446f90315d300bbddab2000eb90_39.png)

![](img/3332f446f90315d300bbddab2000eb90_35.png)

现在日志中显示了每天的筛选结果。结果是一个 `DataFrame`，横轴是股票名称，纵轴是日期，表格中的值是当天的营业收入。

![](img/3332f446f90315d300bbddab2000eb90_37.png)

![](img/3332f446f90315d300bbddab2000eb90_41.png)

![](img/3332f446f90315d300bbddab2000eb90_39.png)

这表明我们的股票池筛选功能已经成功实现，每天都能动态筛选出营业收入最高的前十只沪深300成分股。

## 总结
本节课中我们一起学习了量化交易中股票池筛选的完整流程。

我们首先在策略初始化时定义了基础的股票池（沪深300指数成分股）。接着，在每日交易前，我们利用平台的 `query` 和 `get_fundamentals` 功能，查询特定财务指标（营业收入），并通过 `filter`、`order_by` 和 `limit` 方法，实现了从股票池中筛选出符合条件（营收排名前十）的股票。过程中我们还遇到了股票池定义不准确的问题，并通过查阅文档和使用正确的API函数（`get_index_stocks`）解决了它。

![](img/3332f446f90315d300bbddab2000eb90_41.png)

这个筛选出的股票列表（`context.filtered_stocks`）为后续制定具体的买卖交易策略提供了基础。