# 量化交易入门：P25：2-股票池筛选 📊

在本节课中，我们将学习如何构建一个股票池筛选策略。我们将从一个基础的沪深300股票池出发，通过查询财务数据（如营业收入）对股票进行排序和筛选，最终选出排名靠前的股票。整个过程将演示如何在量化框架中实现数据查询、条件过滤和排序。

---

![](img/2917b4949fb3ecc6df974f6f153228c0_1.png)

## 构造函数与股票池初始化

首先，在策略的构造函数中，我们需要初始化我们的股票池。这里我们选择沪深300指数作为初始的股票池。你可以使用指数名称或代码来指定。

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    context.stock_pool = index_components('沪深300')
```

![](img/2917b4949fb3ecc6df974f6f153228c0_3.png)

初始化完成后，我们不再需要打印额外的信息。接下来，我们将进入核心的数据预处理部分。

---

## 数据预处理与财务指标查询

![](img/2917b4949fb3ecc6df974f6f153228c0_5.png)

上一节我们初始化了股票池，本节中我们来看看如何获取并处理股票的财务数据。这需要使用平台提供的查询功能。

所有可用的查询指标和函数都在官方帮助文档中。量化交易本质上是数据挖掘，涉及大量指标。本次示例我们以查询“营业收入”这个财务指标为例。

![](img/2917b4949fb3ecc6df974f6f153228c0_7.png)

![](img/2917b4949fb3ecc6df974f6f153228c0_9.png)

以下是查询财务数据的基本函数结构：

![](img/2917b4949fb3ecc6df974f6f153228c0_11.png)

```python
get_fundamentals(query, date=None)
```

![](img/2917b4949fb3ecc6df974f6f153228c0_13.png)

该函数需要传入一个 `query` 参数来指定查询内容，并可以使用 `filter` 进行条件过滤，这与我们的筛选需求相符。

![](img/2917b4949fb3ecc6df974f6f153228c0_15.png)

---

## 构建查询语句

以下是构建查询、过滤、排序和限制结果数量的完整步骤。

首先，我们需要定义查询（query）。我们想查询的是基础财务数据中的“营业收入”指标。

```python
# 定义查询，选择营业收入指标
q = query(
    fundamentals.financial_indicator.operating_revenue
)
```

查询定义好后，我们需要指定在哪些股票上执行这个查询。这通过 `filter` 实现，我们只筛选属于沪深300股票池的股票。

```python
# 过滤条件：股票代码在初始化的股票池中
q = q.filter(
    fundamentals.financial_indicator.code.in_(context.stock_pool)
)
```

接下来，我们对结果进行排序。我们希望选出营业收入最高的股票，因此按营业收入降序排列。

```python
# 按营业收入降序排列
q = q.order_by(
    fundamentals.financial_indicator.operating_revenue.desc()
)
```

排序后，结果可能仍然很多。我们通常只关注排名最靠前的几只股票，因此使用 `limit` 来限制返回的数量。

```python
# 限制只返回前10条结果
q = q.limit(10)
```

![](img/2917b4949fb3ecc6df974f6f153228c0_17.png)

最后，我们执行查询，并将返回的 DataFrame 结果保存在上下文（context）中，以便后续使用。

![](img/2917b4949fb3ecc6df974f6f153228c0_19.png)

```python
# 执行查询并保存结果
df = get_fundamentals(q)
context.selected_stocks = df
```

![](img/2917b4949fb3ecc6df974f6f153228c0_21.png)

为了验证查询是否成功，我们可以打印出结果。由于 `before_trading` 函数在每个交易日都会运行，因此会每日打印筛选结果。

![](img/2917b4949fb3ecc6df974f6f153228c0_23.png)

```python
def before_trading(context):
    # 执行上述查询并打印结果
    df = get_fundamentals(q)
    print(df)
    context.selected_stocks = df
```

---

## 调试与修正

![](img/2917b4949fb3ecc6df974f6f153228c0_25.png)

运行回测后，如果发现打印的结果为空，需要检查问题。一个常见原因是股票池的代码不正确。我们最初可能错误地使用了股票列表，而不是指数成分股函数。

![](img/2917b4949fb3ecc6df974f6f153228c0_27.png)

正确的初始化方式应使用 `index_components` 函数来获取指数成分股：

![](img/2917b4949fb3ecc6df974f6f153228c0_29.png)

```python
# 修正后的股票池初始化
context.stock_pool = index_components('沪深300')
```

![](img/2917b4949fb3ecc6df974f6f153228c0_31.png)

修正后重新运行回测，即可在日志中看到每日筛选出的股票列表。输出结果是一个 DataFrame，横向是股票名称，纵向是具体的财务数据。

![](img/2917b4949fb3ecc6df974f6f153228c0_33.png)

---

## 总结

![](img/2917b4949fb3ecc6df974f6f153228c0_35.png)

本节课中我们一起学习了量化策略中股票池筛选的基本流程。我们从初始化一个基于沪深300的股票池开始，然后利用 `get_fundamentals` 函数查询财务指标，并通过 `filter`、`order_by` 和 `limit` 完成了股票的过滤、排序和精选。这个过程是构建更复杂选股策略的基础。记住，所有可用的数据字段和函数都应参考官方文档，这是进行有效数据挖掘的关键。