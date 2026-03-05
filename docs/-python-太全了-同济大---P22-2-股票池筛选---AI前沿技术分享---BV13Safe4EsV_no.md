# 量化投资入门：P22：股票池筛选 📊

## 概述
在本节课中，我们将学习如何构建一个量化交易策略的核心环节——股票池筛选。我们将基于财务指标，从沪深300指数成分股中筛选出表现优异的股票，为后续的交易决策做准备。

![](img/5c3e60faee3a17d923cac806fdc714d5_1.png)

## 策略初始化与股票池设定

上一节我们介绍了策略的基本框架，本节中我们来看看如何设定初始的股票池。

在策略的构造函数中，我们需要定义我们的初始股票池。这里我们选择沪深300指数作为我们的基础股票池。你可以使用指数名称或代码来指定。

![](img/5c3e60faee3a17d923cac806fdc714d5_3.png)

```python
# 在策略的 __init__ 函数中设定初始股票池
self.security = ‘沪深300‘
```

![](img/5c3e60faee3a17d923cac806fdc714d5_5.png)

打印信息的代码可以暂时移除，因为我们后续会通过其他方式查看筛选结果。

## 数据预处理与指标查询

![](img/5c3e60faee3a17d923cac806fdc714d5_7.png)

![](img/5c3e60faee3a17d923cac806fdc714d5_9.png)

设定好股票池后，我们需要在交易日开始前进行数据预处理。核心任务是获取股票池中每只股票的财务数据，并根据特定指标进行筛选。

![](img/5c3e60faee3a17d923cac806fdc714d5_11.png)

![](img/5c3e60faee3a17d923cac806fdc714d5_13.png)

以下是实现数据预处理的关键步骤：

![](img/5c3e60faee3a17d923cac806fdc714d5_15.png)

首先，我们需要查询财务数据。量化交易本质上是数据挖掘，涉及大量指标。所有可用的查询函数和指标都在平台的帮助文档中。对于初学者，务必学会查阅官方文档。

例如，要查询财务数据，可以使用 `get_fundamentals` 函数。该函数需要一个 `query` 参数来指定查询的指标，以及 `filter` 参数来设置过滤条件。

```python
# 在 before_trading 函数中进行数据预处理
def before_trading(context):
    # 构建查询语句
    q = query(
        # 指定要查询的财务指标字段，例如营业总收入
        fundamentals.financial_indicator.operating_revenue
    )
```

## 构建完整的筛选逻辑

仅仅查询数据是不够的，我们需要构建完整的筛选、排序和限制逻辑。

以下是构建筛选逻辑的具体步骤：

1.  **过滤股票池**：使用 `filter` 条件，确保我们只查询属于初始股票池（沪深300）的股票。
2.  **排序**：使用 `order_by` 对查询结果进行排序。例如，按照营业总收入降序排列，以找到营收最高的公司。
3.  **限制数量**：使用 `limit` 限制最终输出的股票数量，例如只选择排名前10的股票。

将以上步骤组合起来，完整的查询代码如下：

![](img/5c3e60faee3a17d923cac806fdc714d5_17.png)

![](img/5c3e60faee3a17d923cac806fdc714d5_19.png)

```python
def before_trading(context):
    # 构建完整的查询、筛选、排序语句
    df = get_fundamentals(
        query(
            fundamentals.financial_indicator.operating_revenue # 查询指标：营业总收入
        ).filter(
            fundamentals.stockcode.in_(get_index_stocks(‘000300.XSHG‘)) # 过滤：仅限沪深300成分股
        ).order_by(
            fundamentals.financial_indicator.operating_revenue.desc() # 排序：按营收降序
        ).limit(10) # 限制：只取前10名
    )
    # 将结果存入context，供后续交易函数使用
    context.fundamental_df = df
    # 打印查看结果
    print(df)
```

![](img/5c3e60faee3a17d923cac806fdc714d5_21.png)

![](img/5c3e60faee3a17d923cac806fdc714d5_23.png)

## 调试与验证

![](img/5c3e60faee3a17d923cac806fdc714d5_25.png)

编写完代码后，运行回测以验证筛选逻辑是否正确。

首次运行时，打印结果可能为空。这通常是因为股票池指定有误。我们需要确保获取的是指数（如沪深300）的成分股列表，而不是指数本身。可以使用 `get_index_stocks(‘指数代码‘)` 函数来获取成分股。

![](img/5c3e60faee3a17d923cac806fdc714d5_27.png)

修正后的关键代码如下：

![](img/5c3e60faee3a17d923cac806fdc714d5_29.png)

![](img/5c3e60faee3a17d923cac806fdc714d5_31.png)

```python
.filter(
    fundamentals.stockcode.in_(get_index_stocks(‘000300.XSHG‘)) # 正确获取沪深300成分股列表
)
```

![](img/5c3e60faee3a17d923cac806fdc714d5_33.png)

修正后再次运行回测，在日志中就能看到每天筛选出的前10只股票及其营业总收入数据。数据以DataFrame格式呈现，横向是股票名称，纵向是具体的财务数据。

![](img/5c3e60faee3a17d923cac806fdc714d5_35.png)

## 总结
本节课中我们一起学习了量化策略中股票池筛选的完整流程。我们掌握了如何：
1.  在策略初始化时设定基础股票池。
2.  在交易日开始前，使用 `get_fundamentals` 函数查询财务数据。
3.  结合 `query`、`filter`、`order_by` 和 `limit` 构建完整的筛选、排序逻辑。
4.  将筛选结果存储并传递，为下一步的交易信号生成做好准备。
5.  通过打印日志和修正代码来调试策略。

![](img/5c3e60faee3a17d923cac806fdc714d5_37.png)

这个筛选出的股票池，将成为我们后续进行买卖决策的基础。