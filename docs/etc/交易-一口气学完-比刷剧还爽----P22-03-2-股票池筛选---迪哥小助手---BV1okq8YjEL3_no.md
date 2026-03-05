# Python金融量化与股票交易：P22：03-2-股票池筛选 📊

在本节课中，我们将学习如何构建一个股票池筛选策略。我们将基于财务指标（如营业收入）从沪深300指数成分股中筛选出表现最佳的股票，并进行排序和数量限制，为后续的交易决策做准备。

## 策略初始化与股票池设定

![](img/0fbd44fa59db541337b35502559b6dba_1.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何设定初始的股票池。

在策略的构造函数中，我们需要定义我们的股票池。这里我们选择沪深300指数作为初始的股票池。你可以使用指数名称或代码来指定。

```python
def initialize(context):
    # 设定股票池为沪深300指数成分股
    context.stock_pool = index_stocks('沪深300')
```

![](img/0fbd44fa59db541337b35502559b6dba_3.png)

打印信息在此处不需要，因此可以省略。

## 数据预处理与财务指标查询

![](img/0fbd44fa59db541337b35502559b6dba_5.png)

在每日交易开始前，我们需要对股票池进行预处理，即根据财务数据筛选股票。这涉及到查询特定财务指标。

所有可用的查询指标和函数都在平台的帮助文档中。量化交易本质上是数据挖掘，涉及大量指标。本次示例我们仅使用营业收入这一指标。

![](img/0fbd44fa59db541337b35502559b6dba_7.png)

![](img/0fbd44fa59db541337b35502559b6dba_9.png)

以下是查询财务数据的基本函数结构：

![](img/0fbd44fa59db541337b35502559b6dba_11.png)

![](img/0fbd44fa59db541337b35502559b6dba_13.png)

```python
get_fundamentals(query_object, date=None, statDate=None)
```

![](img/0fbd44fa59db541337b35502559b6dba_15.png)

我们需要构建一个查询对象（`query_object`），其中包含要查询的指标、过滤条件和排序规则。

## 构建查询、过滤与排序逻辑

现在，我们来具体构建筛选逻辑。这个过程分为三步：查询指标、过滤股票池、排序并限制数量。

首先，在 `before_trading` 函数中，我们开始构建查询。

```python
def before_trading(context):
    # 1. 构建查询：查询营业收入指标
    q = query(
        fundamentals.eod_derivative_indicator.operating_revenue
    )
```

接下来，我们需要添加过滤条件，确保只查询我们股票池（即沪深300成分股）中的股票。

```python
    # 2. 添加过滤条件：股票代码必须在沪深300成分股中
    q = q.filter(
        fundamentals.eod_derivative_indicator.code.in_(context.stock_pool)
    )
```

然后，我们对结果进行排序。我们希望选出营业收入最高的股票，因此需要按营业收入降序排列。

```python
    # 3. 添加排序规则：按营业收入降序排列
    q = q.order_by(
        fundamentals.eod_derivative_indicator.operating_revenue.desc()
    )
```

最后，我们限制只取排名前10的股票。

![](img/0fbd44fa59db541337b35502559b6dba_17.png)

```python
    # 4. 限制返回结果数量：只取前10名
    q = q.limit(10)
```

![](img/0fbd44fa59db541337b35502559b6dba_19.png)

![](img/0fbd44fa59db541337b35502559b6dba_21.png)

执行查询，并将结果（一个DataFrame）保存到 `context` 中，以便后续使用。同时，我们可以打印出来查看结果。

![](img/0fbd44fa59db541337b35502559b6dba_23.png)

```python
    # 执行查询并保存结果
    context.filtered_stocks = get_fundamentals(q)
    # 打印查看结果
    print(context.filtered_stocks)
```

![](img/0fbd44fa59db541337b35502559b6dba_25.png)

其他暂时不用的函数（如 `handle_data`）可以先使用 `pass` 语句跳过。

## 调试与验证

![](img/0fbd44fa59db541337b35502559b6dba_27.png)

运行回测后，如果发现打印结果为空，需要检查问题。常见原因是股票池指定不正确。确保在获取指数成分股时使用了正确的函数和指数名称。

![](img/0fbd44fa59db541337b35502559b6dba_29.png)

修正后的股票池初始化代码如下：

![](img/0fbd44fa59db541337b35502559b6dba_31.png)

![](img/0fbd44fa59db541337b35502559b6dba_33.png)

```python
def initialize(context):
    # 正确获取沪深300指数成分股
    context.stock_pool = get_index_stocks('000300.XSHG') # 使用指数代码
    # 或者使用指数名称（需确认平台支持）
    # context.stock_pool = index_stocks('沪深300')
```

重新运行回测，即可在日志中看到每天筛选出的前10只股票列表。数据以DataFrame形式呈现，横向是股票名称，纵向是具体的财务数据。

![](img/0fbd44fa59db541337b35502559b6dba_35.png)

## 总结

![](img/0fbd44fa59db541337b35502559b6dba_37.png)

本节课中我们一起学习了股票池筛选的核心步骤。我们首先在策略初始化时设定了沪深300作为基础股票池。然后，在每日交易前，通过查询财务指标（营业收入）、过滤股票池、按指标降序排序并限制数量，动态筛选出当日最值得关注的10只股票。这个过程是量化选股的基础，后续可以引入更多指标和复杂逻辑来优化筛选策略。