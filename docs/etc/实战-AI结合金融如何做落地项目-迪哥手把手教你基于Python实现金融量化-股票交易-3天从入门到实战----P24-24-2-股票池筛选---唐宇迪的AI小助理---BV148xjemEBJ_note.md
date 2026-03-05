# Python金融分析与量化交易实战：P24：股票池筛选 📊

![](img/6a5875b706ed34c9618e8acfccb49a37_1.png)

在本节课中，我们将学习如何基于财务指标筛选股票池。我们将使用一个具体的例子，从沪深300指数成分股中，筛选出营业收入最高的10只股票，作为我们每日交易的候选股票池。

![](img/6a5875b706ed34c9618e8acfccb49a37_3.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何实现一个具体的股票筛选逻辑。

![](img/6a5875b706ed34c9618e8acfccb49a37_5.png)

## 策略初始化与股票池设定

![](img/6a5875b706ed34c9618e8acfccb49a37_7.png)

首先，在策略的构造函数中，我们需要设定初始的股票池范围。这里我们选择沪深300指数作为我们的基础股票池。

![](img/6a5875b706ed34c9618e8acfccb49a37_9.png)

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    context.hs300 = '000300.XSHG'  # 沪深300指数代码
```

![](img/6a5875b706ed34c9618e8acfccb49a37_11.png)

![](img/6a5875b706ed34c9618e8acfccb49a37_13.png)

![](img/6a5875b706ed34c9618e8acfccb49a37_15.png)

## 每日开盘前的数据预处理

![](img/6a5875b706ed34c9618e8acfccb49a37_17.png)

在每日交易开始前（`before_trading`函数中），我们需要执行数据查询和筛选。以下是实现这一功能的核心步骤。

以下是实现股票筛选的具体步骤：

1.  **构建查询（Query）**：使用`query`函数指定要查询的财务指标，例如营业收入。
2.  **应用过滤器（Filter）**：使用`filter`函数限定查询范围，确保只查询我们股票池（沪深300）内的股票。
3.  **排序（Order）**：使用`order_by`函数对查询结果进行排序，例如按营业收入降序排列。
4.  **限制数量（Limit）**：使用`limit`函数限制最终输出的股票数量，例如只选取前10名。

## 代码实现详解

现在，让我们将上述步骤转化为具体的代码。我们将查询沪深300成分股中营业收入最高的10只股票。

![](img/6a5875b706ed34c9618e8acfccb49a37_19.png)

```python
def before_trading(context):
    # 步骤1：构建查询，指定要获取“营业收入”指标
    q = query(
        fundamentals.eod_derivative_indicator.operating_revenue
    ).filter(
        # 步骤2：过滤，确保股票代码在沪深300成分股内
        fundamentals.eod_derivative_indicator.code.in_(get_index_stocks('000300.XSHG'))
    ).order_by(
        # 步骤3：排序，按营业收入降序排列
        fundamentals.eod_derivative_indicator.operating_revenue.desc()
    ).limit(
        # 步骤4：限制数量，只取前10名
        10
    )
    
    # 执行查询，获取一个DataFrame格式的结果
    df = get_fundamentals(q)
    
    # 将筛选出的股票列表存入context，供后续交易函数使用
    context.stock_list = df.columns.values
    
    # 打印结果以供检查（在实际回测中可注释掉以提升速度）
    print(df)
```

![](img/6a5875b706ed34c9618e8acfccb49a37_21.png)

![](img/6a5875b706ed34c9618e8acfccb49a37_23.png)

![](img/6a5875b706ed34c9618e8acfccb49a37_25.png)

**代码要点说明**：
*   `get_fundamentals(q)` 是执行查询的核心函数，它返回一个`DataFrame`。
*   `df.columns.values` 可以获取这个`DataFrame`的列名，即筛选出的股票代码列表。
*   将结果存入`context.stock_list`，这样在`handle_data`等函数中就可以直接使用这个筛选后的股票池进行交易决策。

![](img/6a5875b706ed34c9618e8acfccb49a37_27.png)

![](img/6a5875b706ed34c9618e8acfccb49a37_29.png)

## 常见问题与调试

在编写和运行策略时，可能会遇到查询结果为空的情况。这通常是由于数据获取范围或代码标识不正确导致的。

![](img/6a5875b706ed34c9618e8acfccb49a37_31.png)

![](img/6a5875b706ed34c9618e8acfccb49a37_33.png)

![](img/6a5875b706ed34c9618e8acfccb49a37_35.png)

如果查询结果为空，请检查以下两点：

![](img/6a5875b706ed34c9618e8acfccb49a37_37.png)

![](img/6a5875b706ed34c9618e8acfccb49a37_39.png)

*   **股票池代码是否正确**：确保使用`get_index_stocks('指数代码')`来获取指数成分股列表，而不是直接使用指数代码本身。
*   **财务指标字段名是否准确**：所有可用的财务指标字段都需要参考官方API文档，确保名称完全一致。

![](img/6a5875b706ed34c9618e8acfccb49a37_41.png)

本节课中我们一起学习了如何利用财务数据动态筛选股票池。我们掌握了`query`、`filter`、`order_by`、`limit`这一套数据查询组合拳，实现了从指定指数中筛选出符合特定财务条件（如营业收入Top 10）的股票。这是构建量化选股策略非常基础和关键的一步。在接下来的课程中，我们将学习如何基于这个筛选出的股票池执行具体的买卖交易。