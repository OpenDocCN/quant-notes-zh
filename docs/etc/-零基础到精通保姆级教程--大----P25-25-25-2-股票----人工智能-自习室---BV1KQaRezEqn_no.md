# Python金融量化交易：P25：股票池筛选

![](img/170ffb05767eeaa0ead0294d661d12e4_1.png)

## 概述
在本节课中，我们将学习如何构建一个股票池筛选策略。我们将从沪深300指数成分股中，根据财务指标（如营业收入）筛选出表现最佳的股票，为后续的交易决策做准备。整个过程将涉及数据查询、条件过滤、排序和限制结果数量。

![](img/170ffb05767eeaa0ead0294d661d12e4_3.png)

## 策略初始化与股票池设定
上一节我们介绍了策略的基本框架，本节中我们来看看如何设定初始的股票池。

![](img/170ffb05767eeaa0ead0294d661d12e4_5.png)

在策略的构造函数中，我们需要定义我们感兴趣的股票池。这里我们选择沪深300指数作为我们的初始股票池。你可以使用指数名称或代码来指定。

![](img/170ffb05767eeaa0ead0294d661d12e4_7.png)

![](img/170ffb05767eeaa0ead0294d661d12e4_9.png)

```python
def initialize(context):
    # 设定股票池为沪深300指数成分股
    set_universe('沪深300')
```

![](img/170ffb05767eeaa0ead0294d661d12e4_11.png)

![](img/170ffb05767eeaa0ead0294d661d12e4_13.png)

## 数据预处理与查询
设定好股票池后，我们需要在每日交易开始前进行数据预处理。核心是查询相关财务指标，并根据我们的规则进行筛选。

量化交易本质上是数据挖掘，涉及大量指标。我们将使用平台提供的`query`功能来获取数据。所有可用的指标都可以在官方帮助文档中找到。

以下是查询财务数据的基本步骤：
1.  构建一个查询（`query`），指定要获取的指标。
2.  使用`filter`来限定查询范围，例如只查询股票池内的股票。
3.  使用`order_by`对结果进行排序。
4.  使用`limit`限制返回的结果数量。

## 构建筛选逻辑
现在，我们来具体构建筛选逻辑。我们希望在每日开盘前，从沪深300成分股中筛选出营业收入最高的10只股票。

![](img/170ffb05767eeaa0ead0294d661d12e4_15.png)

以下是实现此逻辑的代码步骤：

![](img/170ffb05767eeaa0ead0294d661d12e4_17.png)

![](img/170ffb05767eeaa0ead0294d661d12e4_19.png)

![](img/170ffb05767eeaa0ead0294d661d12e4_21.png)

```python
def before_trading_start(context, data):
    # 构建查询：获取基础财务数据中的营业收入指标
    fundamental_df = get_fundamentals(
        query(
            fundamentals.financial_indicator.operating_revenue # 营业收入指标
        )
        .filter(
            fundamentals.financial_indicator.stockcode.in_(get_index_stocks('沪深300')) # 过滤：仅限沪深300成分股
        )
        .order_by(
            fundamentals.financial_indicator.operating_revenue.desc() # 排序：按营业收入降序排列
        )
        .limit(10) # 限制：只取前10名
    )
    # 将筛选结果存入context，供后续函数使用
    context.fundamental_df = fundamental_df
    # 打印结果以供检查
    print(fundamental_df)
```

![](img/170ffb05767eeaa0ead0294d661d12e4_23.png)

![](img/170ffb05767eeaa0ead0294d661d12e4_25.png)

**代码解析**：
*   `get_fundamentals()`: 用于获取基本面数据的函数。
*   `query(...)`: 指定查询营业收入(`operating_revenue`)。
*   `.filter(...)`: 确保只查询股票代码在沪深300成分股列表中的股票。这里使用了`get_index_stocks('沪深300')`来动态获取成分股列表，比硬编码更可靠。
*   `.order_by(...desc())`: 将结果按照营业收入从高到低（降序）排列。
*   `.limit(10)`: 只保留排名前10的结果。
*   最后，将结果`DataFrame`保存在`context.fundamental_df`中，并打印出来验证。

![](img/170ffb05767eeaa0ead0294d661d12e4_27.png)

**注意**：在初次运行时，如果直接使用`‘沪深300’`字符串进行过滤可能无法匹配，需要确保使用正确的API（如`get_index_stocks`）来获取指数成分股列表，这是之前示例中结果为空的原因。修正后即可正常输出。

![](img/170ffb05767eeaa0ead0294d661d12e4_29.png)

![](img/170ffb05767eeaa0ead0294d661d12e4_31.png)

## 总结
本节课中我们一起学习了股票池筛选的基本流程。
1.  我们首先在`initialize`函数中设定了初始股票池（沪深300）。
2.  接着，在`before_trading_start`函数中，我们利用`get_fundamentals`和`query`方法，结合`filter`（过滤）、`order_by`（排序）和`limit`（限制）操作，从股票池中筛选出了营业收入最高的10只股票。
3.  这个筛选结果被存储起来，为下一节课将要学习的交易逻辑提供了备选股票列表。

![](img/170ffb05767eeaa0ead0294d661d12e4_33.png)

![](img/170ffb05767eeaa0ead0294d661d12e4_35.png)

![](img/170ffb05767eeaa0ead0294d661d12e4_37.png)

通过本节，你掌握了如何使用基本面数据对股票进行初步筛选，这是构建量化策略非常关键的第一步。