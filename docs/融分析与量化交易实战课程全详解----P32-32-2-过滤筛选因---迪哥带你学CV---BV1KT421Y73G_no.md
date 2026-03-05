# Python金融分析与量化交易实战课程：P32：32.2-过滤筛选因子指标数据 📊

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_1.png)

在本节课中，我们将学习如何对股票池进行过滤筛选，并获取所需的财务指标数据。这是构建量化交易策略中非常关键的一步，目的是剔除无效或不符合条件的股票，并提取用于后续分析的因子数据。

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现股票的筛选和指标查询。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_3.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_5.png)

## 过滤不想要的股票 🚫

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_7.png)

在构建股票池时，我们需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一功能的步骤。

首先，我们需要编写一个过滤函数。这个函数接收一个股票列表，并返回经过筛选后的列表。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_9.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_11.png)

```python
def filter_stocks(stock_list):
    # 此处将实现具体的过滤逻辑
    pass
```

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_13.png)

### 判断并过滤停牌股

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_15.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_17.png)

第一个过滤条件是剔除全年停牌的股票。我们可以使用API提供的 `is_suspended` 函数来判断。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_19.png)

```python
def filter_suspended(stock_list):
    filtered_list = []
    for stock in stock_list:
        if not is_suspended(stock):  # 判断是否停牌
            filtered_list.append(stock)
    return filtered_list
```

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_21.png)

### 判断并过滤ST股

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_23.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_25.png)

第二个过滤条件是剔除ST股（即被特别处理的股票）。我们可以使用 `is_st` 函数来判断。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_27.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_29.png)

```python
def filter_st(stock_list):
    filtered_list = []
    for stock in stock_list:
        if not is_st(stock):  # 判断是否为ST股
            filtered_list.append(stock)
    return filtered_list
```

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_31.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_33.png)

### 判断并过滤新股

第三个过滤条件是剔除上市时间过短的新股。虽然没有直接的“是否新股”API，但我们可以通过判断股票上市天数来实现。例如，我们只保留上市超过180天的股票。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_35.png)

```python
def filter_new(stock_list):
    filtered_list = []
    for stock in stock_list:
        if days_from_listed(stock) > 180:  # 判断上市天数是否大于180天
            filtered_list.append(stock)
    return filtered_list
```

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_37.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_39.png)

### 整合过滤流程

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_41.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_43.png)

将上述三个过滤函数依次应用，即可完成对股票池的清洗。

```python
# 假设 initial_stocks 是初始的股票列表
stocks_no_suspended = filter_suspended(initial_stocks)
stocks_no_st = filter_st(stocks_no_suspended)
final_stocks = filter_new(stocks_no_st)
```

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_45.png)

通过以上步骤，我们得到了一个经过初步筛选、相对“干净”的股票池 `final_stocks`。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_47.png)

## 查询所需的财务指标 📈

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_49.png)

在得到目标股票池后，下一步是获取这些股票的特定财务指标数据，例如市净率（PB）和市值（Market Cap）。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_51.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_53.png)

我们使用 `get_fundamentals` 函数来查询财务数据。该函数需要传入一个查询语句（query）来指定要获取的指标。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_55.png)

以下是查询市净率和市值的代码示例：

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_57.png)

```python
# 构建查询语句，查询 pb_ratio（市净率）和 market_cap（市值）
query = query(fundamentals.eod_derivative_indicator.pb_ratio,
              fundamentals.eod_derivative_indicator.market_cap)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_59.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_61.png)

# 执行查询，并过滤只查询我们最终股票池里的股票
df = get_fundamentals(query, filter=fundamentals.stockcode.in_(final_stocks))
```

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_63.png)

查询返回的数据 `df` 是一个 `DataFrame`，但其格式可能不是我们想要的。通常，数据是横向排列的（每行是一个指标，每列是一只股票），而我们需要的是纵向排列（每行是一只股票，每列是一个指标）。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_65.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_67.png)

因此，我们需要对数据进行转置操作。同时，处理可能存在的空值（NaN），这里我们选择直接删除包含空值的行。

```python
# 转置 DataFrame，使行代表股票，列代表指标
df = df.T

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_69.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_71.png)

# 删除包含任何空值的行
df = df.dropna()
```

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_73.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_75.png)

经过转置和清理，`df` 中的数据格式就变得清晰易用，每一行对应一只股票，每一列对应一个财务指标，方便我们进行后续的排序、计算和选股。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_77.png)

## 总结 ✨

本节课中我们一起学习了量化策略中两个重要的数据处理步骤：

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_79.png)

1.  **股票过滤**：我们编写了三个过滤函数，分别用于剔除停牌股、ST股和上市时间过短的新股，从而得到一个高质量的目标股票池。
2.  **指标查询**：我们使用 `get_fundamentals` 函数查询了目标股票的市净率和市值指标，并通过转置和删除空值操作，将数据整理成适合分析的格式。

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_81.png)

![](img/4df0acbdd1d4ec1eabde92eaea7ae74e_83.png)

这些步骤为后续基于因子（如低市净率、小市值）进行股票排序和选择打下了坚实的基础。下一节，我们将学习如何利用这些处理好的数据来构建具体的交易信号。