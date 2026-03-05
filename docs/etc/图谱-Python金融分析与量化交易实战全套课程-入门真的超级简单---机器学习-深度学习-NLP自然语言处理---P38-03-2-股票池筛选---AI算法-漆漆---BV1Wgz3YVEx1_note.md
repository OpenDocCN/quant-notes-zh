# 量化交易实战：P38：03-2-股票池筛选

在本节课中，我们将学习如何利用财务数据指标，从预设的股票池（如沪深300指数）中筛选出符合特定条件的股票，并构建一个精简的股票组合。这是量化策略中构建股票池的关键步骤。

![](img/b4f92b78273fe75ae8209b41b88d939f_1.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何实现一个具体的股票筛选逻辑。

![](img/b4f92b78273fe75ae8209b41b88d939f_3.png)

## 初始化策略与股票池

![](img/b4f92b78273fe75ae8209b41b88d939f_5.png)

首先，在策略的构造函数中，我们需要定义我们的初始股票池。这里我们选择沪深300指数作为我们的备选股票池。

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    context.stock_pool = ['000300.XSHG']  # 沪深300指数代码
    # 后续可以调整起始资金、回测频率等参数，此处暂不调整
```

![](img/b4f92b78273fe75ae8209b41b88d939f_7.png)

![](img/b4f92b78273fe75ae8209b41b88d939f_9.png)

![](img/b4f92b78273fe75ae8209b41b88d939f_11.png)

## 数据预处理与指标查询

![](img/b4f92b78273fe75ae8209b41b88d939f_13.png)

![](img/b4f92b78273fe75ae8209b41b88d939f_15.png)

在每日交易开始前，我们需要对股票池进行预处理，即根据财务指标进行筛选。这本质上是一个数据挖掘过程，我们需要从海量数据中提取有价值的指标。

以下是实现这一过程的关键步骤：

1.  **查询财务指标**：我们使用平台提供的 `query` 函数来获取所需的财务数据，例如营业收入。
2.  **应用过滤条件**：通过 `filter` 参数，限定只查询属于我们股票池（沪深300）的股票。
3.  **排序与限制**：对查询结果按指标（如营业收入）进行排序，并只保留排名靠前的若干只股票。

```python
def before_trading_start(context):
    # 构建查询，获取营业收入指标
    q = query(
        fundamentals.financial_indicator.operating_revenue  # 查询营业收入
    ).filter(
        fundamentals.eod_derivative_indicator.code.in_(context.stock_pool)  # 过滤条件：股票代码在沪深300池中
    ).order_by(
        fundamentals.financial_indicator.operating_revenue.desc()  # 按营业收入降序排列
    ).limit(10)  # 只取前10名

    # 执行查询，将结果（DataFrame格式）存入context中供后续使用
    context.filtered_stocks = get_fundamentals(q)
    # 打印结果以供检查
    print(context.filtered_stocks)
```

## 代码调试与问题解决

在初次运行回测时，打印出的结果可能为空。这通常是因为股票池的定义不正确。我们需要确保 `context.stock_pool` 中包含的是具体的股票代码，而不是指数代码。

![](img/b4f92b78273fe75ae8209b41b88d939f_17.png)

![](img/b4f92b78273fe75ae8209b41b88d939f_19.png)

**修正方法**：将股票池设置为沪深300指数的**成分股代码列表**，而非指数本身。可以通过平台的数据接口获取该列表。

![](img/b4f92b78273fe75ae8209b41b88d939f_21.png)

![](img/b4f92b78273fe75ae8209b41b88d939f_23.png)

```python
def initialize(context):
    # 获取沪深300指数在指定日期的成分股列表
    context.stock_pool = get_index_stocks('000300.XSHG')
```

![](img/b4f92b78273fe75ae8209b41b88d939f_25.png)

修正后重新运行回测，即可在日志中看到每天筛选出的前10只股票信息，包括股票代码和对应的营业收入数据。

![](img/b4f92b78273fe75ae8209b41b88d939f_27.png)

## 策略执行与后续步骤

![](img/b4f92b78273fe75ae8209b41b88d939f_29.png)

![](img/b4f92b78273fe75ae8209b41b88d939f_31.png)

至此，我们成功实现了股票筛选功能。`context.filtered_stocks` 中保存了每日根据营业收入排名选出的10只股票。

![](img/b4f92b78273fe75ae8209b41b88d939f_33.png)

![](img/b4f92b78273fe75ae8209b41b88d939f_35.png)

在后续的 `handle_data` 函数（每日盘中执行）中，我们就可以基于这个动态更新的股票池来进行具体的买卖决策，例如等权重买入这10只股票。

![](img/b4f92b78273fe75ae8209b41b88d939f_37.png)

本节课中我们一起学习了量化策略中股票池筛选的核心流程：从定义初始池，到查询财务数据、应用过滤和排序规则，最终得到目标股票组合。这个过程是构建任何基于基本面或技术面指标的量化策略的基础。