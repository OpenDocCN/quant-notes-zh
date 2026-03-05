# Python金融分析量化交易：P36：2-过滤筛选因子指标数据 📊

![](img/ca49134df6eba1cc7cb0ffe579ffb506_0.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_2.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_4.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_6.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_8.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_10.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_12.png)

在本节课中，我们将学习如何对股票池进行过滤筛选，并获取我们所需的财务指标数据。这是构建量化交易策略中非常关键的一步，能帮助我们剔除无效或不符合条件的股票，专注于分析高质量的标的。

![](img/ca49134df6eba1cc7cb0ffe579ffb506_14.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_16.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_18.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_20.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_22.png)

上一节我们介绍了如何获取初始的股票列表，本节中我们来看看如何对这些股票进行清洗和筛选。

![](img/ca49134df6eba1cc7cb0ffe579ffb506_24.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_26.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_28.png)

## 过滤不想要的股票 🧹

![](img/ca49134df6eba1cc7cb0ffe579ffb506_30.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_32.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_34.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_36.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_38.png)

在构建股票池时，我们需要剔除一些不符合交易条件的股票，例如停牌股、ST股和上市时间过短的新股。以下是实现这一功能的三个核心过滤函数。

![](img/ca49134df6eba1cc7cb0ffe579ffb506_40.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_42.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_44.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_46.png)

### 1. 过滤停牌股票
此函数用于判断并过滤掉全年停牌的股票。

![](img/ca49134df6eba1cc7cb0ffe579ffb506_48.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_50.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_52.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_54.png)

```python
def filter_suspended(stock_list):
    filtered_list = []
    for stock in stock_list:
        if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API
            filtered_list.append(stock)
    return filtered_list
```

![](img/ca49134df6eba1cc7cb0ffe579ffb506_56.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_58.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_60.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_62.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_64.png)

### 2. 过滤ST股票
此函数用于判断并过滤掉被标记为ST（特别处理）的股票。

![](img/ca49134df6eba1cc7cb0ffe579ffb506_66.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_68.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_70.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_72.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_74.png)

```python
def filter_st(stock_list):
    filtered_list = []
    for stock in stock_list:
        if not is_st(stock):  # 假设 is_st 是判断ST股的API
            filtered_list.append(stock)
    return filtered_list
```

![](img/ca49134df6eba1cc7cb0ffe579ffb506_76.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_78.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_80.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_82.png)

### 3. 过滤新股
此函数用于过滤掉上市时间过短（例如少于180天）的股票，以确保数据有足够的长度进行分析。

![](img/ca49134df6eba1cc7cb0ffe579ffb506_84.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_86.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_88.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_90.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_92.png)

```python
def filter_new(stock_list):
    filtered_list = []
    for stock in stock_list:
        if days_from_listed(stock) > 180:  # 假设 days_from_listed 能返回上市天数
            filtered_list.append(stock)
    return filtered_list
```

![](img/ca49134df6eba1cc7cb0ffe579ffb506_94.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_96.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_98.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_100.png)

完成函数定义后，我们可以按顺序调用它们，对初始股票池进行层层过滤。

![](img/ca49134df6eba1cc7cb0ffe579ffb506_102.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_104.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_106.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_108.png)

```python
# 假设 all_stocks 是初始获取的所有股票列表
filtered_stocks = filter_suspended(all_stocks)
filtered_stocks = filter_st(filtered_stocks)
filtered_stocks = filter_new(filtered_stocks)
```

![](img/ca49134df6eba1cc7cb0ffe579ffb506_110.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_112.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_114.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_116.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_118.png)

## 查询所需的财务指标 📈

![](img/ca49134df6eba1cc7cb0ffe579ffb506_120.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_122.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_124.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_126.png)

过滤掉不想要的股票后，下一步是获取我们策略中需要分析的财务指标数据，例如市净率（PB）和市值（Market Cap）。

![](img/ca49134df6eba1cc7cb0ffe579ffb506_128.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_130.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_132.png)

我们使用 `get_fundamentals` 函数来查询这些数据。该函数需要一个查询语句（query）来指定要获取的指标，并通过过滤器（filter）限定只查询我们过滤后股票池中的股票。

![](img/ca49134df6eba1cc7cb0ffe579ffb506_134.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_136.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_138.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_140.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_142.png)

以下是查询市净率和市值的示例代码：

![](img/ca49134df6eba1cc7cb0ffe579ffb506_144.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_146.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_148.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_150.png)

```python
# 构建查询语句，获取市净率(pb_ratio)和市值(market_cap)
query = query(fundamentals.eod_derivative_indicator.pb_ratio,
              fundamentals.eod_derivative_indicator.market_cap)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_152.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_154.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_156.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_158.png)

# 执行查询，并限定股票范围为我们过滤后的列表
fundamental_data = get_fundamentals(query, filter=fundamentals.stockcode.in_(filtered_stocks))
```

![](img/ca49134df6eba1cc7cb0ffe579ffb506_160.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_162.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_164.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_166.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_168.png)

查询返回的数据格式通常是横向的（指标为列，股票为行）。为了便于后续分析，我们通常需要将其转置，使每一行代表一只股票，每一列代表一个指标。同时，可以剔除其中的空值。

![](img/ca49134df6eba1cc7cb0ffe579ffb506_170.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_172.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_174.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_176.png)

```python
# 转置数据框，并删除包含空值的行
processed_data = fundamental_data.T.dropna()
```

![](img/ca49134df6eba1cc7cb0ffe579ffb506_178.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_180.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_182.png)

![](img/ca49134df6eba1cc7cb0ffe579ffb506_184.png)

本节课中我们一起学习了量化策略中数据准备的关键步骤。我们首先定义了三个过滤函数来清洗股票池，去除了停牌股、ST股和上市时间过短的新股。然后，我们使用 `get_fundamentals` 函数查询了所需的财务指标（市净率和市值），并对结果数据进行了转置和清理，为后续的因子分析和策略构建打下了坚实的基础。