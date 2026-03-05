# Python金融量化：P36：过滤筛选因子指标数据 📊

![](img/9ff21e3ef75a05c81344a57b9c474c15_0.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_2.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_4.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_6.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_8.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的财务指标数据。这是构建量化交易策略中非常关键的一步，它帮助我们剔除无效或不符合策略要求的股票，并聚焦于核心数据。

![](img/9ff21e3ef75a05c81344a57b9c474c15_10.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_12.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_14.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现股票数据的筛选与指标获取。

![](img/9ff21e3ef75a05c81344a57b9c474c15_16.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_18.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_20.png)

## 股票池初步筛选 🧹

![](img/9ff21e3ef75a05c81344a57b9c474c15_22.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_24.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_26.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_28.png)

在构建策略时，我们通常不希望将停牌股、ST股或刚上市的新股纳入考虑范围。因此，我们需要编写函数来过滤掉这些股票。

![](img/9ff21e3ef75a05c81344a57b9c474c15_30.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_32.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_34.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_36.png)

以下是实现股票筛选的三个核心函数：

![](img/9ff21e3ef75a05c81344a57b9c474c15_38.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_40.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_42.png)

```python
def filter_paused(stock_list):
    """
    过滤停牌股票。
    遍历股票列表，使用API判断每只股票是否全面停牌，保留未停牌的股票。
    """
    filtered_stocks = []
    for stock in stock_list:
        if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API
            filtered_stocks.append(stock)
    return filtered_stocks

![](img/9ff21e3ef75a05c81344a57b9c474c15_44.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_46.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_48.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_50.png)

def filter_st(stock_list):
    """
    过滤ST股票。
    遍历股票列表，使用API判断每只股票是否为ST股，保留非ST股票。
    """
    filtered_stocks = []
    for stock in stock_list:
        if not is_st(stock):  # 假设 is_st 是判断ST股的API
            filtered_stocks.append(stock)
    return filtered_stocks

![](img/9ff21e3ef75a05c81344a57b9c474c15_52.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_54.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_56.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_58.png)

def filter_new(stock_list, days=180):
    """
    过滤上市时间过短的新股。
    遍历股票列表，判断每只股票的上市天数，保留上市超过指定天数的股票。
    """
    filtered_stocks = []
    for stock in stock_list:
        if get_listing_days(stock) > days:  # 假设 get_listing_days 是获取上市天数的API
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/9ff21e3ef75a05c81344a57b9c474c15_60.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_62.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_64.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_66.png)

## 执行筛选流程 🔄

![](img/9ff21e3ef75a05c81344a57b9c474c15_68.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_70.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_72.png)

定义了筛选函数后，我们需要按顺序对初始股票池应用这些过滤器。

![](img/9ff21e3ef75a05c81344a57b9c474c15_74.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_76.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_78.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_80.png)

以下是执行筛选的步骤：

![](img/9ff21e3ef75a05c81344a57b9c474c15_82.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_84.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_86.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_88.png)

1.  **获取初始股票池**：首先获得所有待考虑的股票代码列表。
2.  **应用停牌过滤器**：调用 `filter_paused` 函数，剔除停牌股票。
3.  **应用ST股过滤器**：对上一步的结果调用 `filter_st` 函数，剔除ST股票。
4.  **应用新股过滤器**：对上一步的结果调用 `filter_new` 函数，剔除上市时间过短的股票。

![](img/9ff21e3ef75a05c81344a57b9c474c15_90.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_92.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_94.png)

经过以上步骤，我们最终得到一个经过初步净化的、符合基本交易条件的股票池。

![](img/9ff21e3ef75a05c81344a57b9c474c15_96.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_98.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_100.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_102.png)

## 查询财务指标数据 📈

![](img/9ff21e3ef75a05c81344a57b9c474c15_104.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_106.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_108.png)

获得筛选后的股票池后，下一步是获取这些股票的关键财务指标，用于后续的因子分析和选股。

![](img/9ff21e3ef75a05c81344a57b9c474c15_110.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_112.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_114.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_116.png)

以下是使用 `get_fundamentals` API查询指标数据的示例：

![](img/9ff21e3ef75a05c81344a57b9c474c15_118.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_120.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_122.png)

```python
# 假设 filtered_stocks 是经过上述步骤筛选后的股票代码列表
query_statement = query(
    fundamentals.eod_derivative_indicator.pb_ratio,  # 市净率 (P/B Ratio)
    fundamentals.eod_derivative_indicator.market_cap  # 总市值 (Market Capitalization)
).filter(
    fundamentals.income_statement.code.in_(filtered_stocks)  # 仅查询筛选后股票池中的股票
)

![](img/9ff21e3ef75a05c81344a57b9c474c15_124.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_126.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_128.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_130.png)

# 执行查询
factor_data = get_fundamentals(query_statement)

![](img/9ff21e3ef75a05c81344a57b9c474c15_132.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_134.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_136.png)

# 数据整理：通常返回的数据需要转置，并处理缺失值
factor_data = factor_data.T  # 转置操作，使行代表股票，列代表指标
factor_data = factor_data.dropna()  # 删除包含空值的行
```

![](img/9ff21e3ef75a05c81344a57b9c474c15_138.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_140.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_142.png)

在这段代码中：
*   `fundamentals.eod_derivative_indicator.pb_ratio` 代表市净率因子。
*   `fundamentals.eod_derivative_indicator.market_cap` 代表市值因子。
*   `.filter(...)` 方法确保了只查询我们筛选后股票池中的数据。
*   查询结果 `factor_data` 通常需要进行转置（`.T`）和清理缺失值（`.dropna()`）操作，以得到便于分析的数据格式。

![](img/9ff21e3ef75a05c81344a57b9c474c15_144.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_146.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_148.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_150.png)

## 总结 📝

![](img/9ff21e3ef75a05c81344a57b9c474c15_152.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_154.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_156.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_158.png)

本节课中我们一起学习了量化策略数据准备阶段的两个核心操作。

![](img/9ff21e3ef75a05c81344a57b9c474c15_160.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_162.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_164.png)

首先，我们通过编写**过滤函数**，系统地剔除了**停牌股**、**ST股**和**新股**，得到了一个高质量的初始股票池。其次，我们使用 `get_fundamentals` 接口，针对这个股票池查询了所需的**财务指标**（如市净率和市值），并对返回的数据进行了必要的整理（转置和清理缺失值）。

![](img/9ff21e3ef75a05c81344a57b9c474c15_166.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_168.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_170.png)

![](img/9ff21e3ef75a05c81344a57b9c474c15_172.png)

这些步骤为后续的因子分析、模型构建和策略回测奠定了坚实的数据基础。