# Python金融分析与量化交易实战教程：P22：2-股票池筛选 📊

## 概述
在本节课中，我们将学习如何构建一个股票池筛选策略。我们将使用沪深300指数作为基础股票池，并基于财务指标（如营业收入）对股票进行排序和筛选，最终选出排名靠前的股票进行后续分析。

![](img/ce88ebc2f22c497b33aaee0a02aa79de_1.png)

---

## 策略初始化与股票池设定
上一节我们介绍了策略的基本框架，本节中我们来看看如何设定初始的股票池。

![](img/ce88ebc2f22c497b33aaee0a02aa79de_3.png)

在策略的构造函数中，我们需要指定要操作的股票池。这里我们选择沪深300指数作为我们的基础股票池。你可以使用指数名称或代码来指定。

```python
def initialize(context):
    # 设定股票池为沪深300指数
    context.stock_pool = '沪深300'
```

![](img/ce88ebc2f22c497b33aaee0a02aa79de_5.png)

打印信息在此处不需要，因此可以移除。

![](img/ce88ebc2f22c497b33aaee0a02aa79de_7.png)

---

![](img/ce88ebc2f22c497b33aaee0a02aa79de_9.png)

![](img/ce88ebc2f22c497b33aaee0a02aa79de_11.png)

## 数据预处理与指标查询
设定好股票池后，我们需要在每日交易开始前进行数据预处理，即获取并筛选股票。

![](img/ce88ebc2f22c497b33aaee0a02aa79de_13.png)

![](img/ce88ebc2f22c497b33aaee0a02aa79de_15.png)

以下是实现数据预处理的关键步骤：

1.  **查阅帮助文档**：所有可用的财务指标和查询方法都在官方帮助文档中。量化交易本质上是数据挖掘，涉及大量指标。本教程仅以营业收入为例。
2.  **使用查询函数**：我们将使用 `get_fundamentals` 函数来查询财务数据。该函数需要一个 `query` 对象来指定查询内容，并可以用 `filter` 进行条件过滤。
3.  **构建查询逻辑**：查询逻辑包括选择指标、过滤股票池、排序和限制返回数量。

我们将查询函数复制到策略的 `before_trading` 函数中。

```python
def before_trading(context):
    # 构建查询对象
    q = query(
        # 指定要查询的指标：营业总收入
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        # 过滤条件：股票代码必须在沪深300成分股中
        fundamentals.eod_derivative_indicator.stockcode.in_(context.stock_pool)
    ).order_by(
        # 排序：按营业总收入降序排列（从大到小）
        fundamentals.financial_indicator.operating_revenue.desc()
    ).limit(
        # 限制返回结果数量：只取前10名
        10
    )
    
    # 执行查询，结果是一个DataFrame
    context.filtered_stocks = get_fundamentals(q)
    
    # 打印结果以供检查
    print(context.filtered_stocks)
```

这段代码会在每个交易日开始前运行，筛选出沪深300中营业收入最高的10只股票，并将结果存储在 `context.filtered_stocks` 中。

---

## 代码调试与问题解决
编写完代码后，我们运行回测以检查筛选结果。

![](img/ce88ebc2f22c497b33aaee0a02aa79de_17.png)

首次运行时，打印出的结果是空的DataFrame。这表明我们的筛选条件可能未正确生效。

![](img/ce88ebc2f22c497b33aaee0a02aa79de_19.png)

![](img/ce88ebc2f22c497b33aaee0a02aa79de_21.png)

问题在于，我们在过滤条件中直接使用了 `‘沪深300’` 这个字符串，但系统可能需要更精确的指数标识。我们需要查阅API文档，找到沪深300指数的正确代码或调用方式。

![](img/ce88ebc2f22c497b33aaee0a02aa79de_23.png)

修正方法如下：

![](img/ce88ebc2f22c497b33aaee0a02aa79de_25.png)

```python
def initialize(context):
    # 修正：使用正确的指数代码或API函数获取沪深300成分股
    # 假设使用 `get_index_stocks` 函数
    context.stock_pool = get_index_stocks('000300.XSHG') # 沪深300指数代码示例
```

修正后重新运行回测，日志中成功打印出了每天筛选出的股票列表。列表横向显示股票代码，纵向显示对应的财务数据。

![](img/ce88ebc2f22c497b33aaee0a02aa79de_27.png)

![](img/ce88ebc2f22c497b33aaee0a02aa79de_29.png)

---

![](img/ce88ebc2f22c497b33aaee0a02aa79de_31.png)

![](img/ce88ebc2f22c497b33aaee0a02aa79de_33.png)

## 总结
本节课中我们一起学习了股票池筛选的核心流程。

![](img/ce88ebc2f22c497b33aaee0a02aa79de_35.png)

1.  **初始化股票池**：在 `initialize` 函数中设定基础股票范围（如沪深300）。
2.  **预处理与查询**：在 `before_trading` 函数中，使用 `query` 对象构建复杂的财务数据查询，结合 `filter`、`order_by` 和 `limit` 完成筛选和排序。
3.  **调试与验证**：通过打印中间结果，验证查询逻辑是否正确，并根据API文档修正参数。

![](img/ce88ebc2f22c497b33aaee0a02aa79de_37.png)

我们成功实现了一个简单的策略：每日选择沪深300指数中营业收入最高的10只股票。这为后续构建交易信号和下单逻辑奠定了基础。下一节，我们将学习如何基于筛选出的股票池执行具体的交易操作。