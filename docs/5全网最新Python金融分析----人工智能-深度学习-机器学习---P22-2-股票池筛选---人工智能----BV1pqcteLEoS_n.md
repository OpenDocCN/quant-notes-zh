# Python金融量化实战教程：P22：2-股票池筛选 📊

![](img/666f195dab951ee036444b3c790d5fb8_1.png)

## 概述
在本节课中，我们将学习如何构建一个股票池筛选策略。我们将从沪深300指数中，根据财务指标（如营业收入）筛选出表现最佳的股票，为后续的交易决策做准备。整个过程将涉及数据查询、条件过滤、排序和限制结果数量。

![](img/666f195dab951ee036444b3c790d5fb8_3.png)

## 策略初始化与股票池设定
上一节我们介绍了策略的基本框架，本节中我们来看看如何设定初始的股票池。

![](img/666f195dab951ee036444b3c790d5fb8_5.png)

在策略的构造函数中，我们需要定义我们的初始股票池。这里我们选择沪深300指数作为我们的股票池。你可以使用指数名称或代码来指定。

![](img/666f195dab951ee036444b3c790d5fb8_7.png)

![](img/666f195dab951ee036444b3c790d5fb8_9.png)

![](img/666f195dab951ee036444b3c790d5fb8_11.png)

```python
def initialize(context):
    # 设定初始股票池为沪深300指数
    context.stock_pool = '沪深300'
    # 后续可以调整起始资金、频率（日/分钟）等参数，此处暂用默认值
```

![](img/666f195dab951ee036444b3c790d5fb8_13.png)

![](img/666f195dab951ee036444b3c790d5fb8_15.png)

## 数据预处理与指标查询
设定好股票池后，我们需要在每日交易开始前进行数据预处理，即根据财务指标筛选股票。

以下是实现数据预处理的关键步骤：

首先，我们需要查询财务数据。所有可用的指标都在官方帮助文档中，使用前请仔细阅读。我们的目标是查询公司的营业收入。

```python
def before_trading_start(context):
    # 导入必要的查询模块
    from jqdata import get_fundamentals

    # 构建查询语句
    query = query(
        # 指定要查询的指标：营业收入
        indicator.operating_revenue
    ).filter(
        # 过滤条件：股票代码必须在我们的股票池（沪深300）中
        indicator.code.in_(get_index_stocks('沪深300'))
    ).order_by(
        # 排序：按营业收入降序排列（从高到低）
        indicator.operating_revenue.desc()
    ).limit(
        # 限制结果数量：只取前10名
        10
    )

    # 执行查询，结果是一个DataFrame
    context.filtered_stocks = get_fundamentals(query)
    # 打印结果以供检查
    print(context.filtered_stocks)
```

![](img/666f195dab951ee036444b3c790d5fb8_17.png)

![](img/666f195dab951ee036444b3c790d5fb8_19.png)

## 代码调试与问题解决
运行上述代码后，如果发现查询结果为空，可能是股票池指定有误。

![](img/666f195dab951ee036444b3c790d5fb8_21.png)

![](img/666f195dab951ee036444b3c790d5fb8_23.png)

![](img/666f195dab951ee036444b3c790d5fb8_25.png)

问题在于，我们最初可能错误地在股票列表中寻找“沪深300”，而“沪深300”本身是一个指数。正确的做法是使用 `get_index_stocks` 函数来获取该指数包含的成分股列表。

![](img/666f195dab951ee036444b3c790d5fb8_27.png)

修正后的过滤条件如下：
```python
.filter(
    indicator.code.in_(get_index_stocks('沪深300'))
)
```
修正后重新运行回测，即可在日志中看到每天筛选出的前10只股票及其营业收入数据。

![](img/666f195dab951ee036444b3c790d5fb8_29.png)

![](img/666f195dab951ee036444b3c790d5fb8_31.png)

![](img/666f195dab951ee036444b3c790d5fb8_33.png)

## 总结
本节课中我们一起学习了股票池筛选的基本流程：
1.  **初始化设定**：在 `initialize` 函数中定义初始股票池（如沪深300指数）。
2.  **数据查询**：在 `before_trading_start` 函数中，使用 `get_fundamentals` 和 `query` 对象查询所需的财务指标（如营业收入）。
3.  **条件过滤**：使用 `.filter()` 确保只处理股票池内的股票。
4.  **结果排序与限制**：使用 `.order_by().desc()` 进行降序排序，并用 `.limit()` 限制输出股票数量。
5.  **调试与修正**：若结果异常，需检查数据源和过滤逻辑是否正确，例如确保从指数获取成分股列表。

![](img/666f195dab951ee036444b3c790d5fb8_35.png)

![](img/666f195dab951ee036444b3c790d5fb8_37.png)

通过以上步骤，我们成功构建了一个简单的股票筛选器，为后续的选股和交易策略打下了数据基础。