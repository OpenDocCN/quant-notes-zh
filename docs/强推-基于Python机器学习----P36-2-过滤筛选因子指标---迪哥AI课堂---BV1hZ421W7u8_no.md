# 金融量化分析：P36：2-过滤筛选因子指标数据 📊

![](img/06ff87ad1ba43e5659400a3bb5116c60_0.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_2.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_4.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_6.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_8.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的财务指标数据。这是构建量化交易策略中至关重要的一步，目的是剔除无效或不符合策略要求的股票，并准备用于后续分析的核心数据。

![](img/06ff87ad1ba43e5659400a3bb5116c60_10.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_12.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_14.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_16.png)

上一节我们介绍了量化交易的基本概念和策略框架，本节中我们来看看如何具体实现数据的预处理。

![](img/06ff87ad1ba43e5659400a3bb5116c60_18.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_20.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_22.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_24.png)

## 过滤不想要的股票 🚫

![](img/06ff87ad1ba43e5659400a3bb5116c60_26.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_28.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_30.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_32.png)

在构建股票池时，并非所有股票都适合纳入分析。我们需要过滤掉以下几类股票：
*   **停牌股票**：无法交易。
*   **ST/S*ST股票**：存在退市风险或其他特殊状况。
*   **新股**：上市时间过短，数据不充分，波动性大。

![](img/06ff87ad1ba43e5659400a3bb5116c60_34.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_36.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_38.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_40.png)

以下是实现股票过滤功能的步骤：

![](img/06ff87ad1ba43e5659400a3bb5116c60_42.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_44.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_46.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_48.png)

首先，我们需要编写一个函数，用于过滤掉停牌的股票。该函数接收一个股票代码列表，并返回过滤后的列表。

![](img/06ff87ad1ba43e5659400a3bb5116c60_50.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_52.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_54.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_56.png)

```python
def filter_suspended(stock_list):
    """
    过滤停牌股票
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API判断股票是否全年停牌
        if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API函数
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/06ff87ad1ba43e5659400a3bb5116c60_58.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_60.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_62.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_64.png)

接着，我们以同样的逻辑编写过滤ST/S*ST股票的函数。

![](img/06ff87ad1ba43e5659400a3bb5116c60_66.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_68.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_70.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_72.png)

```python
def filter_st(stock_list):
    """
    过滤ST/S*ST股票
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API判断股票是否为ST/S*ST
        if not is_st(stock):  # 假设 is_st 是判断ST的API函数
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/06ff87ad1ba43e5659400a3bb5116c60_74.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_76.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_78.png)

最后，我们编写过滤新上市股票的函数。这里我们通过判断股票上市天数是否超过阈值（例如180天）来实现。

![](img/06ff87ad1ba43e5659400a3bb5116c60_80.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_82.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_84.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_86.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_88.png)

```python
def filter_new(stock_list, days_threshold=180):
    """
    过滤上市时间过短的新股
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API获取股票上市天数，并判断是否大于阈值
        if days_from_listed(stock) > days_threshold:  # 假设 days_from_listed 是获取上市天数的API函数
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/06ff87ad1ba43e5659400a3bb5116c60_90.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_92.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_94.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_96.png)

完成三个过滤函数的编写后，我们可以对初始股票池进行链式过滤。

![](img/06ff87ad1ba43e5659400a3bb5116c60_98.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_100.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_102.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_104.png)

```python
# 假设 all_stocks 是初始获取的所有股票代码列表
filtered_by_suspend = filter_suspended(all_stocks)
filtered_by_st = filter_st(filtered_by_suspend)
final_stock_pool = filter_new(filtered_by_st)
```

![](img/06ff87ad1ba43e5659400a3bb5116c60_106.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_108.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_110.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_112.png)

## 查询所需的财务指标 📈

![](img/06ff87ad1ba43e5659400a3bb5116c60_114.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_116.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_118.png)

过滤出合格的股票池后，下一步是获取这些股票的特定财务指标数据，例如市净率（PB）和市值（Market Cap），这些将作为我们因子策略的核心输入。

![](img/06ff87ad1ba43e5659400a3bb5116c60_120.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_122.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_124.png)

以下是查询指标数据的步骤：

![](img/06ff87ad1ba43e5659400a3bb5116c60_126.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_128.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_130.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_132.png)

我们使用 `get_fundamentals` API函数，通过编写查询语句（query）来获取指定股票的财务数据。

![](img/06ff87ad1ba43e5659400a3bb5116c60_134.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_136.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_138.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_140.png)

```python
# 构建查询语句，获取市净率(pb_ratio)和市值(market_cap)
query = query(
    fundamentals.eod_derivative_indicator.pb_ratio,  # 市净率
    fundamentals.eod_derivative_indicator.market_cap  # 总市值
).filter(
    fundamentals.income_statement.stockcode.in_(final_stock_pool)  # 只查询我们最终股票池中的股票
)

![](img/06ff87ad1ba43e5659400a3bb5116c60_142.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_144.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_146.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_148.png)

# 执行查询
factor_data = get_fundamentals(query)
```

![](img/06ff87ad1ba43e5659400a3bb5116c60_150.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_152.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_154.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_156.png)

查询返回的数据格式（`factor_data`）通常是一个`DataFrame`，其中行是股票代码，列是指标。但为了后续分析方便，我们可能需要将其转置，使每一行代表一只股票的所有指标。同时，需要处理可能存在的空值（NaN）。

![](img/06ff87ad1ba43e5659400a3bb5116c60_158.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_160.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_162.png)

```python
# 转置数据框，使股票代码成为索引，指标成为列
factor_data_transposed = factor_data.T

![](img/06ff87ad1ba43e5659400a3bb5116c60_164.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_166.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_168.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_170.png)

# 删除包含空值的行（简单处理方式）
factor_data_clean = factor_data_transposed.dropna()
```

![](img/06ff87ad1ba43e5659400a3bb5116c60_172.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_174.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_176.png)

![](img/06ff87ad1ba43e5659400a3bb5116c60_178.png)

本节课中我们一起学习了量化交易数据预处理的两个关键步骤：**股票池过滤**与**财务指标查询**。我们通过编写过滤函数移除了停牌股、ST股和新股，确保了股票池的质量。随后，我们使用特定的API查询了目标股票的市净率和市值指标，并对数据进行了转置和清洗，为后续的因子分析和策略构建做好了准备。