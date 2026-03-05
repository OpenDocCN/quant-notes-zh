# 人工智能金融量化交易：P48：02-2-过滤筛选因子指标数据

![](img/e1c5bb35214750c694cb2811ea0df0fd_1.png)

## 概述

![](img/e1c5bb35214750c694cb2811ea0df0fd_3.png)

在本节课程中，我们将学习如何为量化交易策略准备数据。核心任务是编写代码，从股票池中过滤掉不符合条件的股票，并查询我们所需的财务指标数据。我们将使用Python和特定的金融数据API来完成这些操作。

![](img/e1c5bb35214750c694cb2811ea0df0fd_5.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_7.png)

## 过滤不想要的股票

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何构建一个干净的股票池。在构建因子策略时，我们首先需要剔除那些不适合交易的股票，例如停牌股、ST股和刚上市的新股。

![](img/e1c5bb35214750c694cb2811ea0df0fd_9.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_11.png)

以下是实现股票过滤的三个核心函数：

![](img/e1c5bb35214750c694cb2811ea0df0fd_13.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_15.png)

1.  **过滤停牌股**：此函数遍历股票列表，使用API判断股票是否全天停牌，并保留未停牌的股票。
    ```python
    def filter_paused_stock(stock_list):
        current_stocks = []
        for stock in stock_list:
            if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API
                current_stocks.append(stock)
        return current_stocks
    ```

![](img/e1c5bb35214750c694cb2811ea0df0fd_17.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_19.png)

2.  **过滤ST股**：此函数遍历股票列表，使用API判断股票是否为ST或*ST股，并保留非ST股。
    ```python
    def filter_st_stock(stock_list):
        current_stocks = []
        for stock in stock_list:
            if not is_st_stock(stock):  # 假设 is_st_stock 是判断ST股的API
                current_stocks.append(stock)
        return current_stocks
    ```

![](img/e1c5bb35214750c694cb2811ea0df0fd_21.png)

3.  **过滤新股**：此函数通过判断股票上市天数是否超过阈值（例如180天）来过滤掉上市时间过短的新股。
    ```python
    def filter_new_stock(stock_list):
        current_stocks = []
        for stock in stock_list:
            if get_listed_days(stock) > 180:  # 假设 get_listed_days 是获取上市天数的API
                current_stocks.append(stock)
        return current_stocks
    ```

![](img/e1c5bb35214750c694cb2811ea0df0fd_23.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_25.png)

## 应用过滤函数

![](img/e1c5bb35214750c694cb2811ea0df0fd_27.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_29.png)

在定义了过滤函数后，我们需要将它们依次应用到初始的股票列表上。

![](img/e1c5bb35214750c694cb2811ea0df0fd_31.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_33.png)

首先，我们获得一个初始的股票代码列表。然后，按顺序执行过滤：先过滤停牌股，再过滤ST股，最后过滤新股。每一步过滤的结果都作为下一步的输入。

```python
# 假设 all_stocks 是初始的股票代码列表
filtered_stocks = filter_paused_stock(all_stocks)
filtered_stocks = filter_st_stock(filtered_stocks)
filtered_stocks = filter_new_stock(filtered_stocks)
```

![](img/e1c5bb35214750c694cb2811ea0df0fd_35.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_37.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_39.png)

经过以上步骤，`filtered_stocks` 中剩下的就是符合我们基本交易条件的股票池。

![](img/e1c5bb35214750c694cb2811ea0df0fd_41.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_43.png)

## 查询所需的财务指标

在获得干净的股票池后，下一步是获取用于因子计算的财务指标数据。本节策略中，我们关注**市净率（PB）**和**市值（Market Cap）**这两个指标。

![](img/e1c5bb35214750c694cb2811ea0df0fd_45.png)

我们使用 `get_fundamentals` API来查询数据。需要构建一个查询语句（query），指定要查询的指标和股票范围。

![](img/e1c5bb35214750c694cb2811ea0df0fd_47.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_49.png)

以下是查询代码示例：

![](img/e1c5bb35214750c694cb2811ea0df0fd_51.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_53.png)

```python
# 构建查询，获取市净率(pb_ratio)和市值(market_cap)
q = query(
    fundamentals.eod_derivative_indicator.pb_ratio,
    fundamentals.eod_derivative_indicator.market_cap
).filter(
    fundamentals.income_statement.stockcode.in_(filtered_stocks)  # 仅查询过滤后的股票
)

![](img/e1c5bb35214750c694cb2811ea0df0fd_55.png)

# 执行查询
fundamental_data = get_fundamentals(q)
```

![](img/e1c5bb35214750c694cb2811ea0df0fd_57.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_59.png)

## 数据整理与转置

![](img/e1c5bb35214750c694cb2811ea0df0fd_61.png)

查询返回的数据格式通常是以指标为行、股票为列的DataFrame。然而，在后续分析中，我们更常用的格式是以股票为行、指标为列。

![](img/e1c5bb35214750c694cb2811ea0df0fd_63.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_65.png)

因此，我们需要对数据进行转置操作。同时，数据中可能存在空值（NaN），为了简化处理，我们可以选择直接删除包含空值的行。

![](img/e1c5bb35214750c694cb2811ea0df0fd_67.png)

```python
# 转置数据，使股票成为行，指标成为列
transposed_data = fundamental_data.T

![](img/e1c5bb35214750c694cb2811ea0df0fd_69.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_71.png)

# 删除包含任何空值的行（简化处理）
cleaned_data = transposed_data.dropna()
```

经过转置和清理，`cleaned_data` 就是一个结构清晰、可供下一步因子计算和策略回测使用的数据集。

![](img/e1c5bb35214750c694cb2811ea0df0fd_73.png)

## 总结

![](img/e1c5bb35214750c694cb2811ea0df0fd_75.png)

![](img/e1c5bb35214750c694cb2811ea0df0fd_77.png)

本节课中我们一起学习了量化交易数据准备的关键步骤。我们首先编写了三个过滤函数，用于从股票池中剔除停牌股、ST股和新股，从而构建一个高质量的候选股票池。接着，我们使用 `get_fundamentals` API 查询了所需的财务指标（市净率和市值）。最后，我们对查询结果进行了数据格式的转置和空值清理，得到了符合分析要求的数据集。这些步骤是构建任何量化交易策略不可或缺的数据基础工作。