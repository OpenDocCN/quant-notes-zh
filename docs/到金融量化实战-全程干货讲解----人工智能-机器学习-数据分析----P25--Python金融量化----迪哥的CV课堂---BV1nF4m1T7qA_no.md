# Python金融量化：25：股票池筛选教程 📈

在本节课中，我们将学习如何构建一个股票池筛选策略。我们将从设定初始股票池开始，逐步学习如何查询财务数据、应用过滤条件、进行排序，并最终筛选出目标股票。整个过程将使用具体的代码示例进行演示，确保初学者能够理解并应用。

## 概述

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_1.png)

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_3.png)

股票池筛选是量化交易中的基础环节，其核心在于从众多股票中，依据特定规则（如财务指标）筛选出符合策略要求的股票组合。本节我们将通过一个具体实例，演示如何基于财务营收数据，从沪深300指数成分股中筛选出营收排名前十的股票。

## 策略构建步骤

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何实现一个具体的股票筛选逻辑。

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_5.png)

### 1. 初始化股票池

首先，在策略的构造函数中，我们需要定义初始的股票池。这里我们选择沪深300指数作为我们的初始股票池。

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_7.png)

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    context.stock_pool = index_components('000300.XSHG')
```

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_9.png)

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_11.png)

### 2. 数据预处理与查询

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_13.png)

在每日交易开始前（`before_trading`函数中），我们需要对股票池进行筛选。这涉及到查询财务数据。

以下是查询财务数据的关键步骤介绍：

*   **使用`query`函数**：该函数用于指定需要查询的财务指标。所有可用的指标均可在平台的帮助文档中查找。
*   **查阅帮助文档**：要查询“营业总收入”指标，我们需要在财务数据文档中找到对应的字段名称。
*   **构建查询语句**：在`query`对象中填入我们想要查询的指标字段。

```python
def before_trading(context):
    # 构建查询，获取基础财务数据中的“营业总收入”指标
    q = query(
        fundamentals.financial_indicator.operating_revenue
    )
```

### 3. 应用过滤与排序条件

仅有查询语句还不够，我们需要将其限定在特定的股票池内，并对结果进行排序和数量限制。

以下是应用过滤与排序条件的步骤：

*   **使用`filter`方法**：此方法用于过滤数据。我们将条件设置为：只选择股票代码在我们初始股票池（沪深300）中的股票。
*   **使用`order_by`方法**：此方法用于对结果进行排序。我们按照“营业总收入”进行降序排列，以选出营收最高的股票。
*   **使用`limit`方法**：此方法用于限制返回结果的数量。我们只取排名前十的股票。

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_15.png)

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_17.png)

将以上所有条件整合到查询中，代码如下：

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_19.png)

```python
def before_trading(context):
    # 完整的查询、过滤、排序与限制语句
    df = get_fundamentals(
        query(
            fundamentals.financial_indicator.operating_revenue
        ).filter(
            fundamentals.financial_indicator.code.in_(context.stock_pool)
        ).order_by(
            fundamentals.financial_indicator.operating_revenue.desc()
        ).limit(10)
    )
    # 将筛选结果存入context，供后续交易函数使用
    context.filtered_stocks = df
    # 打印结果进行验证
    print(df)
```

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_21.png)

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_23.png)

### 4. 运行与调试

编写完代码后，需要运行回测以验证筛选逻辑是否正确。

初次运行时，打印结果可能为空。这通常是因为股票池设置不正确。我们需要确保在获取指数成分股时使用了正确的函数和指数代码。

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_25.png)

**修正方法**：使用`index_components`函数并传入正确的沪深300指数代码（例如`‘000300.XSHG’`）。

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_27.png)

修正后再次运行，即可在日志中看到每天筛选出的前十只股票及其营收数据。

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_29.png)

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_31.png)

## 总结

![](img/36b02fc1dd6d48a7be2517cc8b5e8e6a_33.png)

本节课中我们一起学习了股票池筛选的完整流程。我们首先设定了初始股票池（沪深300），然后通过`get_fundamentals`和`query`函数查询财务指标，并利用`filter`、`order_by`和`limit`方法对结果进行过滤、排序和筛选，最终得到了我们想要的股票列表。这个过程清晰地展示了如何将数据查询、处理与策略规则相结合，是构建量化交易策略的重要基础。