# 量化交易全教程：P22：02-2-股票池筛选 📊

## 概述
在本节课中，我们将学习如何在量化交易策略中筛选股票池。我们将使用财务数据作为筛选指标，从沪深300指数成分股中，根据营业收入筛选出排名前十的股票，为后续的交易决策做准备。

![](img/3f55075a32bd52dec4d4a3828f384882_1.png)

## 策略初始化
上一节我们介绍了策略的基本框架，本节中我们来看看如何初始化我们的股票池。

![](img/3f55075a32bd52dec4d4a3828f384882_3.png)

在策略的构造函数中，我们需要定义我们关注的股票池。这里我们选择沪深300指数作为初始股票池。你可以使用指数名称或代码来指定。

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    context.stock_pool = ‘沪深300’
```

![](img/3f55075a32bd52dec4d4a3828f384882_5.png)

## 数据预处理与筛选
初始化股票池后，我们需要在每天交易开始前进行数据预处理和筛选。这通常在 `before_trading` 函数中完成。

![](img/3f55075a32bd52dec4d4a3828f384882_7.png)

![](img/3f55075a32bd52dec4d4a3828f384882_9.png)

我们的目标是：每天从沪深300成分股中，查询所有股票的营业收入数据，然后按照营业收入从高到低排序，最终选出排名前十的股票。

![](img/3f55075a32bd52dec4d4a3828f384882_11.png)

![](img/3f55075a32bd52dec4d4a3828f384882_13.png)

以下是实现此功能的关键步骤：

![](img/3f55075a32bd52dec4d4a3828f384882_15.png)

### 1. 理解查询函数
量化交易的核心是数据挖掘，平台提供了丰富的财务指标查询功能。所有可用的指标和函数用法都在官方帮助文档中。

我们需要使用的核心函数是 `query_fundamentals`，它用于查询财务数据。该函数主要接受一个 `query` 参数来指定查询的指标，并可以用 `filter` 参数来过滤条件。

### 2. 构建查询语句
首先，我们需要构建查询语句。我们想查询的是“营业收入”这个财务指标。

```python
# 导入必要的模块
from jqdata import *

query = query(
    fundamentals.eod_derivative_indicator.operating_revenue
)
```
在帮助文档的“财务数据”部分，可以找到 `operating_revenue`（营业收入）等所有可用字段。

### 3. 添加过滤条件
仅查询指标还不够，我们需要限定查询范围在我们的股票池内。这通过 `filter` 实现。

![](img/3f55075a32bd52dec4d4a3828f384882_17.png)

![](img/3f55075a32bd52dec4d4a3828f384882_19.png)

```python
# 添加过滤条件：只查询属于沪深300指数成分股的股票
query = query.filter(
    fundamentals.stockcode.in_(get_index_stocks(‘000300.XSHG‘)) # 沪深300指数代码
)
```

![](img/3f55075a32bd52dec4d4a3828f384882_21.png)

![](img/3f55075a32bd52dec4d4a3828f384882_23.png)

### 4. 排序与限制结果
过滤出股票池内的股票后，我们需要对结果进行排序，并只保留我们需要的数量。

![](img/3f55075a32bd52dec4d4a3828f384882_25.png)

```python
# 按营业收入降序排列，并限制只取前10名
query = query.order_by(
    fundamentals.eod_derivative_indicator.operating_revenue.desc()
).limit(10)
```

![](img/3f55075a32bd52dec4d4a3828f384882_27.png)

### 5. 执行查询并存储结果
最后，执行完整的查询，并将结果存储在 `context` 中，以便在交易函数中使用。

![](img/3f55075a32bd52dec4d4a3828f384882_29.png)

![](img/3f55075a32bd52dec4d4a3828f384882_31.png)

```python
def before_trading(context):
    # 执行查询，获取DataFrame格式的结果
    df = get_fundamentals(query)

    # 将筛选出的股票列表存入context
    context.selected_stocks = df[‘code’].tolist()

    # 打印结果以供检查（实际运行时可以注释掉）
    print(df)
```

![](img/3f55075a32bd52dec4d4a3828f384882_33.png)

![](img/3f55075a32bd52dec4d4a3828f384882_35.png)

**注意**：初次运行时，如果发现结果为空，请检查股票池的指定方式是否正确。确保使用 `get_index_stocks(‘指数代码’)` 来获取指数成分股列表，而不是直接使用指数名称。

![](img/3f55075a32bd52dec4d4a3828f384882_37.png)

## 总结
本节课中我们一起学习了股票池筛选的完整流程。我们首先在策略初始化时设定了沪深300作为基础股票池。然后，通过在 `before_trading` 函数中构建查询语句，结合 `filter`、`order_by` 和 `limit` 方法，实现了基于营业收入指标从大到小的排序，并最终筛选出每日前十的股票。这个过程是构建量化选股策略的基础，后续我们可以在此基础上加入更复杂的交易逻辑。