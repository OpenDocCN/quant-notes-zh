# 量化交易实战：P25：股票池筛选

在本节课中，我们将学习如何使用Python进行股票池的筛选。我们将基于财务指标（如营业收入）从沪深300指数成分股中，筛选出表现最佳的股票，为后续的量化交易策略构建基础股票池。

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_1.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何利用财务数据动态筛选股票池。

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_2.png)

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_3.png)

## 代码初始化与股票池设定

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_5.png)

首先，在策略的初始化部分，我们需要设定基础的交易参数和股票池。初始日期和资金等参数后续可以调整，目前我们聚焦于代码逻辑。

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_7.png)

以下是初始化步骤：

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_9.png)

1.  在 `initialize` 函数中，定义我们的基础股票池。这里我们选择沪深300指数。
2.  使用 `get_index_stocks` 函数获取指数成分股列表。

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_11.png)

```python
def initialize(context):
    # 设定沪深300指数为初始股票池
    g.stock_pool = get_index_stocks('000300.XSHG')
    # 后续可以在此处调整基准日期、初始资金等参数
```

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_12.png)

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_13.png)

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_14.png)

## 数据预处理与财务指标查询

在每日交易开始前（`before_trading_start`），我们需要进行数据预处理，即根据财务指标对股票池进行筛选和排序。

量化交易的核心是数据挖掘，平台提供了丰富的财务数据指标供我们查询。所有可用指标均可在官方帮助文档中找到。

以下是查询与筛选的核心步骤：

1.  **构建查询（Query）**：使用 `query` 函数指定想要查询的财务指标，例如营业收入。
2.  **应用过滤器（Filter）**：使用 `filter` 条件限定查询范围，例如只查询属于我们基础股票池（沪深300）的股票。
3.  **排序（Order）**：使用 `order_by` 对查询结果进行排序，例如按营业收入降序排列。
4.  **限制数量（Limit）**：使用 `limit` 函数限制最终输出的股票数量，例如只选择排名前10的股票。

对应的代码逻辑如下：

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_16.png)

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_17.png)

```python
def before_trading_start(context):
    # 1. 构建查询，获取营业收入指标
    q = query(
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        # 2. 过滤：仅选择在沪深300股票池中的股票
        fundamentals.financial_indicator.stockcode.in_(g.stock_pool)
    ).order_by(
        # 3. 排序：按营业收入降序排列
        fundamentals.financial_indicator.operating_revenue.desc()
    ).limit(
        # 4. 限制：只取前10名
        10
    )
    
    # 执行查询，获取结果DataFrame
    fund = get_fundamentals(q)
    
    # 将筛选出的股票列表存入context，供后续交易函数使用
    context.stock_list = fund.columns.values
    
    # 打印查看结果（实际运行时可能注释掉）
    print(context.stock_list)
```

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_18.png)

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_20.png)

## 代码调试与注意事项

在编写和运行策略时，可能会遇到一些问题，需要仔细检查和调试。

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_22.png)

以下是常见的注意事项：

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_23.png)

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_24.png)

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_25.png)

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_26.png)

1.  **确保数据源正确**：获取指数成分股时，需使用正确的指数代码。例如，沪深300的代码是 `‘000300.XSHG’`。
2.  **理解数据格式**：`get_fundamentals` 返回的是 `DataFrame`，其列（`columns`）是股票代码，我们需要将其转换为列表供后续使用。
3.  **关注执行频率**：`before_trading_start` 函数在每个交易日都会执行，因此筛选出的股票池是每日更新的。

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_28.png)

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_29.png)

如果运行后发现结果为空，应检查：
*   股票池代码是否正确。
*   查询的指标字段名是否准确。
*   过滤条件逻辑是否正确（如使用 `in_` 进行成员判断）。

![](img/36f870e5e6ef6bd0ec2414b3ef2844c8_31.png)

本节课中我们一起学习了如何构建一个动态股票池筛选策略。我们掌握了使用 `query`、`filter`、`order_by` 和 `limit` 方法，基于财务数据从指定指数中筛选出符合条件的股票。这是构建量化选股策略非常基础和重要的一步，为后续的择时、仓位管理等操作打下了坚实的基础。