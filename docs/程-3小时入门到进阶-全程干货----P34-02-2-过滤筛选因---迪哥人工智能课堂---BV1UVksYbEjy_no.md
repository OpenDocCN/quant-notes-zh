# Python金融分析与量化交易实战教程：P34：02-2-过滤筛选因子指标数据 📊

![](img/104373e4fbb00fe7bd6febd7729d8004_1.png)

在本节课中，我们将学习如何对股票池进行过滤筛选，并获取我们所需的因子指标数据。这是构建量化交易策略的关键一步，目的是剔除无效或不符合条件的股票，并提取用于分析的财务指标。

![](img/104373e4fbb00fe7bd6febd7729d8004_3.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_5.png)

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何具体实现数据的预处理。

![](img/104373e4fbb00fe7bd6febd7729d8004_7.png)

## 过滤不想要的股票 🚫

![](img/104373e4fbb00fe7bd6febd7729d8004_9.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_11.png)

在构建股票池时，我们首先需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一功能的步骤。

![](img/104373e4fbb00fe7bd6febd7729d8004_13.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_15.png)

首先，我们需要编写一个函数来过滤停牌的股票。这个函数接收一个股票列表作为输入，遍历列表中的每一只股票，判断其是否全天停牌。如果股票没有停牌，则将其保留在新的列表中。

![](img/104373e4fbb00fe7bd6febd7729d8004_17.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_19.png)

```python
def filter_suspended_stocks(stock_list):
    filtered_stocks = []
    for stock in stock_list:
        if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/104373e4fbb00fe7bd6febd7729d8004_21.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_23.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_25.png)

接下来，我们继续过滤ST股。逻辑与过滤停牌股类似，只是判断条件变为股票是否为ST股。

![](img/104373e4fbb00fe7bd6febd7729d8004_27.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_29.png)

```python
def filter_st_stocks(stock_list):
    filtered_stocks = []
    for stock in stock_list:
        if not is_st(stock):  # 假设 is_st 是判断ST股的API
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/104373e4fbb00fe7bd6febd7729d8004_31.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_33.png)

最后，我们需要过滤掉上市时间过短的新股。这里我们假设一个股票需要上市超过180天才符合我们的要求。

![](img/104373e4fbb00fe7bd6febd7729d8004_35.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_37.png)

```python
def filter_new_stocks(stock_list):
    filtered_stocks = []
    for stock in stock_list:
        if days_from_listed(stock) > 180:  # 假设 days_from_listed 返回上市天数
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/104373e4fbb00fe7bd6febd7729d8004_39.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_41.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_43.png)

现在，我们将这三个过滤步骤依次应用到初始的股票池中。

```python
# 假设 all_stocks 是初始的股票代码列表
filtered_stocks = filter_suspended_stocks(all_stocks)
filtered_stocks = filter_st_stocks(filtered_stocks)
filtered_stocks = filter_new_stocks(filtered_stocks)
```

![](img/104373e4fbb00fe7bd6febd7729d8004_45.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_47.png)

经过以上步骤，`filtered_stocks` 中剩下的就是经过初步筛选、符合我们基本交易条件的股票。

![](img/104373e4fbb00fe7bd6febd7729d8004_49.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_51.png)

## 查询所需的因子指标 🔍

![](img/104373e4fbb00fe7bd6febd7729d8004_53.png)

股票池准备好之后，下一步是获取我们策略需要用到的因子指标，例如市盈率（PE）和市值（Market Cap）。

![](img/104373e4fbb00fe7bd6febd7729d8004_55.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_57.png)

以下是查询这些指标的方法。我们使用一个假设的 `get_fundamentals` API，它允许我们通过类SQL的查询语句来获取财务数据。

![](img/104373e4fbb00fe7bd6febd7729d8004_59.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_61.png)

```python
# 构建查询语句，获取市盈率(pb_ratio)和市值(market_cap)
query = “””
    fundamentals.pb_ratio,
    fundamentals.market_cap
“””

![](img/104373e4fbb00fe7bd6febd7729d8004_63.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_65.png)

# 执行查询，并指定只查询我们过滤后股票池中的股票
factor_data = get_fundamentals(query, filter=in_(filtered_stocks))
```

![](img/104373e4fbb00fe7bd6febd7729d8004_67.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_69.png)

查询返回的数据通常是一个二维表格，其中行代表不同的股票，列代表不同的指标。然而，我们通常希望数据是“竖着”的格式，即每一行是一个指标在不同股票上的值。因此，我们需要对数据进行转置操作。

![](img/104373e4fbb00fe7bd6febd7729d8004_71.png)

```python
# 对数据进行转置，并处理可能存在的空值
factor_data_transposed = factor_data.T.dropna()
```

![](img/104373e4fbb00fe7bd6febd7729d8004_73.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_75.png)

`factor_data_transposed` 现在就是一个结构清晰、可供后续分析和建模使用的因子数据表了。

![](img/104373e4fbb00fe7bd6febd7729d8004_77.png)

![](img/104373e4fbb00fe7bd6febd7729d8004_79.png)

本节课中我们一起学习了量化交易中数据预处理的两个核心环节：股票池的过滤与因子指标的提取。我们通过编写过滤函数移除了停牌股、ST股和新股，然后使用查询API获取了市盈率和市值指标，并对数据格式进行了整理。这些步骤为后续的因子分析和策略构建打下了坚实的基础。