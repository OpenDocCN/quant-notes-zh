# 量化交易教程：P36：过滤筛选因子指标数据 📊

![](img/e8964825e11f15f8d0927ea299f0fe79_0.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_2.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_4.png)

在本节课中，我们将学习如何对股票池进行过滤和筛选，以剔除不符合交易条件的股票（如停牌股、ST股、新股），并获取我们所需的财务指标数据（如市净率和市值），为后续的因子策略分析做准备。

![](img/e8964825e11f15f8d0927ea299f0fe79_6.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_8.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_10.png)

上一节我们介绍了量化交易的基本框架，本节中我们来看看如何在实际操作中清洗和准备数据。

![](img/e8964825e11f15f8d0927ea299f0fe79_12.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_14.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_16.png)

## 过滤不想要的股票 🚫

![](img/e8964825e11f15f8d0927ea299f0fe79_18.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_20.png)

在构建股票池时，我们首先需要剔除一些不符合交易条件的股票。以下是常见的需要过滤的股票类型：

![](img/e8964825e11f15f8d0927ea299f0fe79_22.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_24.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_26.png)

*   **停牌股票**：无法进行交易。
*   **ST/S*ST股票**：存在退市风险或其他特别处理情况。
*   **新上市股票**：上市时间过短，数据可能不稳定，缺乏足够的交易历史。

![](img/e8964825e11f15f8d0927ea299f0fe79_28.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_30.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_32.png)

我们将编写三个独立的过滤函数来完成这项工作。

![](img/e8964825e11f15f8d0927ea299f0fe79_34.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_36.png)

### 1. 过滤停牌股票

![](img/e8964825e11f15f8d0927ea299f0fe79_38.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_40.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_42.png)

这个函数用于判断并过滤掉全年停牌的股票。

![](img/e8964825e11f15f8d0927ea299f0fe79_44.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_46.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_48.png)

```python
def filter_paused(stock_list):
    """
    过滤停牌股票
    :param stock_list: 初始股票列表
    :return: 过滤掉停牌股票后的列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API判断股票是否全年停牌
        if not is_suspended(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/e8964825e11f15f8d0927ea299f0fe79_50.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_52.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_54.png)

### 2. 过滤ST/S*ST股票

![](img/e8964825e11f15f8d0927ea299f0fe79_56.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_58.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_60.png)

这个函数用于判断并过滤掉ST或S*ST股票。

![](img/e8964825e11f15f8d0927ea299f0fe79_62.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_64.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_66.png)

```python
def filter_st(stock_list):
    """
    过滤ST/S*ST股票
    :param stock_list: 经过停牌过滤后的股票列表
    :return: 过滤掉ST股后的列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API判断股票是否为ST/S*ST
        if not is_st_stock(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/e8964825e11f15f8d0927ea299f0fe79_68.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_70.png)

### 3. 过滤新上市股票

![](img/e8964825e11f15f8d0927ea299f0fe79_72.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_74.png)

这个函数用于过滤掉上市时间过短的股票（例如，上市天数少于180天）。

![](img/e8964825e11f15f8d0927ea299f0fe79_76.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_78.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_80.png)

```python
def filter_new(stock_list):
    """
    过滤新上市股票
    :param stock_list: 经过前两步过滤后的股票列表
    :return: 过滤掉新股后的列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 获取股票上市天数，假设有get_listing_days函数
        listing_days = get_listing_days(stock)
        # 过滤掉上市天数少于180天的股票
        if listing_days > 180:
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/e8964825e11f15f8d0927ea299f0fe79_82.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_84.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_86.png)

### 执行过滤流程

![](img/e8964825e11f15f8d0927ea299f0fe79_88.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_90.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_92.png)

编写完过滤函数后，我们需要按顺序调用它们，对初始股票池进行层层筛选。

![](img/e8964825e11f15f8d0927ea299f0fe79_94.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_96.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_98.png)

```python
# 假设 initial_stocks 是初始获取的所有股票代码列表
initial_stocks = ['000001.XSHE', '600000.XSHG', ...]

![](img/e8964825e11f15f8d0927ea299f0fe79_100.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_102.png)

# 第一步：过滤停牌股
stocks_after_pause = filter_paused(initial_stocks)

![](img/e8964825e11f15f8d0927ea299f0fe79_104.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_106.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_108.png)

# 第二步：过滤ST股
stocks_after_st = filter_st(stocks_after_pause)

![](img/e8964825e11f15f8d0927ea299f0fe79_110.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_112.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_114.png)

# 第三步：过滤新股
final_stocks = filter_new(stocks_after_st)

![](img/e8964825e11f15f8d0927ea299f0fe79_116.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_118.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_120.png)

print(f"初始股票数量：{len(initial_stocks)}")
print(f"最终合格股票数量：{len(final_stocks)}")
```

![](img/e8964825e11f15f8d0927ea299f0fe79_122.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_124.png)

## 查询所需的财务指标 📈

![](img/e8964825e11f15f8d0927ea299f0fe79_126.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_128.png)

在得到干净的股票池后，下一步是获取这些股票的特定财务指标数据，例如市净率（PB）和总市值（Market Cap），这些是因子策略中常用的因子。

![](img/e8964825e11f15f8d0927ea299f0fe79_130.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_132.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_134.png)

以下是查询指标数据的步骤：

![](img/e8964825e11f15f8d0927ea299f0fe79_136.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_138.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_140.png)

1.  **确定查询目标**：明确需要获取哪些指标。
2.  **构建查询语句**：使用平台提供的API（如`get_fundamentals`）和类SQL的查询语法。
3.  **指定查询范围**：将查询限定在我们过滤后的股票池`final_stocks`中。
4.  **处理返回数据**：API返回的数据格式可能需要进行转置（行转列）和缺失值处理，以符合分析需求。

![](img/e8964825e11f15f8d0927ea299f0fe79_142.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_144.png)

```python
# 导入必要的库，假设q是量化平台提供的API对象
import pandas as pd

![](img/e8964825e11f15f8d0927ea299f0fe79_146.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_148.png)

# 构建查询语句，查询市净率(pb_ratio)和总市值(market_cap)
query = query(
    fundamentals.eod_derivative_indicator.pb_ratio, # 市净率
    fundamentals.eod_derivative_indicator.market_cap # 总市值
).filter(
    fundamentals.income_statement.stockcode.in_(final_stocks) # 限定股票范围
)

![](img/e8964825e11f15f8d0927ea299f0fe79_150.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_152.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_154.png)

# 执行查询
df = get_fundamentals(query)

![](img/e8964825e11f15f8d0927ea299f0fe79_156.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_158.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_160.png)

# 处理数据：转置，使每一行代表一只股票，每一列代表一个指标
df = df.T

![](img/e8964825e11f15f8d0927ea299f0fe79_162.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_164.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_166.png)

# 处理缺失值：简单删除包含NaN的行
df_cleaned = df.dropna()

![](img/e8964825e11f15f8d0927ea299f0fe79_168.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_170.png)

print(df_cleaned.head())
```

![](img/e8964825e11f15f8d0927ea299f0fe79_172.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_174.png)

![](img/e8964825e11f15f8d0927ea299f0fe79_176.png)

本节课中我们一起学习了量化交易中数据准备的关键步骤。我们首先通过编写三个过滤函数，从股票池中剔除了停牌股、ST股和新股，得到了一个干净的、可用于策略分析的股票列表。接着，我们使用`get_fundamentals` API查询了这些股票的市净率和市值指标，并对返回的数据进行了转置和清洗，使其成为易于分析的格式。这些处理后的数据将为下一节构建具体的因子选股策略打下坚实的基础。