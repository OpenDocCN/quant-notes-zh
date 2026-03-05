# 量化交易实战：P25：2-股票池筛选 📊

![](img/417a347927d7f3cd253bbd1e17420eec_1.png)

在本节课中，我们将学习如何基于财务指标，从沪深300指数成分股中筛选出表现优异的股票，构建一个动态的股票池。这是量化策略中数据预处理和选股的关键一步。

![](img/417a347927d7f3cd253bbd1e17420eec_3.png)

上一节我们介绍了量化交易的基本框架，本节中我们来看看如何实现一个具体的股票筛选策略。

![](img/417a347927d7f3cd253bbd1e17420eec_5.png)

## 策略初始化与股票池设定

![](img/417a347927d7f3cd253bbd1e17420eec_7.png)

首先，在策略的构造函数中，我们需要设定初始的股票池。这里我们选择沪深300指数作为基础池。

![](img/417a347927d7f3cd253bbd1e17420eec_9.png)

![](img/417a347927d7f3cd253bbd1e17420eec_11.png)

![](img/417a347927d7f3cd253bbd1e17420eec_13.png)

```python
def initialize(context):
    # 设定股票池为沪深300指数成分股
    context.stock_pool = index_components('沪深300')
```

## 数据查询与预处理

在每日交易开始前，我们需要获取股票的财务数据并进行筛选。这通过 `before_trading` 函数实现。

以下是实现数据查询与筛选的步骤：

1.  **查询财务指标**：使用 `query` 函数，指定要查询的财务字段，例如营业总收入。
2.  **应用过滤条件**：使用 `filter` 方法，确保只查询股票池内的股票。
3.  **排序与限制**：根据查询到的指标（如营收）进行降序排序，并限制只取排名前10的股票。

![](img/417a347927d7f3cd253bbd1e17420eec_15.png)

![](img/417a347927d7f3cd253bbd1e17420eec_17.png)

```python
def before_trading(context):
    # 构建查询，获取营业总收入指标
    q = query(
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        fundamentals.financial_indicator.code.in_(context.stock_pool)
    ).order_by(
        fundamentals.financial_indicator.operating_revenue.desc()
    ).limit(10)

    # 执行查询，将结果存入context中供后续使用
    context.selected_stocks = get_fundamentals(q)
    # 打印结果以供检查
    log.info(context.selected_stocks)
```

![](img/417a347927d7f3cd253bbd1e17420eec_19.png)

![](img/417a347927d7f3cd253bbd1e17420eec_21.png)

![](img/417a347927d7f3cd253bbd1e17420eec_23.png)

## 代码调试与注意事项

![](img/417a347927d7f3cd253bbd1e17420eec_25.png)

初次运行代码时，可能无法打印出预期结果。这通常是因为股票池的设定有误。

![](img/417a347927d7f3cd253bbd1e17420eec_27.png)

*   **问题**：直接使用字符串 `‘沪深300’` 可能无法被系统识别为有效的指数代码。
*   **解决方案**：使用 `index_components` 函数来获取指数成分股列表，确保我们操作的是具体的股票代码，而非指数本身。

![](img/417a347927d7f3cd253bbd1e17420eec_29.png)

![](img/417a347927d7f3cd253bbd1e17420eec_31.png)

![](img/417a347927d7f3cd253bbd1e17420eec_33.png)

修正后的初始化代码确保了 `context.stock_pool` 是一个包含具体股票代码的列表，使得后续的 `filter` 条件能够正确匹配。

![](img/417a347927d7f3cd253bbd1e17420eec_35.png)

![](img/417a347927d7f3cd253bbd1e17420eec_37.png)

本节课中我们一起学习了如何利用财务数据接口，通过查询、过滤、排序和限制操作，从一个宽基指数（如沪深300）中动态筛选出符合特定条件（如营收最高）的股票，从而构建一个精简的、用于后续交易的股票池。这是量化选股策略的基础。