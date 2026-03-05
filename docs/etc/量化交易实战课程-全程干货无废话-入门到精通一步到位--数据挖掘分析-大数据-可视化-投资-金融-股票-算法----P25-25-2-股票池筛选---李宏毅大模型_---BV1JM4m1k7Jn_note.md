# Python金融分析与量化交易实战课程：P25：股票池筛选 📊

![](img/d12db8788c28540d2648e5a2b973add5_1.png)

![](img/d12db8788c28540d2648e5a2b973add5_3.png)

## 概述
在本节课中，我们将学习如何构建一个股票池筛选策略。我们将使用财务数据作为筛选指标，从沪深300指数成分股中，筛选出营收表现最好的前10只股票，为后续的交易决策做准备。

## 策略初始化与股票池设定
上一节我们介绍了策略的基本框架，本节中我们来看看如何设定初始的股票池。

![](img/d12db8788c28540d2648e5a2b973add5_5.png)

在策略的构造函数中，我们需要定义我们要操作的股票范围。这里我们选择沪深300指数作为我们的初始股票池。

![](img/d12db8788c28540d2648e5a2b973add5_7.png)

![](img/d12db8788c28540d2648e5a2b973add5_9.png)

![](img/d12db8788c28540d2648e5a2b973add5_11.png)

![](img/d12db8788c28540d2648e5a2b973add5_13.png)

```python
def initialize(context):
    # 设定股票池为沪深300指数成分股
    context.hs300 = index_components('000300.XSHG')
```

## 数据预处理与查询构建
在交易日开始前，我们需要进行数据预处理，即根据财务指标筛选股票。这通常在 `before_trading` 函数中完成。

以下是构建数据查询的步骤：

1.  **确定查询指标**：我们首先需要确定使用哪个财务指标进行筛选。例如，我们可以选择“营业总收入”。
2.  **构建查询语句**：使用平台提供的 `query` 函数，并传入我们选择的指标。
3.  **应用过滤条件**：通过 `filter` 方法，限定只查询属于我们股票池（沪深300）的股票。
4.  **排序与限制**：对查询结果按指标进行排序（例如降序），并限制返回结果的数量（如前10名）。

![](img/d12db8788c28540d2648e5a2b973add5_15.png)

```python
def before_trading(context):
    # 构建查询：获取营业总收入指标
    q = query(
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        fundamentals.financial_indicator.code.in_(context.hs300)
    ).order_by(
        fundamentals.financial_indicator.operating_revenue.desc()
    ).limit(10)

    # 执行查询，并将结果存入context中供后续使用
    context.top_stocks = get_fundamentals(q)
    # 打印结果以供检查
    log.info(context.top_stocks)
```

![](img/d12db8788c28540d2648e5a2b973add5_17.png)

![](img/d12db8788c28540d2648e5a2b973add5_19.png)

![](img/d12db8788c28540d2648e5a2b973add5_21.png)

## 代码调试与问题排查
运行上述代码后，如果发现查询结果为空，需要检查问题所在。一个常见的原因是股票池的指定方式不正确。

![](img/d12db8788c28540d2648e5a2b973add5_23.png)

例如，直接使用指数名称字符串可能无法被识别。正确的做法是使用平台提供的特定函数（如 `index_components`）或标准的指数代码来获取成分股列表。

![](img/d12db8788c28540d2648e5a2b973add5_25.png)

![](img/d12db8788c28540d2648e5a2b973add5_27.png)

修正股票池定义后，重新运行回测，即可在日志中看到每天筛选出的前10只股票列表。

![](img/d12db8788c28540d2648e5a2b973add5_29.png)

![](img/d12db8788c28540d2648e5a2b973add5_31.png)

![](img/d12db8788c28540d2648e5a2b973add5_33.png)

## 总结
本节课中我们一起学习了股票池筛选的基本流程。我们首先在策略初始化时设定了沪深300作为基础股票池，然后在每个交易日开始前，使用 `query` 和 `get_fundamentals` 函数，基于“营业总收入”这一财务指标，从池中筛选出排名前10的股票。这个过程体现了量化交易中“数据驱动”的核心思想，即通过客观的数据指标来指导投资决策。在接下来的课程中，我们将基于这个筛选出的股票池，进行具体的交易操作。