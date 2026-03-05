# Python金融分析与量化交易实战教程：P24：03-2-股票池筛选 🎯

## 概述
在本节课程中，我们将学习如何在量化交易策略中筛选股票池。我们将使用沪深300指数作为基础股票池，并基于财务指标（如营业收入）对股票进行排序和筛选，最终选出排名靠前的股票进行后续分析。

![](img/51a686536e268dcb3811c65e9f2629df_1.png)

![](img/51a686536e268dcb3811c65e9f2629df_3.png)

## 股票池的初始化与配置

![](img/51a686536e268dcb3811c65e9f2629df_5.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何具体配置和筛选股票池。

在策略的构造函数中，我们需要定义初始的股票池。这里我们选择沪深300指数作为基础池。

![](img/51a686536e268dcb3811c65e9f2629df_7.png)

```python
def initialize(context):
    # 设定股票池为沪深300指数成分股
    context.stock_pool = '沪深300'
```

![](img/51a686536e268dcb3811c65e9f2629df_9.png)

![](img/51a686536e268dcb3811c65e9f2629df_11.png)

## 数据预处理与指标查询

![](img/51a686536e268dcb3811c65e9f2629df_13.png)

![](img/51a686536e268dcb3811c65e9f2629df_15.png)

![](img/51a686536e268dcb3811c65e9f2629df_17.png)

配置好股票池后，我们需要在每日交易开始前进行数据预处理，即根据财务指标筛选股票。

以下是数据预处理的核心步骤：

1.  **查询财务指标**：使用平台提供的API查询所需的财务数据。
2.  **应用过滤条件**：确保只处理股票池内的股票。
3.  **排序与限制**：根据指标对股票排序，并选取排名靠前的若干只。

### 查询函数的应用
所有可用的查询指标和函数都在官方帮助文档中。对于财务数据，我们使用 `query_fundamentals` 函数。

该函数的核心参数是一个 `query` 对象，用于指定查询内容和过滤条件。

```python
def before_trading_start(context):
    # 构建查询对象
    q = query(
        fundamentals.financial_indicator.operating_revenue # 查询营业收入指标
    ).filter(
        fundamentals.eod_derivative_indicator.code.in_(context.stock_pool) # 过滤条件：股票代码在沪深300池中
    ).order_by(
        fundamentals.financial_indicator.operating_revenue.desc() # 按营业收入降序排列
    ).limit(10) # 限制只取前10名

    # 执行查询，结果是一个DataFrame
    context.fundamental_df = get_fundamentals(q)
    print(context.fundamental_df)
```

![](img/51a686536e268dcb3811c65e9f2629df_19.png)

### 关键问题排查
在初次运行时，查询结果可能为空。这通常是因为股票池的指定方式不正确。

![](img/51a686536e268dcb3811c65e9f2629df_21.png)

![](img/51a686536e268dcb3811c65e9f2629df_23.png)

![](img/51a686536e268dcb3811c65e9f2629df_25.png)

**错误示例**：直接使用字符串 `'沪深300'` 作为过滤条件，系统无法识别。
**正确方法**：需要通过专门的指数成分股查询函数来获取沪深300的具体股票代码列表。

![](img/51a686536e268dcb3811c65e9f2629df_27.png)

修正后的关键代码如下：

![](img/51a686536e268dcb3811c65e9f2629df_29.png)

```python
# 正确获取指数成分股列表
context.stock_pool = get_index_stocks('沪深300')
```

![](img/51a686536e268dcb3811c65e9f2629df_31.png)

![](img/51a686536e268dcb3811c65e9f2629df_33.png)

修正后，`before_trading_start` 函数将在每个交易日开盘前，打印出当天根据营业收入排名前10的沪深300成分股信息。

![](img/51a686536e268dcb3811c65e9f2629df_35.png)

![](img/51a686536e268dcb3811c65e9f2629df_37.png)

## 总结
本节课中我们一起学习了量化策略中股票池筛选的完整流程：
1.  在 `initialize` 函数中初始化股票池。
2.  在 `before_trading_start` 函数中，使用 `query` 和 `get_fundamentals` 方法，结合过滤(`filter`)、排序(`order_by`)和限制(`limit`)操作，基于财务指标动态筛选出目标股票。
3.  掌握了排查“查询结果为空”这类常见问题的方法，即确保用于过滤的股票代码列表是有效的。

![](img/51a686536e268dcb3811c65e9f2629df_39.png)

通过本节的学习，你已经掌握了构建一个简单但核心的股票筛选模块的方法，这是进行后续量化分析和交易决策的基础。