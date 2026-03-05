# Python金融量化分析：P25：股票池筛选 📊

在本节课中，我们将学习如何构建一个股票池筛选策略。我们将从沪深300指数成分股中，根据财务指标（如营业收入）筛选出表现最佳的股票，为后续的交易决策做准备。

![](img/20d755883d53ea1b15bd17f907e1ebf2_1.png)

![](img/20d755883d53ea1b15bd17f907e1ebf2_3.png)

## 策略初始化与股票池设定

![](img/20d755883d53ea1b15bd17f907e1ebf2_5.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何设定初始的股票池。

![](img/20d755883d53ea1b15bd17f907e1ebf2_7.png)

在策略的构造函数中，我们需要定义我们的初始股票池。这里我们选择沪深300指数作为我们的股票池。你可以使用指数名称或代码来指定。

![](img/20d755883d53ea1b15bd17f907e1ebf2_9.png)

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    set_universe('沪深300')
```

![](img/20d755883d53ea1b15bd17f907e1ebf2_11.png)

## 数据预处理与指标查询

![](img/20d755883d53ea1b15bd17f907e1ebf2_13.png)

![](img/20d755883d53ea1b15bd17f907e1ebf2_15.png)

![](img/20d755883d53ea1b15bd17f907e1ebf2_17.png)

设定好股票池后，我们需要在每日交易开始前进行数据预处理，即根据财务指标筛选股票。这涉及到查询特定财务数据。

![](img/20d755883d53ea1b15bd17f907e1ebf2_19.png)

量化交易的核心是数据挖掘，平台提供了丰富的财务和行情指标供我们查询。所有可用的指标都在官方帮助文档中有详细说明。

以下是查询财务数据的基本步骤：

1.  **构造查询（Query）**：使用 `query()` 函数，并指定要查询的指标字段。
2.  **设置过滤条件（Filter）**：限定查询范围，例如只查询我们股票池中的股票。
3.  **排序（Order）**：根据指标对结果进行排序（升序或降序）。
4.  **限制数量（Limit）**：只保留排名靠前的若干只股票。

我们将查询“营业总收入”这个财务指标，并据此进行筛选。

```python
def before_trading_start(context, data):
    # 构造查询，获取基础财务数据中的“营业总收入”指标
    fundamental_df = get_fundamentals(
        query(
            fundamentals.eod_derivative_indicator.operating_revenue # 营业总收入指标
        )
        .filter(
            fundamentals.eod_derivative_indicator.code.in_(get_index_stocks('沪深300')) # 过滤：仅限沪深300成分股
        )
        .order_by(
            fundamentals.eod_derivative_indicator.operating_revenue.desc() # 排序：按营收降序排列
        )
        .limit(10) # 限制：只取前10名
    )
    
    # 将筛选结果存入context，供后续函数使用
    context.fundamental_df = fundamental_df
    
    # 打印查看结果（实际运行时可以注释掉）
    print(fundamental_df)
```

## 代码调试与运行

![](img/20d755883d53ea1b15bd17f907e1ebf2_21.png)

编写完筛选逻辑后，我们需要运行回测来验证代码是否正确。

![](img/20d755883d53ea1b15bd17f907e1ebf2_23.png)

![](img/20d755883d53ea1b15bd17f907e1ebf2_25.png)

![](img/20d755883d53ea1b15bd17f907e1ebf2_27.png)

最初运行时，打印出的结果可能是空的。这是因为在过滤条件中，我们错误地在股票列表中查找指数代码。正确的做法是使用 `get_index_stocks(‘指数代码’)` 函数来获取指数对应的成分股列表。

![](img/20d755883d53ea1b15bd17f907e1ebf2_29.png)

![](img/20d755883d53ea1b15bd17f907e1ebf2_31.png)

修正后的过滤条件如下：
```python
.filter(fundamentals.eod_derivative_indicator.code.in_(get_index_stocks(‘000300.XSHG‘))) # 使用指数代码获取成分股
```
或者使用指数名称：
```python
.filter(fundamentals.eod_derivative_indicator.code.in_(get_index_stocks(‘沪深300‘)))
```

修正后再次运行回测，你将在日志中看到每天筛选出的前10只股票列表，数据格式通常是一个DataFrame，行是股票代码，列是指标值。

![](img/20d755883d53ea1b15bd17f907e1ebf2_33.png)

## 总结

![](img/20d755883d53ea1b15bd17f907e1ebf2_35.png)

![](img/20d755883d53ea1b15bd17f907e1ebf2_37.png)

本节课中我们一起学习了股票池筛选的基本流程。

![](img/20d755883d53ea1b15bd17f907e1ebf2_39.png)

![](img/20d755883d53ea1b15bd17f907e1ebf2_41.png)

1.  **初始化股票池**：在 `initialize` 函数中通过 `set_universe` 设定初始范围。
2.  **查询与筛选**：在 `before_trading_start` 函数中，使用 `get_fundamentals` 和 `query` 对象，结合 `filter`、`order_by` 和 `limit` 方法，根据财务指标从股票池中筛选出目标股票。
3.  **存储结果**：将筛选结果存入 `context` 对象，以便在交易函数中调用。

![](img/20d755883d53ea1b15bd17f907e1ebf2_43.png)

![](img/20d755883d53ea1b15bd17f907e1ebf2_45.png)

通过这个简单的例子，你已经掌握了量化策略中数据预处理和股票初选的关键步骤。在实际应用中，你可以结合多个指标（如市盈率、净利润增长率等）构建更复杂的筛选模型。