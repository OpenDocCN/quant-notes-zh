# 量化交易教程：P36：过滤筛选因子指标数据

![](img/fa316671159a92c8f25532acd39706ea_0.png)

![](img/fa316671159a92c8f25532acd39706ea_2.png)

在本节课中，我们将学习如何编写一个函数来过滤股票池，并查询我们所需的因子指标数据。这是构建量化交易策略中非常关键的一步，它帮助我们剔除无效或不符合条件的股票，并获取用于分析和决策的核心财务数据。

![](img/fa316671159a92c8f25532acd39706ea_4.png)

![](img/fa316671159a92c8f25532acd39706ea_5.png)

![](img/fa316671159a92c8f25532acd39706ea_6.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现数据的预处理和获取。

![](img/fa316671159a92c8f25532acd39706ea_8.png)

![](img/fa316671159a92c8f25532acd39706ea_9.png)

![](img/fa316671159a92c8f25532acd39706ea_10.png)

## 过滤不想要的股票

![](img/fa316671159a92c8f25532acd39706ea_12.png)

![](img/fa316671159a92c8f25532acd39706ea_13.png)

在构建股票池时，我们通常需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一功能的步骤：

![](img/fa316671159a92c8f25532acd39706ea_14.png)

![](img/fa316671159a92c8f25532acd39706ea_15.png)

![](img/fa316671159a92c8f25532acd39706ea_16.png)

![](img/fa316671159a92c8f25532acd39706ea_17.png)

首先，我们需要定义一个过滤函数。这个函数接收一个股票列表作为输入，并依次应用多个过滤条件。

![](img/fa316671159a92c8f25532acd39706ea_19.png)

![](img/fa316671159a92c8f25532acd39706ea_20.png)

### 1. 过滤停牌股票

![](img/fa316671159a92c8f25532acd39706ea_21.png)

![](img/fa316671159a92c8f25532acd39706ea_22.png)

我们使用API来判断股票是否全年停牌。如果股票停牌，则将其从列表中移除。

![](img/fa316671159a92c8f25532acd39706ea_24.png)

![](img/fa316671159a92c8f25532acd39706ea_25.png)

![](img/fa316671159a92c8f25532acd39706ea_26.png)

![](img/fa316671159a92c8f25532acd39706ea_27.png)

```python
def filter_suspended_stocks(stock_list):
    filtered_list = []
    for stock in stock_list:
        if not api.is_suspended(stock):  # 假设api.is_suspended是判断停牌的API
            filtered_list.append(stock)
    return filtered_list
```

![](img/fa316671159a92c8f25532acd39706ea_29.png)

![](img/fa316671159a92c8f25532acd39706ea_30.png)

![](img/fa316671159a92c8f25532acd39706ea_31.png)

### 2. 过滤ST股票

![](img/fa316671159a92c8f25532acd39706ea_33.png)

![](img/fa316671159a92c8f25532acd39706ea_34.png)

接下来，我们过滤掉被标记为ST（特别处理）的股票。

![](img/fa316671159a92c8f25532acd39706ea_36.png)

![](img/fa316671159a92c8f25532acd39706ea_37.png)

![](img/fa316671159a92c8f25532acd39706ea_38.png)

```python
def filter_st_stocks(stock_list):
    filtered_list = []
    for stock in stock_list:
        if not api.is_st(stock):  # 假设api.is_st是判断ST股的API
            filtered_list.append(stock)
    return filtered_list
```

![](img/fa316671159a92c8f25532acd39706ea_39.png)

![](img/fa316671159a92c8f25532acd39706ea_41.png)

### 3. 过滤新股

![](img/fa316671159a92c8f25532acd39706ea_43.png)

![](img/fa316671159a92c8f25532acd39706ea_45.png)

![](img/fa316671159a92c8f25532acd39706ea_47.png)

最后，我们过滤掉上市时间过短的股票。这里我们设定一个阈值（例如180天），只保留上市超过此天数的股票。

```python
def filter_new_stocks(stock_list, days_threshold=180):
    filtered_list = []
    for stock in stock_list:
        # 假设api.get_listing_days可以获取上市天数
        if api.get_listing_days(stock) > days_threshold:
            filtered_list.append(stock)
    return filtered_list
```

![](img/fa316671159a92c8f25532acd39706ea_49.png)

![](img/fa316671159a92c8f25532acd39706ea_50.png)

![](img/fa316671159a92c8f25532acd39706ea_51.png)

![](img/fa316671159a92c8f25532acd39706ea_52.png)

![](img/fa316671159a92c8f25532acd39706ea_53.png)

在实际应用中，我们将这三个过滤步骤串联起来，对初始股票池进行清洗。

![](img/fa316671159a92c8f25532acd39706ea_55.png)

![](img/fa316671159a92c8f25532acd39706ea_56.png)

```python
# 假设initial_stocks是初始股票列表
filtered_by_suspension = filter_suspended_stocks(initial_stocks)
filtered_by_st = filter_st_stocks(filtered_by_suspension)
final_filtered_stocks = filter_new_stocks(filtered_by_st)
```

![](img/fa316671159a92c8f25532acd39706ea_58.png)

![](img/fa316671159a92c8f25532acd39706ea_59.png)

## 查询所需的指标数据

![](img/fa316671159a92c8f25532acd39706ea_60.png)

![](img/fa316671159a92c8f25532acd39706ea_61.png)

过滤出合格的股票后，下一步是获取这些股票的特定财务指标数据。在本例中，我们关注**市盈率（P/E Ratio）**和**市值（Market Cap）**。

![](img/fa316671159a92c8f25532acd39706ea_63.png)

![](img/fa316671159a92c8f25532acd39706ea_64.png)

![](img/fa316671159a92c8f25532acd39706ea_65.png)

![](img/fa316671159a92c8f25532acd39706ea_66.png)

我们使用`get_fundamentals`函数来查询数据。该函数需要一个查询语句（query）来指定要获取的指标，并通过`filter`参数限定查询的股票范围。

![](img/fa316671159a92c8f25532acd39706ea_68.png)

![](img/fa316671159a92c8f25532acd39706ea_69.png)

以下是查询这两个指标的代码示例：

![](img/fa316671159a92c8f25532acd39706ea_71.png)

![](img/fa316671159a92c8f25532acd39706ea_73.png)

```python
# 查询市盈率(pe_ratio)和市值(market_cap)
query = query(fundamentals.eod_derivative_indicator.pe_ratio,
              fundamentals.eod_derivative_indicator.market_cap)

![](img/fa316671159a92c8f25532acd39706ea_74.png)

![](img/fa316671159a92c8f25532acd39706ea_75.png)

# 应用过滤，只查询我们最终筛选出的股票列表
fundamental_data = get_fundamentals(query, filter=fundamentals.stockcode.in_(final_filtered_stocks))
```

![](img/fa316671159a92c8f25532acd39706ea_77.png)

![](img/fa316671159a92c8f25532acd39706ea_78.png)

![](img/fa316671159a92c8f25532acd39706ea_79.png)

查询返回的数据通常是一个`DataFrame`，其行是股票代码，列是指标。然而，我们通常希望数据是转置后的格式，即行是指标，列是股票。此外，我们可能需要处理数据中的空值。

![](img/fa316671159a92c8f25532acd39706ea_81.png)

![](img/fa316671159a92c8f25532acd39706ea_82.png)

![](img/fa316671159a92c8f25532acd39706ea_83.png)

以下是进行数据转置和清理的示例：

![](img/fa316671159a92c8f25532acd39706ea_85.png)

![](img/fa316671159a92c8f25532acd39706ea_86.png)

```python
# 转置DataFrame，使股票代码成为列
transposed_data = fundamental_data.T

![](img/fa316671159a92c8f25532acd39706ea_88.png)

![](img/fa316671159a92c8f25532acd39706ea_89.png)

![](img/fa316671159a92c8f25532acd39706ea_90.png)

# 删除包含空值的行（简单处理）
cleaned_data = transposed_data.dropna()
```

![](img/fa316671159a92c8f25532acd39706ea_92.png)

![](img/fa316671159a92c8f25532acd39706ea_93.png)

通过以上步骤，我们就得到了一个干净、格式规整的因子指标数据集，可以用于后续的因子分析和策略回测。

![](img/fa316671159a92c8f25532acd39706ea_94.png)

![](img/fa316671159a92c8f25532acd39706ea_95.png)

![](img/fa316671159a92c8f25532acd39706ea_96.png)

![](img/fa316671159a92c8f25532acd39706ea_97.png)

本节课中我们一起学习了如何通过编写过滤函数来清洗股票池，以及如何使用`get_fundamentals` API查询特定的财务指标数据。掌握这些数据预处理技能是进行有效量化分析的基础。在接下来的课程中，我们将利用这些处理好的数据来构建和测试具体的交易策略。