# 人工智能金融量化交易：P37：02-2-股票池筛选

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_1.png)

## 概述
在本节课中，我们将学习如何在量化交易策略中，根据财务指标对股票池进行筛选和排序。我们将使用一个具体的例子：从沪深300指数成分股中，筛选出营业收入最高的10只股票，作为我们每日交易的候选股票池。

上一节我们介绍了量化交易的基本框架和策略初始化，本节中我们来看看如何实现一个具体的股票筛选逻辑。

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_3.png)

## 策略初始化与股票池设定
首先，我们需要在策略的构造函数中设定初始的股票池。这里我们选择沪深300指数作为我们的基础股票池。

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_5.png)

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    context.stock_pool = index_components('沪深300')
```

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_7.png)

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_9.png)

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_11.png)

日期、起始资金、交易频率等参数在后续都可以调整，为了简化初始步骤，我们暂时使用默认设置。

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_13.png)

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_15.png)

## 数据查询与预处理
在每日交易开始前，我们需要获取股票的财务数据并进行筛选。这个步骤在 `before_trading` 函数中完成。

以下是实现股票筛选的核心步骤：

1.  **查询财务指标**：我们需要查询所有候选股票的营业收入数据。
2.  **应用过滤条件**：确保我们只处理属于沪深300指数成分股的股票。
3.  **排序与限制**：将股票按营业收入降序排列，并只选取排名前10的股票。

## 代码实现详解
现在，我们来看具体的代码实现。量化交易平台通常提供详细的API文档，所有可查询的指标和函数用法都应参考官方文档。

以下是实现筛选逻辑的代码块：

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_17.png)

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_19.png)

```python
def before_trading(context):
    # 1. 构建查询：获取营业收入指标
    query = query(
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        # 2. 过滤：只保留沪深300指数成分股
        fundamentals.financial_indicator.code.in_(context.stock_pool)
    ).order_by(
        # 3. 排序：按营业收入降序排列
        fundamentals.financial_indicator.operating_revenue.desc()
    ).limit(
        # 4. 限制：只取前10名
        10
    )

    # 执行查询，将结果（DataFrame格式）存入context中供后续使用
    context.selected_stocks = get_fundamentals(query)
    # 打印结果以供检查
    print(context.selected_stocks)
```

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_21.png)

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_23.png)

**代码解析**：
*   `query(...)`：指定要查询的财务指标字段，这里是 `operating_revenue`（营业收入）。
*   `.filter(...)`：添加过滤条件，`in_()` 方法用于判断股票代码是否在我们的初始股票池 `context.stock_pool` 中。
*   `.order_by(...)`：对结果进行排序，`desc()` 表示降序排列，即营业收入从高到低。
*   `.limit(...)`：限制返回结果的数量，这里设置为10。
*   `get_fundamentals(query)`：执行构建好的查询，返回一个包含股票代码和对应营业收入数据的DataFrame。
*   我们将结果存储在 `context.selected_stocks` 中，并在后续的 `handle_data` 函数中用于交易决策。

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_25.png)

## 常见问题与调试
在初次运行策略时，可能会遇到查询结果为空的情况。这通常是由于股票池指定不正确造成的。

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_27.png)

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_29.png)

**问题**：查询结果打印为空。
**排查**：检查在 `filter` 中使用的股票池列表是否正确。确保我们是从指数成分股函数 `index_components(‘沪深300’)` 获取的列表，而不是直接使用字符串 ‘沪深300’。
**解决**：修正股票池的获取方式，确保 `context.stock_pool` 是一个有效的股票代码列表。

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_31.png)

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_33.png)

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_35.png)

修正并重新运行后，日志中应该能正确打印出每天筛选出的前10只股票代码及其营业收入数据。

![](img/d534b50a22fe4f833ccb9dc68d4d7f59_37.png)

## 总结
本节课中我们一起学习了量化交易中股票池筛选的核心流程。我们掌握了如何利用平台API查询财务数据，并通过 `filter`、`order_by` 和 `limit` 方法对股票进行过滤、排序和筛选。这个筛选出的股票池 `context.selected_stocks` 为下一步制定具体的买卖交易规则奠定了基础。记住，量化交易的本质是数据挖掘，熟练查阅和使用官方文档是获取所需数据的关键。