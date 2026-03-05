# Python金融量化交易：P25：2-股票池筛选 📊

## 概述
在本节课中，我们将学习如何构建一个股票池筛选策略。我们将从沪深300指数成分股中，根据财务指标（如营业收入）筛选出表现最佳的股票，为后续的交易决策做准备。整个过程将涉及数据查询、条件过滤、排序和限制结果数量。

![](img/853ff18f573ae45d9510ee10ce5adb29_1.png)

![](img/853ff18f573ae45d9510ee10ce5adb29_3.png)

![](img/853ff18f573ae45d9510ee10ce5adb29_5.png)

## 策略初始化
上一节我们介绍了策略的基本框架，本节中我们来看看如何初始化我们的股票池。

![](img/853ff18f573ae45d9510ee10ce5adb29_7.png)

在策略的构造函数中，我们需要定义我们的股票池。这里我们选择沪深300指数作为初始的股票池。你可以使用指数名称或代码来指定。

![](img/853ff18f573ae45d9510ee10ce5adb29_9.png)

```python
def initialize(context):
    # 设定股票池为沪深300指数成分股
    context.hs300 = index_stocks('000300.XSHG')
    # 后续可以调整起始资金、频率等参数，此处使用默认设置
```

## 数据预处理与筛选
初始化股票池后，我们需要在每日交易开始前进行数据预处理和筛选。以下是实现步骤。

![](img/853ff18f573ae45d9510ee10ce5adb29_11.png)

![](img/853ff18f573ae45d9510ee10ce5adb29_13.png)

首先，在 `before_trading` 函数中，我们需要查询相关财务数据。量化交易的核心是数据挖掘，平台提供了丰富的财务指标供我们使用。所有可用的指标都可以在官方帮助文档中找到。

![](img/853ff18f573ae45d9510ee10ce5adb29_15.png)

为了查询财务数据，我们使用 `get_fundamentals` 函数。该函数需要一个查询语句（query），在查询语句中我们可以指定要获取的指标，并使用 `filter` 来添加筛选条件。

以下是我们构建查询和筛选逻辑的步骤：

1.  **构建查询（Query）**：我们首先确定要查询的财务指标。例如，我们关注公司的营业收入。在帮助文档的“基础财务数据”部分可以找到对应的字段（如 `income` 或 `operating_revenue`，具体名称需查阅文档）。
2.  **添加筛选条件（Filter）**：查询需要限定在特定的股票范围内。我们使用 `filter` 来确保只查询属于我们股票池（即沪深300成分股）的股票。
3.  **结果排序（Order By）**：查询到的结果可能很多，我们需要对其进行排序。例如，按照营业收入从高到低（降序）排列，以找到营收表现最好的公司。
4.  **限制结果数量（Limit）**：我们通常不需要所有结果，只需排名前N的股票。使用 `limit` 来限制返回的股票数量，例如前10只。

将以上逻辑整合到代码中，如下所示：

```python
def before_trading(context):
    # 构建财务数据查询
    query = query(
        fundamentals.eod_derivative_indicator.operating_revenue # 示例字段，请根据实际文档调整
    ).filter(
        fundamentals.eod_derivative_indicator.code.in_(context.hs300) # 筛选股票池内的股票
    ).order_by(
        fundamentals.eod_derivative_indicator.operating_revenue.desc() # 按营收降序排列
    ).limit(10) # 只取前10名

    # 执行查询，结果是一个DataFrame
    context.fundamental_df = get_fundamentals(query)

    # 打印结果以供检查
    print(context.fundamental_df)
```

![](img/853ff18f573ae45d9510ee10ce5adb29_17.png)

## 调试与验证
代码编写完成后，运行回测以验证筛选逻辑是否正确工作。

![](img/853ff18f573ae45d9510ee10ce5adb29_19.png)

![](img/853ff18f573ae45d9510ee10ce5adb29_21.png)

首次运行时，打印出的 `DataFrame` 可能是空的。这通常是因为在 `filter` 条件中，我们错误地使用了股票列表，而没有正确引用指数成分股函数。

![](img/853ff18f573ae45d9510ee10ce5adb29_23.png)

![](img/853ff18f573ae45d9510ee10ce5adb29_25.png)

![](img/853ff18f573ae45d9510ee10ce5adb29_27.png)

问题在于 `context.hs300` 需要被正确赋值为指数成分股的代码列表。修正方法是使用平台提供的 `get_index_stocks` 函数（或类似函数，具体名称需参考平台API）来获取指数成分股。

修正后的初始化代码如下：

```python
def initialize(context):
    # 正确获取沪深300指数成分股代码列表
    context.hs300 = get_index_stocks('000300.XSHG') # 函数名可能为 get_index_stocks 或 index_stocks，请以平台文档为准
```

![](img/853ff18f573ae45d9510ee10ce5adb29_29.png)

![](img/853ff18f573ae45d9510ee10ce5adb29_31.png)

修正后再次运行回测，观察日志输出。现在，`before_trading` 函数应该能在每个交易日打印出一个包含股票代码和其对应营业收入的 `DataFrame`。数据格式通常为行是股票代码，列是财务指标。

![](img/853ff18f573ae45d9510ee10ce5adb29_33.png)

![](img/853ff18f573ae45d9510ee10ce5adb29_35.png)

## 总结
本节课中我们一起学习了股票池筛选的基本流程。我们首先在策略初始化时定义了股票池（沪深300指数）。然后，在每日交易前，通过查询财务数据、过滤股票池成分、按指标排序并限制数量，动态筛选出目标股票（如营收前十的股票）。这个过程是量化策略中数据预处理和阿尔法因子选股的典型应用。下一节，我们将基于筛选出的股票池，进一步构建交易逻辑。