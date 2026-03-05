# 量化交易实战：P25：股票池筛选 📊

![](img/30cac78790baabbb0077aec996160d03_1.png)

## 概述
在本节课中，我们将学习如何使用Python进行股票池的筛选。我们将基于财务指标（例如营业收入）从沪深300指数成分股中，筛选出表现最佳的股票。这个过程是构建量化交易策略的基础步骤。

![](img/30cac78790baabbb0077aec996160d03_3.png)

![](img/30cac78790baabbb0077aec996160d03_5.png)

## 策略初始化与股票池设定
上一节我们介绍了量化交易的基本框架，本节中我们来看看如何设定初始的股票池。

![](img/30cac78790baabbb0077aec996160d03_7.png)

在策略的构造函数中，我们需要定义我们感兴趣的股票范围。这里我们选择沪深300指数作为我们的初始股票池。

![](img/30cac78790baabbb0077aec996160d03_9.png)

```python
def initialize(context):
    # 设定股票池为沪深300指数成分股
    context.stock_pool = index_components('沪深300')
```

![](img/30cac78790baabbb0077aec996160d03_11.png)

![](img/30cac78790baabbb0077aec996160d03_13.png)

![](img/30cac78790baabbb0077aec996160d03_15.png)

![](img/30cac78790baabbb0077aec996160d03_17.png)

## 数据预处理与指标查询
在交易日开始前，我们通常需要进行数据预处理。本节将介绍如何查询财务数据并进行初步筛选。

![](img/30cac78790baabbb0077aec996160d03_19.png)

在 `before_trading_start` 函数中，我们将执行数据查询。量化交易的核心是数据挖掘，我们需要从海量数据中提取有价值的指标。所有可用的指标都可以在平台的帮助文档中找到。

以下是查询财务数据的基本步骤：

1.  **构建查询（Query）**：使用 `query` 函数，并指定要查询的财务指标字段。
2.  **设置过滤条件（Filter）**：通过 `filter` 方法，限定只查询属于我们股票池（沪深300）的股票。
3.  **排序（Order）**：根据选定的指标（如营业收入）进行排序，这里我们按降序排列以获取营收最高的股票。
4.  **限制数量（Limit）**：使用 `limit` 方法，只选取排名前10的股票。

```python
def before_trading_start(context):
    # 构建查询，获取营业收入指标
    q = query(
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        fundamentals.financial_indicator.code.in_(context.stock_pool)
    ).order_by(
        fundamentals.financial_indicator.operating_revenue.desc()
    ).limit(10)

    # 执行查询，并将结果存入context中供后续使用
    context.filtered_stocks = get_fundamentals(q)
    # 打印结果以供检查
    print(context.filtered_stocks)
```

![](img/30cac78790baabbb0077aec996160d03_21.png)

## 代码调试与问题排查
运行上述代码后，如果发现查询结果为空，需要进行问题排查。一个常见的原因是股票池的指定方式不正确。

![](img/30cac78790baabbb0077aec996160d03_23.png)

![](img/30cac78790baabbb0077aec996160d03_25.png)

![](img/30cac78790baabbb0077aec996160d03_27.png)

![](img/30cac78790baabbb0077aec996160d03_29.png)

最初的代码可能直接使用了指数名称 `‘沪深300’`，但 `index_components` 函数需要正确的指数代码。我们需要查阅平台的API文档来获取准确的指数代码。

![](img/30cac78790baabbb0077aec996160d03_31.png)

修正后的初始化代码如下：

![](img/30cac78790baabbb0077aec996160d03_33.png)

![](img/30cac78790baabbb0077aec996160d03_35.png)

```python
def initialize(context):
    # 使用正确的指数代码获取成分股
    context.stock_pool = index_components('000300.XSHG') # 沪深300的指数代码
```

![](img/30cac78790baabbb0077aec996160d03_37.png)

![](img/30cac78790baabbb0077aec996160d03_39.png)

![](img/30cac78790baabbb0077aec996160d03_41.png)

修正后重新运行回测，即可在日志中看到每天筛选出的前10只营业收入最高的股票列表。

![](img/30cac78790baabbb0077aec996160d03_43.png)

![](img/30cac78790baabbb0077aec996160d03_45.png)

## 总结
本节课中我们一起学习了量化交易中股票池筛选的基本流程。我们掌握了如何在策略初始化时设定股票池，如何在交易日开始前查询财务数据，并通过过滤、排序和限制数量来筛选出目标股票。这个过程是构建更复杂选股策略的基石。记住，充分利用平台的帮助文档来查找正确的函数和指标代码是成功的关键。