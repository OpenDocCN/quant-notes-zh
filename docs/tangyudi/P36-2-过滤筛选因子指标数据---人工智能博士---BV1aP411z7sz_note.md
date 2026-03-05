# 量化投资策略：P36：2-过滤筛选因子指标数据 📊

![](img/6496d8d1cc68ec60bb31c0604bf8e372_1.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的财务指标数据。这是构建量化因子策略的关键一步，目的是剔除不符合交易条件的股票，并准备好用于后续分析的数据。

![](img/6496d8d1cc68ec60bb31c0604bf8e372_3.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_5.png)

上一节我们介绍了如何获取初始的股票池，本节中我们来看看如何对这些股票进行过滤，并提取关键的财务指标。

## 过滤不符合条件的股票 🚫

![](img/6496d8d1cc68ec60bb31c0604bf8e372_7.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_9.png)

在构建股票池时，我们需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一目标的三个核心过滤函数。

![](img/6496d8d1cc68ec60bb31c0604bf8e372_11.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_13.png)

### 1. 过滤停牌股票

![](img/6496d8d1cc68ec60bb31c0604bf8e372_15.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_17.png)

首先，我们需要过滤掉全天停牌的股票。我们使用 `is_suspended` 函数来判断一只股票是否停牌。

![](img/6496d8d1cc68ec60bb31c0604bf8e372_19.png)

```python
def filter_suspended(stock_list):
    """
    过滤掉停牌的股票。
    :param stock_list: 初始股票列表
    :return: 过滤后的股票列表
    """
    filtered_stocks = []
    for stock in stock_list:
        if not is_suspended(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/6496d8d1cc68ec60bb31c0604bf8e372_21.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_23.png)

### 2. 过滤ST股票

![](img/6496d8d1cc68ec60bb31c0604bf8e372_25.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_27.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_29.png)

接下来，我们需要过滤掉被标记为ST（特别处理）的股票。我们使用 `is_st_stock` 函数进行判断。

![](img/6496d8d1cc68ec60bb31c0604bf8e372_31.png)

```python
def filter_st(stock_list):
    """
    过滤掉ST股票。
    :param stock_list: 经过停牌过滤后的股票列表
    :return: 过滤后的股票列表
    """
    filtered_stocks = []
    for stock in stock_list:
        if not is_st_stock(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/6496d8d1cc68ec60bb31c0604bf8e372_33.png)

### 3. 过滤新股

![](img/6496d8d1cc68ec60bb31c0604bf8e372_35.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_37.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_39.png)

最后，我们过滤掉上市时间过短的新股。这里我们假设需要股票上市超过180天。我们使用 `get_days_from_listed` 函数获取上市天数。

![](img/6496d8d1cc68ec60bb31c0604bf8e372_41.png)

```python
def filter_new(stock_list):
    """
    过滤掉上市天数少于180天的新股。
    :param stock_list: 经过ST过滤后的股票列表
    :return: 过滤后的股票列表
    """
    filtered_stocks = []
    for stock in stock_list:
        if get_days_from_listed(stock) > 180:
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/6496d8d1cc68ec60bb31c0604bf8e372_43.png)

完成以上三个过滤函数的定义后，我们可以将它们串联起来，对初始股票池进行逐步筛选。

![](img/6496d8d1cc68ec60bb31c0604bf8e372_45.png)

```python
# 假设 initial_stocks 是初始获取的所有股票列表
filtered_by_suspend = filter_suspended(initial_stocks)
filtered_by_st = filter_st(filtered_by_suspend)
final_filtered_stocks = filter_new(filtered_by_st)
```

![](img/6496d8d1cc68ec60bb31c0604bf8e372_47.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_49.png)

## 查询所需的财务指标 📈

![](img/6496d8d1cc68ec60bb31c0604bf8e372_51.png)

过滤出合格的股票池后，下一步是获取这些股票的财务指标数据。在本策略中，我们关注**市净率（PB）**和**市值（Market Cap）**这两个因子。

![](img/6496d8d1cc68ec60bb31c0604bf8e372_53.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_55.png)

我们使用 `get_fundamentals` 函数来查询数据。该函数需要传入一个查询语句（query）和过滤条件。

![](img/6496d8d1cc68ec60bb31c0604bf8e372_57.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_59.png)

以下是查询代码：

![](img/6496d8d1cc68ec60bb31c0604bf8e372_61.png)

```python
# 构建查询语句，查询市净率(pb_ratio)和市值(market_cap)
query = query(
    fundamentals.eod_derivative_indicator.pb_ratio,
    fundamentals.eod_derivative_indicator.market_cap
)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_63.png)

# 执行查询，仅查询我们最终过滤出的股票池中的股票
fundamental_data = get_fundamentals(query, filter=final_filtered_stocks)
```

![](img/6496d8d1cc68ec60bb31c0604bf8e372_65.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_67.png)

查询返回的数据 `fundamental_data` 通常是一个 `DataFrame`，其索引是股票代码，列是我们查询的指标。但有时数据的排列方式（横表）可能不便于后续分析，我们通常需要将其转换为每行代表一只股票的格式（纵表），这可以通过转置操作 `.T` 来实现。同时，我们可能需要处理缺失值。

![](img/6496d8d1cc68ec60bb31c0604bf8e372_69.png)

```python
# 转置数据，使每行代表一只股票
transposed_data = fundamental_data.T

![](img/6496d8d1cc68ec60bb31c0604bf8e372_71.png)

# 删除包含空值的行（简单处理）
cleaned_data = transposed_data.dropna()
```

![](img/6496d8d1cc68ec60bb31c0604bf8e372_73.png)

![](img/6496d8d1cc68ec60bb31c0604bf8e372_75.png)

本节课中我们一起学习了如何对股票池进行三步关键过滤（停牌、ST、新股），以及如何使用 `get_fundamentals` API 查询所需的财务指标数据（市净率和市值），并对数据进行了初步清洗（转置和删除空值）。这些步骤为我们后续的因子计算和选股策略奠定了坚实的数据基础。