# 量化交易实战课程：P20：股票池筛选

![](img/07c53fab8f6302f376159d4fbd1b4bf6_1.png)

在本节课中，我们将学习如何构建一个股票池筛选策略。我们将使用财务指标（如营业收入）作为筛选条件，从沪深300指数成分股中，每日动态筛选出排名靠前的股票，为后续的交易决策做准备。

上一节我们介绍了策略的基本框架，本节中我们来看看如何实现一个具体的股票筛选逻辑。

## 策略初始化与股票池设定

![](img/07c53fab8f6302f376159d4fbd1b4bf6_3.png)

首先，在策略的构造函数中，我们需要设定初始的股票池。这里我们选择沪深300指数作为我们的初始股票池。

![](img/07c53fab8f6302f376159d4fbd1b4bf6_5.png)

![](img/07c53fab8f6302f376159d4fbd1b4bf6_7.png)

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    context.stock_pool = index_components('000300.XSHG')
    # 打印信息功能可以关闭
    context.print_info = False
```

## 数据预处理与股票筛选

![](img/07c53fab8f6302f376159d4fbd1b4bf6_9.png)

在每日交易开始前，我们需要执行预处理函数，以获取最新的财务数据并筛选出目标股票。

以下是实现股票筛选的核心步骤：

1.  **构建查询**：使用平台提供的 `query` 功能，指定我们想要查询的财务指标。
2.  **应用过滤器**：通过 `filter` 条件，将查询范围限定在我们的初始股票池（沪深300）内。
3.  **排序**：根据选定的财务指标（如营业收入）对结果进行排序。
4.  **限制数量**：使用 `limit` 函数，只保留排名前N的股票作为最终筛选结果。

```python
def before_trading_start(context):
    # 构建查询，获取基础财务数据中的营业收入指标
    q = query(
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        fundamentals.financial_indicator.code.in_(context.stock_pool)
    ).order_by(
        fundamentals.financial_indicator.operating_revenue.desc() # 按营收降序排列
    ).limit(10) # 只取前10名

    # 执行查询，将结果（DataFrame格式）存入context中供后续使用
    context.filtered_stocks = get_fundamentals(q)

    # 打印查看筛选结果（调试用）
    print(context.filtered_stocks)
```

![](img/07c53fab8f6302f376159d4fbd1b4bf6_11.png)

## 代码调试与问题排查

![](img/07c53fab8f6302f376159d4fbd1b4bf6_13.png)

![](img/07c53fab8f6302f376159d4fbd1b4bf6_15.png)

在初次运行策略时，打印出的筛选结果可能为空。这通常是由于查询条件设置不正确导致的。

![](img/07c53fab8f6302f376159d4fbd1b4bf6_17.png)

![](img/07c53fab8f6302f376159d4fbd1b4bf6_19.png)

![](img/07c53fab8f6302f376159d4fbd1b4bf6_21.png)

常见问题及解决方法如下：

![](img/07c53fab8f6302f376159d4fbd1b4bf6_23.png)

*   **问题**：查询结果为空。
*   **原因**：在 `filter` 中，用于对比的股票代码列表 `context.stock_pool` 可能未正确获取或格式不匹配。
*   **解决**：确保 `index_components` 函数传入正确的指数代码。沪深300指数的正确代码通常是 `‘000300.XSHG’`。务必参考平台的API文档确认指数代码的格式。

修正初始化代码后，重新运行回测，即可在日志中看到每日筛选出的股票列表。该列表是一个DataFrame，行索引是日期，列是股票代码，单元格内是对应的财务指标数值。

![](img/07c53fab8f6302f376159d4fbd1b4bf6_25.png)

![](img/07c53fab8f6302f376159d4fbd1b4bf6_27.png)

## 总结

![](img/07c53fab8f6302f376159d4fbd1b4bf6_29.png)

![](img/07c53fab8f6302f376159d4fbd1b4bf6_31.png)

本节课中我们一起学习了股票池筛选的基本流程。我们掌握了如何在策略初始化时设定基础股票池，以及如何在每日开盘前，利用 `query`、`filter`、`order_by` 和 `limit` 等方法，基于财务指标动态筛选出符合条件的股票。这个筛选出的股票列表 `context.filtered_stocks` 为下一节课实现具体的交易逻辑打下了数据基础。