# Python金融分析与量化交易实战：P23：03-2-股票池筛选 📊

在本节课中，我们将学习如何构建一个股票池筛选策略。我们将使用财务指标（如营业收入）对沪深300指数成分股进行筛选和排序，最终选出排名靠前的股票，为后续的交易决策做准备。

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_1.png)

## 策略初始化与股票池设定

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_3.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何设定初始的股票池。

在策略的构造函数中，我们需要指定要操作的股票池。这里我们选择沪深300指数作为初始的股票池。你可以使用指数名称或代码来指定。

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_5.png)

```python
def initialize(context):
    # 设定股票池为沪深300指数
    context.stock_pool = '沪深300'
    # 关闭不必要的打印信息
    log.set_level('order', 'error')
```

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_7.png)

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_9.png)

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_11.png)

## 数据预处理与指标查询

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_13.png)

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_15.png)

在交易日开始前，我们需要对股票池进行预处理，即根据财务指标进行筛选。这本质上是一个数据挖掘过程，我们需要从海量数据中提取有用的信息。

所有可用的查询指标都在平台的帮助文档中。对于初学者，建议在使用任何函数前先阅读相关文档，了解其参数和返回值。

以下是查询财务数据的关键步骤：

1.  **导入查询函数**：使用平台提供的 `query` 函数。
2.  **构建查询语句**：在 `query` 对象中指定要查询的指标。
3.  **应用过滤器**：使用 `filter` 方法限定查询范围，例如只查询特定股票池中的股票。
4.  **排序与限制**：使用 `order_by` 对结果排序，并用 `limit` 限制返回的数量。

## 实现筛选逻辑

现在，我们在 `before_trading` 函数中实现具体的筛选逻辑。我们的目标是：在每个交易日开始前，从沪深300成分股中，找出上一期营业收入最高的10只股票。

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_17.png)

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_19.png)

```python
def before_trading(context):
    # 构建查询：获取股票的基础财务数据-营业总收入
    q = query(
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        # 过滤条件：股票代码在沪深300指数成分股中
        fundamentals.financial_indicator.code.in_(get_index_stocks('沪深300'))
    ).order_by(
        # 按营业总收入降序排列（数值大的排前面）
        fundamentals.financial_indicator.operating_revenue.desc()
    ).limit(
        # 只取前10名
        10
    )

    # 执行查询，结果是一个DataFrame
    context.filtered_stocks = get_fundamentals(q)
    # 打印查看结果（实际运行时可能注释掉以保持日志整洁）
    log.info(context.filtered_stocks)
```

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_21.png)

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_23.png)

**代码要点解析**：
*   `fundamentals.financial_indicator.operating_revenue`：这是查询营业总收入的指标字段，具体字段名需参考官方文档。
*   `get_index_stocks(‘沪深300’)`：这个函数用于获取指定指数（沪深300）当前的成分股列表。这是修正后的关键点，确保我们在正确的股票池中筛选。
*   `desc()`：表示降序排列。
*   `get_fundamentals(q)`：执行构建好的查询，返回财务数据。

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_25.png)

## 运行与验证

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_27.png)

编写完代码后，需要运行回测以验证筛选逻辑是否正确。

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_29.png)

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_31.png)

首次运行时，如果发现 `context.filtered_stocks` 为空，通常是因为股票池指定有误。例如，直接使用字符串 `’沪深300’` 而不是通过 `get_index_stocks` 函数获取实际的股票代码列表。修正后重新运行，即可在回测日志中看到每天筛选出的前10只股票代码及其营业收入数据。

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_33.png)

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_35.png)

---

![](img/a3d3f4a859ba2f784c9a11632fbbcbff_37.png)

本节课中我们一起学习了量化交易中股票池筛选的基本方法。我们掌握了如何利用平台的 `query` 和 `get_fundamentals` 功能，基于财务指标（如营业收入）从指定指数（如沪深300）中筛选并排序出目标股票。这个过程的核心是**数据查询、过滤与排序**。记住，所有可用的数据字段都应以官方文档为准。在后续课程中，我们将利用筛选出的股票池进行具体的交易操作。