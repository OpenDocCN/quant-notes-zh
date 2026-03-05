# Python金融分析与量化交易实战：P36：36.2-过滤筛选因子指标数据 📊

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_0.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_2.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_4.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_6.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的财务指标数据。这是构建量化交易策略中非常关键的一步，目的是剔除无效或不符合条件的股票，并准备好用于后续分析的因子数据。

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_8.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_10.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_12.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现股票池的筛选和因子数据的获取。

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_14.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_16.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_18.png)

## 第一步：过滤不想要的股票 🚫

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_20.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_22.png)

在构建股票池时，我们需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一功能的三个核心函数。

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_24.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_26.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_28.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_30.png)

### 1. 过滤停牌股票
我们首先定义一个函数，用于过滤掉全年停牌的股票。其核心逻辑是遍历股票列表，使用API判断每只股票是否停牌，只保留未停牌的股票。

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_32.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_34.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_36.png)

```python
def filter_suspended_stocks(stock_list):
    """
    过滤掉全年停牌的股票。
    :param stock_list: 股票代码列表
    :return: 过滤后的股票代码列表
    """
    filtered_list = []
    for stock in stock_list:
        # 使用API判断股票是否全年停牌
        if not is_suspended(stock):
            filtered_list.append(stock)
    return filtered_list
```

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_38.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_40.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_42.png)

### 2. 过滤ST股票
接下来，我们过滤掉ST（特别处理）股票。这类股票通常存在财务或其他异常状况，风险较高。

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_44.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_46.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_48.png)

```python
def filter_st_stocks(stock_list):
    """
    过滤掉ST股票。
    :param stock_list: 股票代码列表
    :return: 过滤后的股票代码列表
    """
    filtered_list = []
    for stock in stock_list:
        # 使用API判断股票是否为ST股
        if not is_st(stock):
            filtered_list.append(stock)
    return filtered_list
```

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_50.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_52.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_54.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_56.png)

### 3. 过滤新股
最后，我们过滤掉上市时间过短的新股。新股通常价格波动较大，数据不充分，不适合某些策略。这里我们设定一个阈值（例如上市超过180天）。

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_58.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_60.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_62.png)

```python
def filter_new_stocks(stock_list, days_threshold=180):
    """
    过滤掉上市天数少于指定阈值的新股。
    :param stock_list: 股票代码列表
    :param days_threshold: 上市天数阈值，默认为180天
    :return: 过滤后的股票代码列表
    """
    filtered_list = []
    for stock in stock_list:
        # 获取股票上市天数
        days_listed = get_days_listed(stock)
        # 如果上市天数大于阈值，则保留
        if days_listed > days_threshold:
            filtered_list.append(stock)
    return filtered_list
```

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_64.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_66.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_68.png)

## 第二步：执行股票池过滤 🔄

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_70.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_72.png)

定义了过滤函数后，我们需要按顺序对初始股票池执行这些过滤操作。

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_74.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_76.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_78.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_80.png)

```python
# 假设 initial_stocks 是初始获取的所有股票代码列表
initial_stocks = get_all_stocks()

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_82.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_84.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_86.png)

# 第一步：过滤停牌股
filtered_stocks_1 = filter_suspended_stocks(initial_stocks)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_88.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_90.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_92.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_94.png)

# 第二步：过滤ST股
filtered_stocks_2 = filter_st_stocks(filtered_stocks_1)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_96.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_98.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_100.png)

# 第三步：过滤新股
final_stocks = filter_new_stocks(filtered_stocks_2, days_threshold=180)
```

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_102.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_104.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_106.png)

经过以上三步，`final_stocks` 变量中存储的就是经过初步筛选、符合我们基本交易条件的股票池。

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_108.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_110.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_112.png)

## 第三步：查询所需的因子指标 📈

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_114.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_116.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_118.png)

获得干净的股票池后，下一步是获取用于量化分析的财务指标。在本例中，我们关注**市净率（PB）**和**市值（Market Cap）**这两个因子。

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_120.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_122.png)

以下是查询这些指标的核心代码：

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_124.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_126.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_128.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_130.png)

```python
# 使用 get_fundamentals API 查询财务数据
# query 参数指定要查询的指标
# filter 参数指定只查询我们股票池中的股票
query_statement = query(
    fundamentals.eod_derivative_indicator.pb_ratio,  # 市净率 PB
    fundamentals.eod_derivative_indicator.market_cap   # 总市值
).filter(
    fundamentals.income_statement.stockcode.in_(final_stocks)  # 过滤条件：股票代码在 final_stocks 中
)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_132.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_134.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_136.png)

# 执行查询
factor_data = get_fundamentals(query_statement)
```

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_138.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_140.png)

## 第四步：处理查询结果 🔧

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_142.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_144.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_146.png)

直接查询返回的数据格式（指标为行，股票为列）通常不便于分析。我们需要将其转置，使每一行代表一只股票，每一列代表一个指标。同时，处理可能存在的空值。

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_148.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_150.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_152.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_154.png)

```python
# 转置数据框，使股票成为行，指标成为列
factor_data_transposed = factor_data.T

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_156.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_158.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_160.png)

# 删除包含空值（NaN）的行，确保数据完整性
factor_data_cleaned = factor_data_transposed.dropna()

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_162.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_164.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_166.png)

# 此时，factor_data_cleaned 就是一个干净的、格式规整的因子数据表
# 行索引是股票代码，列是 ‘pb_ratio’ 和 ‘market_cap’
```

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_168.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_170.png)

![](img/f6b0d4e042d24a85bc4644c8f2179f5b_172.png)

本节课中我们一起学习了量化策略数据准备的关键步骤。我们首先定义了三个过滤函数，用于从股票池中剔除停牌股、ST股和新股，得到了一个干净的候选股票列表。接着，我们使用 `get_fundamentals` 接口查询了所需的市净率和市值因子数据。最后，我们对查询结果进行了转置和清洗，得到了一个结构清晰、便于后续分析和建模的因子数据表。这些数据是构建和回测量化交易策略的基础。