# Python金融量化交易实战课程：P21：21.2-股票池筛选 📊

## 概述
在本节课中，我们将学习如何基于财务指标对股票池进行筛选。我们将使用一个具体的例子，从沪深300指数成分股中，筛选出营业收入最高的10只股票，作为我们每日交易的候选股票池。

---

![](img/a516f12d7f91cde34623c0af9700594b_1.png)

## 初始化股票池

![](img/a516f12d7f91cde34623c0af9700594b_3.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何设定初始的股票池。

在策略的构造函数中，我们需要指定我们关注的股票范围。这里我们选择沪深300指数作为我们的初始股票池。你可以使用指数名称或代码来指定。

![](img/a516f12d7f91cde34623c0af9700594b_5.png)

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    context.stock_pool = ‘沪深300’
```

这样，我们就完成了股票池的初始化设定。

---

![](img/a516f12d7f91cde34623c0af9700594b_7.png)

## 编写预处理函数

初始化股票池后，我们需要在每日交易开始前，对股票池进行筛选和预处理。这个操作将在 `before_trading` 函数中完成。

![](img/a516f12d7f91cde34623c0af9700594b_9.png)

![](img/a516f12d7f91cde34623c0af9700594b_11.png)

我们的目标是：每日开盘前，从沪深300成分股中，查询所有股票的营业收入数据，然后按照营业收入从高到低排序，最终选出排名前10的股票。

![](img/a516f12d7f91cde34623c0af9700594b_13.png)

以下是实现这一目标的关键步骤：

![](img/a516f12d7f91cde34623c0af9700594b_15.png)

### 1. 查阅API文档
所有可用的财务指标和数据查询方法都记录在API帮助文档中。进行量化交易本质上是一种数据挖掘，涉及大量指标。本教程仅以营业收入为例进行演示。

![](img/a516f12d7f91cde34623c0af9700594b_17.png)

### 2. 使用查询函数
我们需要使用 `query` 函数来获取财务数据。该函数的核心参数是一个查询语句，用于指定要获取的指标，并可以使用 `filter` 进行条件过滤。

```python
# 从API文档中获取的基础查询函数结构
get_fundamentals(query, entry_date, interval=‘1d’)
```

### 3. 构建查询语句
查询语句 `query` 需要明确指定要查询的指标字段。例如，要查询营业收入，我们需要找到对应的字段名（如 `income.total_operating_revenue`）。

```python
# 构建查询，选择营业收入指标
q = query(income.total_operating_revenue)
```

### 4. 添加过滤条件
仅指定指标还不够，我们需要限定查询范围。通过 `filter` 条件，我们可以指定只查询属于沪深300指数成分股的股票。

```python
# 添加过滤条件：股票代码在沪深300成分股中
q = q.filter(index.code.in_(‘沪深300’))
```

### 5. 排序与限制
查询到所有符合条件的股票后，我们需要对结果进行排序。这里我们按照营业收入降序排列（即营收最高的排在最前面）。最后，使用 `limit` 方法限制只取前10条结果。

```python
# 按营业收入降序排列，并限制只取前10名
q = q.order_by(income.total_operating_revenue.desc()).limit(10)
```

### 6. 执行查询并存储结果
将构建好的查询语句 `q` 传入 `get_fundamentals` 函数执行，返回的结果是一个 `DataFrame`。我们将这个结果存储在 `context` 对象中，以便在后续的交易函数中使用。

```python
def before_trading(context):
    # 构建完整的查询
    q = query(income.total_operating_revenue).filter(index.code.in_(‘沪深300’)).order_by(income.total_operating_revenue.desc()).limit(10)
    
    # 执行查询，获取当前交易日的数据
    df = get_fundamentals(q, context.current_dt)
    
    # 将筛选出的股票池存入context
    context.filtered_pool = df
    # 打印结果以供检查
    print(df)
```

![](img/a516f12d7f91cde34623c0af9700594b_19.png)

---

![](img/a516f12d7f91cde34623c0af9700594b_21.png)

![](img/a516f12d7f91cde34623c0af9700594b_23.png)

## 调试与验证

![](img/a516f12d7f91cde34623c0af9700594b_25.png)

编写完代码后，运行回测以验证筛选逻辑是否正确。

![](img/a516f12d7f91cde34623c0af9700594b_27.png)

首次运行时，打印出的结果为空。检查发现，问题出在过滤条件上：我们错误地在股票列表中寻找指数代码。修正方法是使用正确的API函数来获取指数成分股。

修正后的过滤条件如下：

```python
# 修正：使用 get_index_stocks 获取沪深300成分股列表
stock_list = get_index_stocks(‘000300.XSHG’) # 沪深300指数代码
q = q.filter(valuation.code.in_(stock_list))
```

![](img/a516f12d7f91cde34623c0af9700594b_29.png)

修正后重新运行回测，日志中成功打印出了每日筛选出的前10只股票信息，包括股票代码和其对应的营业收入数据。

![](img/a516f12d7f91cde34623c0af9700594b_31.png)

![](img/a516f12d7f91cde34623c0af9700594b_33.png)

---

![](img/a516f12d7f91cde34623c0af9700594b_35.png)

## 总结

![](img/a516f12d7f91cde34623c0af9700594b_37.png)

本节课中我们一起学习了股票池筛选的核心流程：

1.  **初始化设定**：在 `initialize` 中定义初始关注的股票范围（如沪深300）。
2.  **预处理筛选**：在 `before_trading` 中，利用 `query`、`filter`、`order_by` 和 `limit` 方法，基于财务指标（如营业收入）对股票进行筛选和排序。
3.  **数据获取**：所有指标字段和查询方法都需要参考官方API文档。
4.  **结果应用**：将筛选出的股票列表存入 `context`，为后续的选股和交易逻辑提供输入。

![](img/a516f12d7f91cde34623c0af9700594b_39.png)

通过这个例子，我们掌握了构建动态股票池的基本方法，这是量化策略中资产配置和风险控制的第一步。你可以尝试更换不同的财务指标（如市盈率、净利润增长率）来构建属于自己的筛选策略。