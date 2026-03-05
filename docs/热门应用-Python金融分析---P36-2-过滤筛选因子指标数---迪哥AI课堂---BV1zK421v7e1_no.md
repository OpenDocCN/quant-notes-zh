# Python金融分析量化交易：P36：2-过滤筛选因子指标数据 📊

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_0.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_2.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_4.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_6.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_8.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的财务指标数据。这是构建量化交易策略中非常关键的一步，目的是剔除无效或高风险股票，并准备好用于后续分析的因子数据。

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_10.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_12.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_14.png)

上一节我们介绍了如何获取初始的股票池，本节中我们来看看如何对这些股票进行过滤，并提取关键的财务指标。

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_16.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_18.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_20.png)

## 过滤股票池 🧹

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_22.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_24.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_26.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_28.png)

在开始分析之前，我们需要对初始股票池进行清洗，剔除不符合交易条件的股票。这通常包括停牌股、ST股和上市时间过短的新股。

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_30.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_32.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_34.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_36.png)

以下是实现股票过滤的三个核心函数：

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_38.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_40.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_42.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_44.png)

```python
def filter_paused(stock_list):
    """
    过滤停牌股票。
    遍历股票列表，使用API判断每只股票是否全面停牌，保留未停牌的股票。
    """
    filtered_list = []
    for stock in stock_list:
        if not is_paused(stock):  # 假设 is_paused 是判断停牌的API函数
            filtered_list.append(stock)
    return filtered_list

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_46.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_48.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_50.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_52.png)

def filter_st(stock_list):
    """
    过滤ST股票。
    遍历股票列表，使用API判断每只股票是否为ST股，保留非ST股票。
    """
    filtered_list = []
    for stock in stock_list:
        if not is_st_stock(stock):  # 假设 is_st_stock 是判断ST股的API函数
            filtered_list.append(stock)
    return filtered_list

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_54.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_56.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_58.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_60.png)

def filter_new(stock_list, days=180):
    """
    过滤上市时间过短的新股。
    遍历股票列表，判断每只股票的上市天数，保留上市天数大于指定阈值（如180天）的股票。
    """
    filtered_list = []
    for stock in stock_list:
        if get_listing_days(stock) > days:  # 假设 get_listing_days 是获取上市天数的函数
            filtered_list.append(stock)
    return filtered_list
```

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_62.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_64.png)

在实际应用中，我们可以链式调用这些函数，对初始股票池进行层层筛选：

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_66.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_68.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_70.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_72.png)

```python
# 假设 all_stocks 是初始获取的所有股票代码列表
filtered_stocks = all_stocks
filtered_stocks = filter_paused(filtered_stocks)  # 第一步：过滤停牌股
filtered_stocks = filter_st(filtered_stocks)      # 第二步：过滤ST股
filtered_stocks = filter_new(filtered_stocks)     # 第三步：过滤新股
# 此时 filtered_stocks 就是经过初步清洗后的股票池
```

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_74.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_76.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_78.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_80.png)

## 查询财务指标数据 📈

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_82.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_84.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_86.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_88.png)

完成股票过滤后，下一步是从这些股票中获取我们策略所需的财务指标。在本例中，我们关注**市净率（PB Ratio）**和**市值（Market Cap）**这两个因子。

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_90.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_92.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_94.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_96.png)

以下是使用`get_fundamentals` API查询数据的示例：

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_98.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_100.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_102.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_104.png)

```python
# 构建查询语句，指定要查询的指标
query = query(
    fundamentals.eod_derivative_indicator.pb_ratio,  # 市净率
    fundamentals.eod_derivative_indicator.market_cap   # 市值
).filter(
    fundamentals.income_statement.stockcode.in_(filtered_stocks)  # 只查询过滤后股票池中的股票
)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_106.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_108.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_110.png)

# 执行查询，获取数据
factor_data = get_fundamentals(query)
```

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_112.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_114.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_116.png)

查询返回的数据`factor_data`通常是一个`DataFrame`，其结构是**指标为列，股票为行**。然而，许多分析模型需要**股票为列，指标为行**的格式。因此，我们需要对数据进行转置操作。

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_118.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_120.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_122.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_124.png)

```python
# 对查询结果进行转置，使行变为股票，列变为指标
factor_data_transposed = factor_data.T

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_126.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_128.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_130.png)

# 处理可能存在的空值（NaN），这里选择直接删除包含空值的行
factor_data_cleaned = factor_data_transposed.dropna()
```

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_132.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_134.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_136.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_138.png)

经过转置和清洗，`factor_data_cleaned`就是一个规整的、可供后续因子分析和选股模型使用的数据表。

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_140.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_142.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_144.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_146.png)

## 总结 🎯

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_148.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_150.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_152.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_154.png)

本节课中我们一起学习了量化策略数据准备阶段的两个核心步骤：

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_156.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_158.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_160.png)

1.  **股票池过滤**：我们编写了三个过滤函数，分别用于剔除停牌股、ST股和上市时间过短的新股，确保后续分析基于一个高质量、可交易的股票池。
2.  **因子数据查询与处理**：我们使用`get_fundamentals` API查询了市净率和市值这两个关键财务指标，并对返回的数据进行了转置和空值处理，将其整理成适合建模的格式。

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_162.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_164.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_166.png)

![](img/f4eca2f6a18bc39fa774556d28a3f9fb_168.png)

通过这两个步骤，我们为接下来的因子分析、排序和构建投资组合打下了坚实的数据基础。在下一节中，我们将学习如何利用这些处理好的因子数据，进行有效的股票筛选和排名。