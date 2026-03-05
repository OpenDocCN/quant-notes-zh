# 金融量化分析：P25：股票池筛选 📊

在本节课中，我们将学习如何利用Python进行股票池的筛选。具体来说，我们将从一个预设的股票池（如沪深300指数成分股）出发，通过查询财务数据、设定筛选条件、排序并限制数量，最终得到一个精简的、符合特定策略的股票组合。这是构建量化交易策略的关键一步。

上一节我们介绍了量化交易的基本框架，本节中我们来看看如何实现一个具体的股票筛选策略。

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_1.png)

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_2.png)

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_3.png)

## 初始化股票池 📈

首先，我们需要在策略的构造函数中定义我们的初始股票池。这里我们选择沪深300指数作为基础池。

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_5.png)

```python
# 在策略的构造函数中定义股票池
self.stock_pool = ['沪深300']  # 也可以使用指数代码，如‘000300.SH’
```

打印信息可以暂时移除，以保持代码简洁。

## 数据预处理与查询 🔍

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_7.png)

在每日交易前，我们需要对股票池进行预处理，即根据财务指标进行筛选。这需要查询相关数据。

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_9.png)

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_10.png)

所有可用的查询指标都在官方帮助文档中列出。量化交易本质上是数据挖掘，涉及大量指标。本次示例我们仅使用一个财务指标进行演示。

以下是查询财务数据的基本步骤：
1.  使用 `query` 函数指定要查询的指标。
2.  使用 `filter` 函数过滤条件，例如限定股票范围。
3.  使用 `order_by` 对结果进行排序。
4.  使用 `limit` 限制返回结果的数量。

我们将查询“营业总收入”这个财务指标，并在沪深300成分股中进行筛选。

```python
# 在 before_trading 函数中进行数据查询与处理
query = query(fundamentals.income_statement.revenue)  # 查询营业总收入指标
```

## 构建筛选逻辑 ⚙️

定义了查询目标后，我们需要构建完整的筛选逻辑。这包括过滤、排序和数量限制。

以下是构建筛选逻辑的具体操作：
*   **过滤 (`filter`)**：确保查询的股票在我们的初始股票池（沪深300）中。
*   **排序 (`order_by`)**：按照营业总收入进行降序排列，以选出营收最高的公司。
*   **限制 (`limit`)**：只选取排名前10的股票。

```python
# 构建完整的查询语句
df = get_fundamentals(query.
                      filter(fundamentals.eod_derivative_indicator.code.in_(self.stock_pool)).
                      order_by(fundamentals.income_statement.revenue.desc()).
                      limit(10))
# 将结果存入上下文，供后续交易逻辑使用
context.fundamental_df = df
```

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_12.png)

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_13.png)

为了验证查询结果，我们可以先打印输出。

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_14.png)

```python
print(df)
```

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_16.png)

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_17.png)

## 调试与验证 🐛

运行回测后，如果发现打印结果为空，可能是股票池定义有误。需要检查股票池的引用是否正确。

问题可能在于直接使用了指数名称，而非正确的指数代码或成分股列表。应通过API查找准确的指数代码。

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_19.png)

```python
# 修正后的股票池定义，使用正确的指数代码
self.stock_pool = ['000300.SH']  # 沪深300指数代码
```

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_21.png)

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_22.png)

修正后重新运行回测，即可在日志中看到按日筛选出的股票列表及其营收数据。

![](img/9dfd6eff9cb6ba16b4ce6d796f9dce5b_24.png)

本节课中我们一起学习了如何从基础股票池出发，通过查询财务指标、应用过滤、排序和限制条件，动态构建一个更精细的股票组合。这是量化选股策略的核心环节，为后续的买卖决策提供了数据基础。