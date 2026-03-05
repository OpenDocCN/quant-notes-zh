# Python金融量化分析：P35：36-过滤筛选因子指标数据

![](img/8dd191a30cf8e040f318ad21a2c7be58_0.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_2.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_4.png)

在本节课中，我们将学习如何对股票池进行过滤筛选，并获取我们所需的财务因子指标数据。这是构建量化策略的关键一步，能帮助我们剔除无效或不符合条件的股票，专注于高质量的数据进行分析。

![](img/8dd191a30cf8e040f318ad21a2c7be58_6.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_8.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_10.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现股票数据的预处理。

![](img/8dd191a30cf8e040f318ad21a2c7be58_12.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_14.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_16.png)

## 过滤不想要的股票

![](img/8dd191a30cf8e040f318ad21a2c7be58_18.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_20.png)

在构建股票池时，我们需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一功能的三个核心过滤函数。

![](img/8dd191a30cf8e040f318ad21a2c7be58_22.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_24.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_26.png)

### 1. 过滤停牌股票

![](img/8dd191a30cf8e040f318ad21a2c7be58_28.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_30.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_32.png)

此函数用于判断并过滤掉全年停牌的股票。

![](img/8dd191a30cf8e040f318ad21a2c7be58_34.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_36.png)

```python
def filter_suspended_stocks(stock_list):
    """
    过滤停牌股票
    :param stock_list: 初始股票列表
    :return: 过滤掉停牌股后的股票列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API判断股票是否全年停牌
        if not is_suspended(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/8dd191a30cf8e040f318ad21a2c7be58_38.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_40.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_42.png)

### 2. 过滤ST股票

![](img/8dd191a30cf8e040f318ad21a2c7be58_44.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_46.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_48.png)

此函数用于判断并过滤掉被标记为ST（特别处理）的股票。

![](img/8dd191a30cf8e040f318ad21a2c7be58_50.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_52.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_54.png)

```python
def filter_st_stocks(stock_list):
    """
    过滤ST股票
    :param stock_list: 经过初步过滤的股票列表
    :return: 过滤掉ST股后的股票列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API判断股票是否为ST股
        if not is_st(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/8dd191a30cf8e040f318ad21a2c7be58_56.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_58.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_60.png)

### 3. 过滤新股

![](img/8dd191a30cf8e040f318ad21a2c7be58_62.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_64.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_66.png)

此函数用于过滤掉上市时间过短（例如少于180天）的股票，以确保数据具有足够的统计意义。

![](img/8dd191a30cf8e040f318ad21a2c7be58_68.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_70.png)

```python
def filter_new_stocks(stock_list, min_days=180):
    """
    过滤上市时间过短的新股
    :param stock_list: 经过前两步过滤的股票列表
    :param min_days: 最小上市天数要求，默认为180天
    :return: 过滤掉新股后的股票列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 获取股票上市以来的天数
        days_listed = get_days_listed(stock)
        # 判断是否满足最小天数要求
        if days_listed > min_days:
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/8dd191a30cf8e040f318ad21a2c7be58_72.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_74.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_76.png)

## 应用过滤函数

![](img/8dd191a30cf8e040f318ad21a2c7be58_78.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_80.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_82.png)

定义了过滤函数后，我们需要按顺序将它们应用到初始的股票列表中。

![](img/8dd191a30cf8e040f318ad21a2c7be58_84.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_86.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_88.png)

```python
# 假设 initial_stocks 是初始获取的所有股票代码列表
initial_stocks = get_all_stocks()

![](img/8dd191a30cf8e040f318ad21a2c7be58_90.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_92.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_94.png)

# 第一步：过滤停牌股
stocks_after_suspension = filter_suspended_stocks(initial_stocks)

![](img/8dd191a30cf8e040f318ad21a2c7be58_96.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_98.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_100.png)

# 第二步：过滤ST股
stocks_after_st = filter_st_stocks(stocks_after_suspension)

![](img/8dd191a30cf8e040f318ad21a2c7be58_102.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_104.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_106.png)

# 第三步：过滤新股
final_stocks = filter_new_stocks(stocks_after_st, min_days=180)
```

![](img/8dd191a30cf8e040f318ad21a2c7be58_108.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_110.png)

## 查询所需的因子指标数据

![](img/8dd191a30cf8e040f318ad21a2c7be58_112.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_114.png)

在得到干净的股票池后，下一步是获取我们策略分析所需的财务指标。这里以市净率（PB）和市值（Market Cap）为例。

![](img/8dd191a30cf8e040f318ad21a2c7be58_116.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_118.png)

以下是使用 `get_fundamentals` API 查询指标数据的代码。

![](img/8dd191a30cf8e040f318ad21a2c7be58_120.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_122.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_124.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_126.png)

```python
# 定义要查询的指标：市净率（pb_ratio）和市值（market_cap）
query = query(
    fundamentals.eod_derivative_indicator.pb_ratio,
    fundamentals.eod_derivative_indicator.market_cap
)

![](img/8dd191a30cf8e040f318ad21a2c7be58_128.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_130.png)

# 应用过滤条件，只查询我们最终股票池中的股票
# 注意：这里的 final_stocks 是上一步过滤后得到的列表
data = get_fundamentals(query).filter(
    fundamentals.eod_derivative_indicator.code.in_(final_stocks)
)
```

![](img/8dd191a30cf8e040f318ad21a2c7be58_132.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_134.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_136.png)

## 数据整理与转置

![](img/8dd191a30cf8e040f318ad21a2c7be58_138.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_140.png)

通过API获取的原始数据格式通常是横表（指标为列，股票为行），而分析时我们通常需要竖表格式（股票为列，指标为行）。此外，我们还需要处理可能存在的空值。

![](img/8dd191a30cf8e040f318ad21a2c7be58_142.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_144.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_146.png)

以下是进行数据转置和清洗的步骤。

![](img/8dd191a30cf8e040f318ad21a2c7be58_148.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_150.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_152.png)

```python
# 对数据进行转置，使股票代码成为列名，指标成为行索引
transposed_data = data.T

![](img/8dd191a30cf8e040f318ad21a2c7be58_154.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_156.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_158.png)

# 填充或删除空值。这里选择删除包含任何空值的列（股票）
# 使用 dropna 方法，axis=1 表示按列删除
cleaned_data = transposed_data.dropna(axis=1)

![](img/8dd191a30cf8e040f318ad21a2c7be58_160.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_162.png)

# 此时，cleaned_data 就是一个干净的、可供后续分析的DataFrame
# 行索引是指标名称，列索引是股票代码
```

![](img/8dd191a30cf8e040f318ad21a2c7be58_164.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_166.png)

![](img/8dd191a30cf8e040f318ad21a2c7be58_168.png)

本节课中我们一起学习了量化分析中数据预处理的核心步骤。我们首先编写了三个过滤函数来剔除停牌股、ST股和新股，得到了一个高质量的股票池。然后，我们使用 `get_fundamentals` 查询了所需的财务因子指标（市净率和市值）。最后，我们对获取的数据进行了转置和空值处理，将其整理成适合后续建模分析的格式。这些步骤是构建稳健量化策略的重要基础。