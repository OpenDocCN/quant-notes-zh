# Python金融分析与量化交易实战课程：P22：股票池筛选 📊

![](img/226f0b870b07b3f38ee963bad794f635_1.png)

![](img/226f0b870b07b3f38ee963bad794f635_3.png)

## 概述
在本节课中，我们将学习如何构建一个股票池筛选策略。我们将使用财务指标（如营业收入）作为筛选标准，从沪深300指数成分股中，每日筛选出表现最佳的10只股票。这个过程是量化交易中数据挖掘和策略构建的核心环节。

![](img/226f0b870b07b3f38ee963bad794f635_5.png)

## 策略构建步骤

![](img/226f0b870b07b3f38ee963bad794f635_7.png)

上一节我们介绍了量化交易的基本框架，本节中我们来看看如何实现一个具体的股票筛选策略。

![](img/226f0b870b07b3f38ee963bad794f635_9.png)

![](img/226f0b870b07b3f38ee963bad794f635_11.png)

![](img/226f0b870b07b3f38ee963bad794f635_13.png)

### 1. 初始化股票池
首先，我们需要在策略的构造函数中定义我们的初始股票池。这里我们选择沪深300指数作为基础池。

![](img/226f0b870b07b3f38ee963bad794f635_15.png)

![](img/226f0b870b07b3f38ee963bad794f635_17.png)

```python
def initialize(context):
    # 设定股票池为沪深300指数成分股
    context.stock_pool = index_components('沪深300')
```

### 2. 编写预处理函数
在每日交易开始前，我们需要执行预处理操作，即根据财务指标筛选股票。我们将这个逻辑写在 `before_trading` 函数中。

以下是实现股票筛选的核心步骤：

1.  **查询财务指标**：使用平台提供的 `query` 功能，指定我们想要查询的指标，例如“营业总收入”。
2.  **应用过滤器**：通过 `filter` 条件，限定只查询我们股票池（沪深300）中的股票。
3.  **排序**：使用 `order_by` 对查询结果进行排序，例如按营业收入降序排列，以找到营收最高的公司。
4.  **限制数量**：使用 `limit` 限制最终输出的股票数量，例如只选择排名前10的股票。

![](img/226f0b870b07b3f38ee963bad794f635_19.png)

![](img/226f0b870b07b3f38ee963bad794f635_21.png)

```python
def before_trading(context):
    # 构建查询：获取营业总收入指标
    q = query(
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        fundamentals.financial_indicator.stockcode.in_(context.stock_pool)
    ).order_by(
        fundamentals.financial_indicator.operating_revenue.desc()
    ).limit(10)

    # 执行查询，并将结果（DataFrame格式）存入context中供后续使用
    context.filtered_stocks = get_fundamentals(q)
    # 打印查看结果
    print(context.filtered_stocks)
```

![](img/226f0b870b07b3f38ee963bad794f635_23.png)

![](img/226f0b870b07b3f38ee963bad794f635_25.png)

![](img/226f0b870b07b3f38ee963bad794f635_27.png)

### 3. 调试与验证
编写完代码后，运行回测以验证筛选逻辑是否正确。如果结果为空，需要检查股票池的代码是否正确。例如，确保 `index_components` 函数传入的是正确的指数名称。

![](img/226f0b870b07b3f38ee963bad794f635_29.png)

```python
# 正确的指数名称通常可以在平台的“指数”列表中找到
context.stock_pool = index_components('沪深300')
```

![](img/226f0b870b07b3f38ee963bad794f635_31.png)

![](img/226f0b870b07b3f38ee963bad794f635_33.png)

![](img/226f0b870b07b3f38ee963bad794f635_35.png)

运行成功后，日志会输出每日筛选出的股票列表，格式为一个DataFrame，行是股票代码，列是对应的财务数据。

![](img/226f0b870b07b3f38ee963bad794f635_37.png)

![](img/226f0b870b07b3f38ee963bad794f635_39.png)

## 总结
本节课中我们一起学习了量化交易中股票池筛选的基本方法。我们掌握了如何利用平台的 `query`、`filter`、`order_by` 和 `limit` 功能，基于财务指标从预设的股票池中动态筛选出目标股票。这是构建更复杂交易策略的重要基础，其核心思想是通过数据挖掘找到符合特定条件的投资标的。