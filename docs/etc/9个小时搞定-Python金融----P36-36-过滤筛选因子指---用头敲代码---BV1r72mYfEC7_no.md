# Python金融量化分析：P36：过滤筛选因子指标数据 📊

![](img/2db90d60eac0d42def3a0ef3c59b23e5_0.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_2.png)

在本节课中，我们将学习如何对股票池进行过滤筛选，并获取我们所需的因子指标数据。这是构建量化策略的关键一步，目的是剔除无效或不符合条件的股票，并提取用于后续分析的核心财务数据。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_4.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_6.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现股票数据的预处理。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_8.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_10.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_12.png)

## 过滤不想要的股票 🚫

![](img/2db90d60eac0d42def3a0ef3c59b23e5_14.png)

在构建股票池时，我们需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一功能的步骤。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_16.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_18.png)

首先，我们需要编写一个过滤函数。该函数接收一个股票列表，并返回过滤后的列表。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_20.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_22.png)

```python
def filter_stocks(stock_list):
    # 初始化一个空列表，用于存放过滤后的股票
    filtered_list = []
    # 遍历传入的股票列表
    for stock in stock_list:
        # 判断股票是否全年停牌
        if not is_suspended(stock):
            # 判断股票是否为ST股
            if not is_st(stock):
                # 判断股票是否为新上市股票（例如上市天数小于180天）
                if not is_new(stock, days=180):
                    # 如果以上条件均不满足，则保留该股票
                    filtered_list.append(stock)
    # 返回过滤后的股票列表
    return filtered_list
```

![](img/2db90d60eac0d42def3a0ef3c59b23e5_24.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_26.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_28.png)

接下来，我们分别实现三个判断函数。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_30.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_32.png)

### 判断是否停牌

![](img/2db90d60eac0d42def3a0ef3c59b23e5_34.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_36.png)

使用API判断某只股票是否全年停牌。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_38.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_40.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_42.png)

```python
def is_suspended(stock_code):
    # 调用API判断股票是否停牌
    # 假设存在一个名为 `api.is_suspended` 的函数
    return api.is_suspended(stock_code)
```

![](img/2db90d60eac0d42def3a0ef3c59b23e5_44.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_46.png)

### 判断是否为ST股

![](img/2db90d60eac0d42def3a0ef3c59b23e5_48.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_50.png)

使用API判断某只股票是否为ST股。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_52.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_54.png)

```python
def is_st(stock_code):
    # 调用API判断股票是否为ST股
    # 假设存在一个名为 `api.is_st` 的函数
    return api.is_st(stock_code)
```

![](img/2db90d60eac0d42def3a0ef3c59b23e5_56.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_58.png)

### 判断是否为新股

![](img/2db90d60eac0d42def3a0ef3c59b23e5_60.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_62.png)

通过计算股票上市天数来判断其是否为新股。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_64.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_66.png)

```python
def is_new(stock_code, days=180):
    # 获取股票上市日期
    # 假设存在一个名为 `api.get_listing_date` 的函数
    listing_date = api.get_listing_date(stock_code)
    # 计算当前日期与上市日期的差值
    days_since_listing = (datetime.now() - listing_date).days
    # 如果上市天数小于指定阈值，则认为是新股
    return days_since_listing < days
```

![](img/2db90d60eac0d42def3a0ef3c59b23e5_68.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_70.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_72.png)

完成以上函数定义后，我们可以对初始股票池进行逐层过滤。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_74.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_76.png)

```python
# 假设 initial_stocks 是初始的股票代码列表
initial_stocks = ['000001', '600000', ...]

![](img/2db90d60eac0d42def3a0ef3c59b23e5_78.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_80.png)

# 第一步：过滤停牌股
filtered_by_suspension = [s for s in initial_stocks if not is_suspended(s)]

![](img/2db90d60eac0d42def3a0ef3c59b23e5_82.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_84.png)

# 第二步：过滤ST股
filtered_by_st = [s for s in filtered_by_suspension if not is_st(s)]

![](img/2db90d60eac0d42def3a0ef3c59b23e5_86.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_88.png)

# 第三步：过滤新股
final_stocks = [s for s in filtered_by_st if not is_new(s, 180)]
```

![](img/2db90d60eac0d42def3a0ef3c59b23e5_90.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_92.png)

## 查询想要的指标 🔍

![](img/2db90d60eac0d42def3a0ef3c59b23e5_94.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_96.png)

过滤出合格的股票后，下一步是获取这些股票的特定因子指标数据，例如市净率（PB）和市值（Market Cap）。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_98.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_100.png)

我们使用 `get_fundamentals` 函数来查询财务数据。该函数需要传入一个SQL查询语句。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_102.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_104.png)

以下是查询市净率和市值的代码示例。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_106.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_108.png)

```python
# 定义查询语句，获取市净率(pb_ratio)和市值(market_cap)
query = query(fundamentals.eod_derivative_indicator.pb_ratio,
              fundamentals.eod_derivative_indicator.market_cap)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_110.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_112.png)

# 执行查询，并指定只查询我们最终过滤出的股票
# 假设 final_stocks 是过滤后的股票代码列表
data = get_fundamentals(query, filter=fundamentals.stockcode.in_(final_stocks))
```

![](img/2db90d60eac0d42def3a0ef3c59b23e5_114.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_116.png)

查询返回的数据通常是一个 `DataFrame`，其中行是股票代码，列是指标。但为了后续分析方便，我们通常需要对其进行转置，并处理可能存在的空值。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_118.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_120.png)

```python
# 转置DataFrame，使每一行对应一只股票，每一列对应一个指标
transposed_data = data.T

![](img/2db90d60eac0d42def3a0ef3c59b23e5_122.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_124.png)

# 删除包含空值的行
cleaned_data = transposed_data.dropna()
```

![](img/2db90d60eac0d42def3a0ef3c59b23e5_126.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_128.png)

## 总结 📝

![](img/2db90d60eac0d42def3a0ef3c59b23e5_130.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_132.png)

本节课中我们一起学习了量化策略中数据预处理的两个核心步骤。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_134.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_136.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_138.png)

首先，我们通过编写过滤函数，从股票池中剔除了停牌股、ST股和新股，确保后续分析基于有效且稳定的交易标的。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_140.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_142.png)

其次，我们使用 `get_fundamentals` 函数查询了特定因子指标（市净率和市值），并对查询结果进行了转置和清洗，得到了结构清晰、可供模型直接使用的数据。

![](img/2db90d60eac0d42def3a0ef3c59b23e5_144.png)

![](img/2db90d60eac0d42def3a0ef3c59b23e5_146.png)

这些步骤为后续的因子分析、模型构建和回测奠定了坚实的数据基础。