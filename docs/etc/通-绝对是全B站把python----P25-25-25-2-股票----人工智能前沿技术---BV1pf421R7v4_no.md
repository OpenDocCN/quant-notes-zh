# Python金融量化与股票交易：P25：股票池筛选

![](img/c24629fe89c54e76ea8108555f7db19b_1.png)

在本节课中，我们将学习如何构建一个股票池筛选策略。我们将从沪深300指数中，根据财务指标（如营业收入）筛选出表现最佳的股票，为后续的交易决策做准备。

![](img/c24629fe89c54e76ea8108555f7db19b_3.png)

## 策略初始化

上一节我们介绍了策略的基本框架，本节中我们来看看如何初始化我们的股票池。

![](img/c24629fe89c54e76ea8108555f7db19b_5.png)

在策略的构造函数中，我们需要定义我们感兴趣的股票池。这里我们选择沪深300指数作为我们的初始股票池。

![](img/c24629fe89c54e76ea8108555f7db19b_7.png)

![](img/c24629fe89c54e76ea8108555f7db19b_9.png)

```python
def initialize(context):
    # 设定股票池为沪深300指数成分股
    context.stock_pool = index_components('沪深300')
```

![](img/c24629fe89c54e76ea8108555f7db19b_11.png)

## 数据预处理与筛选

初始化股票池后，我们需要在每日交易开始前进行数据预处理和筛选。这包括查询财务数据并应用过滤和排序条件。

以下是实现股票筛选的关键步骤：

1.  **构建查询**：使用 `query` 函数指定要查询的财务指标，例如营业收入。
2.  **应用过滤器**：使用 `filter` 条件确保我们只处理股票池内的股票。
3.  **排序结果**：使用 `order_by` 对查询结果进行排序，例如按营业收入降序排列。
4.  **限制数量**：使用 `limit` 函数限制最终入选的股票数量，例如只选择前10名。

```python
def before_trading_start(context):
    # 构建财务数据查询
    q = query(
        fundamentals.financial_indicator.operating_revenue # 查询营业收入指标
    ).filter(
        fundamentals.eod_derivative_indicator.code.in_(context.stock_pool) # 过滤：股票代码在股票池内
    ).order_by(
        fundamentals.financial_indicator.operating_revenue.desc() # 排序：按营业收入降序
    ).limit(10) # 限制：只取前10个结果

    # 执行查询，并将结果存入context中供后续使用
    context.filtered_stocks = get_fundamentals(q)
    # 打印结果以供检查
    print(context.filtered_stocks)
```

![](img/c24629fe89c54e76ea8108555f7db19b_13.png)

![](img/c24629fe89c54e76ea8108555f7db19b_15.png)

## 策略执行与调试

![](img/c24629fe89c54e76ea8108555f7db19b_17.png)

![](img/c24629fe89c54e76ea8108555f7db19b_19.png)

编写完筛选逻辑后，我们需要运行回测来验证代码是否正确工作，并检查输出结果是否符合预期。

![](img/c24629fe89c54e76ea8108555f7db19b_21.png)

如果运行后发现没有打印出筛选结果，问题可能出在股票池的定义上。确保使用正确的函数（如 `index_components`）来获取指数成分股列表。

![](img/c24629fe89c54e76ea8108555f7db19b_23.png)

修正后重新运行，即可在日志中看到每日筛选出的股票列表。这个列表是一个 `DataFrame`，横向是股票代码，纵向是财务数据。

![](img/c24629fe89c54e76ea8108555f7db19b_25.png)

![](img/c24629fe89c54e76ea8108555f7db19b_27.png)

## 总结

![](img/c24629fe89c54e76ea8108555f7db19b_29.png)

![](img/c24629fe89c54e76ea8108555f7db19b_31.png)

本节课中我们一起学习了股票池筛选的基本流程。我们首先在 `initialize` 函数中定义了初始股票池（沪深300），然后在 `before_trading_start` 函数中，利用 `query`、`filter`、`order_by` 和 `limit` 方法，根据财务指标（营业收入）对股票池进行筛选和排序，最终得到我们想要的重点关注股票列表。这个过程是量化策略中数据预处理和阿尔法因子构建的关键一步。