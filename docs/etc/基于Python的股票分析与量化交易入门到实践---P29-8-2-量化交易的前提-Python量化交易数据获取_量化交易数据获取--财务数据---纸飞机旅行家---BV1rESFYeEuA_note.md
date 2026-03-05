# 基于Python的股票分析与量化交易入门到实践 - P29：8.2 量化交易的前提-Python量化交易数据获取_量化交易数据获取--财务数据

在本节课中，我们将要学习如何通过聚宽（JoinQuant）的API获取股票的财务数据。财务数据是量化分析中评估公司基本面的重要依据，掌握其获取方法是进行深入分析的基础。

上一节我们介绍了如何获取股票的历史行情数据，本节中我们来看看如何获取更复杂的财务数据。

## 📊 常用财务数据获取函数

获取财务数据主要依赖于聚宽API提供的几个核心函数。以下是三个关键的函数：

1.  **`get_fundamentals`**：此函数用于查询**单日**或**单季度**的财务数据。
2.  **`query`对象**：这是一个强大的查询构建工具，支持过滤、分组、排序等操作，是`get_fundamentals`函数的核心参数。
3.  **`get_fundamentals_continuously`**：此函数用于**连续**查询多日或多季度的财务数据。

掌握了这三个部分，你就具备了查询财务数据的基本能力。接下来，我们将逐一详细讲解。

## 🔍 详解 `get_fundamentals` 函数

`get_fundamentals` 函数的作用是查询指定时间点的财务数据。它的核心参数如下：

*   **`query_object`**：一个`query`对象，定义了要查询的数据表和字段。
*   **`date`**：指定一个**日期**。函数将返回在该日期收盘后所能看到的最新财务数据。
*   **`statDate`**：指定一个**季度或年份**。例如 `‘2022Q2‘` 或 `‘2022‘`。

**注意**：`date`和`statDate`参数只能传入其中一个，不能同时使用。

参数`date`或`statDate`可以是字符串（如 `‘2022-09-01‘`， `‘2022Q2‘`），也可以是Python的`datetime`或`datetime.date`类型。

## 🛠️ 详解 `query` 对象

`query`对象是Python库`sqlalchemy`提供的查询接口，它被聚宽封装用于财务数据查询。它本质上是一个结构化的查询构建器，功能非常强大。

以下是`query`对象支持的主要操作：

*   **`filter()`**：用于设置过滤条件，支持等于(`==`)、大于(`>`)、小于(`<`)等比较运算符，以及逻辑运算符`and_()`和`or_()`。
*   **`order_by()`**：用于对结果进行排序，可以指定升序或降序。
*   **`limit()`**：用于限制返回结果的条数。
*   **`group_by()`**：用于对数据进行分组聚合。

举个例子，如果想查询平安银行（股票代码 `‘000001.XSHE‘`）的市值数据，可以这样构建`query`：

```python
q = query(valuation).filter(valuation.code == ‘000001.XSHE‘)
```

这里，`valuation`是聚宽API中一张存储市值相关数据（如总市值、市盈率等）的表。除了`valuation`，还有`income`（利润表）、`balance`（资产负债表）等许多财务数据表，具体需要查阅聚宽官方文档。

## 📈 详解 `get_fundamentals_continuously` 函数

这个函数名虽长，但功能明确：**查询多日/多季度的连续财务数据**。它的参数与`get_fundamentals`略有不同：

*   **`query_object`**：同样是定义查询的`query`对象。
*   **`end_date`**：查询的结束日期，格式与`date`参数相同。
*   **`count`**：从`end_date`开始向前追溯的数据条数。出于性能考虑，`count`通常需要小于500。
*   **`panel`**：一个布尔值参数。建议设置为`False`，这样函数会返回一个标准的`pandas DataFrame`，便于后续数据处理。

![](img/23dc9077aa168244a4c73e08dfe4f7e6_1.png)

## 💻 实战演练：获取财务数据

理论介绍完毕，下面我们进入实战环节，通过代码演示如何获取财务数据。

### 示例一：查询单日市值

我们来查询平安银行在2022年9月1日的总市值。

```python
# 导入聚宽API，通常在策略研究环境中自动可用
# from jqdata import *

# 1. 构建查询对象：从valuation表查询，过滤出平安银行
q = query(valuation).filter(valuation.code == ‘000001.XSHE‘)

# 2. 执行查询，指定日期
df = get_fundamentals(q, date=‘2022-09-01‘)

# 3. 打印结果（总市值对应的字段名需查文档，这里假设为‘market_cap‘）
print(df[‘market_cap‘])
```

运行代码，即可得到平安银行在指定日期的总市值。具体字段名称（如`market_cap`）请以聚宽官方文档为准。

### 示例二：查询单季度财务数据

接下来，我们查询平安银行2022年第二季度（Q2）的一些基本财务数据，例如每股收益。

```python
# 1. 构建查询对象：从income（利润表）查询，过滤出平安银行
#    假设每股收益的字段为‘basic_eps‘
q = query(income.basic_eps).filter(income.code == ‘000001.XSHE‘)

# 2. 执行查询，指定季度
df = get_fundamentals(q, statDate=‘2022Q2‘)

# 3. 打印结果
print(df)
```

这段代码将返回平安银行2022年第二季度的每股收益数据。同样，实际字段名请参考文档。

### 示例三：连续查询多只股票的多项数据

最后，我们使用`get_fundamentals_continuously`函数，同时查询平安银行和浦发银行在2022年初之前多个季度的几项关键指标。

```python
# 1. 构建复杂的查询对象
#    查询多只股票：平安银行(000001.XSHE) 和 浦发银行(600000.XSHG)
#    查询多个字段：总市值、市盈率(PE)、换手率、每股收益
q = query(
    valuation.code,
    valuation.market_cap,        # 总市值
    valuation.pe_ratio,          # 市盈率
    valuation.turnover_ratio,    # 换手率
    income.basic_eps             # 每股收益
).filter(
    valuation.code.in_([‘000001.XSHE‘, ‘600000.XSHG‘])
)

# 2. 执行连续查询
#    查询截止到2022-01-01，向前取5个时间点的数据
df = get_fundamentals_continuously(q, end_date=‘2022-01-01‘, count=5, panel=False)

![](img/23dc9077aa168244a4c73e08dfe4f7e6_3.png)

# 3. 打印结果
print(df)
```

运行后，你会得到一个`DataFrame`，包含了这两只股票在指定时间段内的多项财务数据。数据默认可能按股票代码分组，你可以使用`pandas`的功能对其进行重塑和排序。

`query`对象的功能远不止于此，它还支持更复杂的过滤（如`valuation.pe_ratio > 10`）和分组聚合操作，这需要你熟悉`sqlalchemy`的语法和聚宽的数据表结构。

## 📝 本章小结

本节课中我们一起学习了如何获取股票相关的财务数据，主要内容包括：

1.  我们介绍了用于查询**单日或单季度**数据的`get_fundamentals`函数，其核心是传入一个`query`对象，并选择`date`或`statDate`参数之一。
2.  我们深入了解了**`query`对象**，它基于`sqlalchemy`库，支持`filter`、`group_by`、`order_by`、`limit`等强大查询功能，相当于将SQL语句结构化，方便我们构建复杂查询。
3.  我们介绍了用于**连续查询多期数据**的`get_fundamentals_continuously`函数，并演示了其参数用法。
4.  最后，我们通过三个实战示例，演示了如何编写代码查询单日市值、单季度收益以及多只股票的多期多项财务数据。

要熟练运用这些功能，关键在于熟悉聚宽平台提供的各类财务数据表（如`valuation`， `income`）及其字段含义。建议多查阅官方文档并进行实践。

![](img/23dc9077aa168244a4c73e08dfe4f7e6_5.png)

我是米tea。