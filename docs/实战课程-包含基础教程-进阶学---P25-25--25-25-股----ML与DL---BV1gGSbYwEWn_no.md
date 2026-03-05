# 量化交易教程：P25：股票池筛选 📊

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_1.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_3.png)

## 概述
在本节课中，我们将学习如何构建一个股票池筛选策略。我们将使用Python，基于财务指标（如营业收入）从沪深300指数成分股中筛选出表现最佳的股票。这个过程是量化交易中构建投资组合的关键步骤。

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_5.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_7.png)

## 策略初始化
上一节我们介绍了策略的基本框架，本节中我们来看看如何初始化一个股票池。

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_9.png)

在策略的构造函数中，我们需要定义我们的股票池。这里我们选择沪深300指数作为我们的初始股票池。你可以使用指数名称或代码来指定。

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_11.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_13.png)

```python
def initialize(context):
    # 设定股票池为沪深300指数
    g.stock_pool = '沪深300'
```

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_15.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_17.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_19.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_21.png)

## 数据预处理
初始化股票池后，我们需要在每天交易开始前进行数据预处理，即筛选股票。这通常在 `before_trading_start` 函数中完成。

以下是数据预处理的核心步骤：

1.  **查询财务指标**：我们使用平台提供的 `query` 函数来获取股票的财务数据。所有可用的指标都在官方帮助文档中列出。
2.  **应用过滤器**：使用 `filter` 条件来限定我们只查询股票池（沪深300）内的股票。
3.  **排序与限制**：根据选定的财务指标（如营业收入）对结果进行排序，并限制返回的股票数量（例如，只取前10名）。

```python
def before_trading_start(context):
    # 构建查询：获取营业收入指标
    q = query(
        fundamentals.eod_derivative_indicator.operating_revenue
    ).filter(
        fundamentals.eod_derivative_indicator.code.in_(get_index_stocks('沪深300'))
    ).order_by(
        fundamentals.eod_derivative_indicator.operating_revenue.desc() # 按营收降序排列
    ).limit(10) # 只取前10只股票

    # 执行查询并将结果存入context中，供后续交易函数使用
    context.stock_list = get_fundamentals(q)
    # 打印查看结果
    log.info(context.stock_list)
```

**代码解析**：
*   `fundamentals.eod_derivative_indicator.operating_revenue`：这是查询营业收入指标的路径。具体指标名称需参考API文档。
*   `get_index_stocks(‘沪深300’)`：这个函数用于获取指定指数（沪深300）当前的所有成分股代码列表。
*   `order_by(…desc())`：表示按营业收入从高到低（降序）排序。
*   `limit(10)`：限制返回结果数量为10条。
*   `get_fundamentals(q)`：执行构建好的查询，返回一个包含股票代码和营收数据的DataFrame。

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_23.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_25.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_27.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_29.png)

## 注意事项与调试
在编写和运行策略时，可能会遇到一些问题。以下是一个常见的调试过程：

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_31.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_33.png)

最初运行策略时，打印出的 `context.stock_list` 可能是空的。这通常是因为在 `filter` 条件中，我们错误地在股票列表中寻找指数代码。正确的做法是使用 `get_index_stocks(‘指数名称’)` 来获取该指数下的所有成分股列表。

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_35.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_37.png)

修正后的关键代码行如下：
```python
).filter(
    fundamentals.eod_derivative_indicator.code.in_(get_index_stocks('沪深300'))
```
确保使用正确的函数获取指数成分股，是筛选策略能正常工作的前提。

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_39.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_41.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_43.png)

## 总结
本节课中我们一起学习了量化交易中股票池筛选的基本方法。我们掌握了如何：
1.  在策略初始化时设定目标股票池（如沪深300）。
2.  在 `before_trading_start` 函数中，使用 `query`、`filter`、`order_by` 和 `limit` 方法，基于财务指标（营业收入）从股票池中筛选出排名靠前的股票。
3.  将筛选结果存储起来，为后续的交易逻辑提供输入。

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_45.png)

![](img/cd4ad8e90303652c2cdd6e64e9b5bb71_47.png)

这是一个简单的单因子筛选示例。在实际应用中，你可以结合多个因子（如市盈率、净利润增长率等）进行更复杂的综合筛选，以构建更稳健的投资组合。