# Python金融量化+股票交易：P36：过滤筛选因子指标数据 📊

![](img/176178ea1afbc1d4c4b0554db1dd0de9_0.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_2.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_4.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的财务指标数据。这是构建量化交易策略中非常关键的一步，目的是剔除无效或不符合策略要求的股票，并提取用于后续分析的因子数据。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_6.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_8.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_10.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现数据的过滤与获取。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_12.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_14.png)

## 过滤不想要的股票 🚫

![](img/176178ea1afbc1d4c4b0554db1dd0de9_16.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_18.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_20.png)

在构建股票池时，我们通常需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一功能的步骤。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_22.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_24.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_26.png)

首先，我们需要编写一个过滤函数，用于剔除停牌股票。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_28.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_30.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_32.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_34.png)

```python
def filter_suspended_stocks(stock_list):
    """
    过滤掉停牌的股票。
    :param stock_list: 初始股票列表
    :return: 过滤后的股票列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API判断股票是否全年停牌
        if not is_suspended(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/176178ea1afbc1d4c4b0554db1dd0de9_36.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_38.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_40.png)

接下来，我们还需要过滤掉ST股。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_42.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_44.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_46.png)

```python
def filter_st_stocks(stock_list):
    """
    过滤掉ST股。
    :param stock_list: 经过停牌过滤后的股票列表
    :return: 进一步过滤后的股票列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API判断股票是否为ST股
        if not is_st(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/176178ea1afbc1d4c4b0554db1dd0de9_48.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_50.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_52.png)

最后，我们需要过滤掉上市时间过短的新股。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_54.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_56.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_58.png)

```python
def filter_new_stocks(stock_list, min_days=180):
    """
    过滤掉上市时间少于指定天数的股票。
    :param stock_list: 经过前两步过滤的股票列表
    :param min_days: 要求的最小上市天数，默认为180天
    :return: 最终过滤后的股票列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 获取股票上市天数
        days_listed = get_days_listed(stock)
        if days_listed > min_days:
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/176178ea1afbc1d4c4b0554db1dd0de9_60.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_62.png)

现在，我们可以按顺序应用这三个过滤函数，得到我们想要的股票池。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_64.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_66.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_68.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_70.png)

```python
# 假设 initial_stocks 是初始获取的所有股票列表
filtered_by_suspension = filter_suspended_stocks(initial_stocks)
filtered_by_st = filter_st_stocks(filtered_by_suspension)
final_stock_pool = filter_new_stocks(filtered_by_st, min_days=180)
```

![](img/176178ea1afbc1d4c4b0554db1dd0de9_72.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_74.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_76.png)

## 查询所需的指标数据 📈

![](img/176178ea1afbc1d4c4b0554db1dd0de9_78.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_80.png)

在得到干净的股票池后，下一步是获取用于量化分析的财务指标。本节中，我们将查询市净率（PB）和市值（Market Cap）这两个常用因子。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_82.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_84.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_86.png)

以下是查询指标数据的代码实现。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_88.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_90.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_92.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_94.png)

```python
# 使用 get_fundamentals 函数查询财务数据
# 查询市净率（pb_ratio）和市值（market_cap）
query_statement = query(
    fundamentals.eod_derivative_indicator.pb_ratio,
    fundamentals.eod_derivative_indicator.market_cap
)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_96.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_98.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_100.png)

# 将查询范围限定在我们最终过滤得到的股票池中
# 假设 final_stock_pool 是股票代码的列表
data = get_fundamentals(query_statement, entry_date='2023-01-01', interval='1d')
```

![](img/176178ea1afbc1d4c4b0554db1dd0de9_102.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_104.png)

查询返回的数据通常是一个`DataFrame`，其行是股票代码，列是指标。但为了后续分析方便，我们通常需要对其进行转置，并处理可能存在的空值。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_106.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_108.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_110.png)

```python
# 转置DataFrame，使每一行对应一只股票，每一列对应一个指标
transposed_data = data.T

![](img/176178ea1afbc1d4c4b0554db1dd0de9_112.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_114.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_116.png)

# 删除包含空值的行
cleaned_data = transposed_data.dropna()
```

![](img/176178ea1afbc1d4c4b0554db1dd0de9_118.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_120.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_122.png)

经过以上操作，我们就得到了一个干净、结构化的数据集，其中包含了我们感兴趣的股票及其对应的财务指标。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_124.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_126.png)

## 总结 ✨

![](img/176178ea1afbc1d4c4b0554db1dd0de9_128.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_130.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_132.png)

本节课中我们一起学习了量化策略中数据准备阶段的两个核心步骤。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_134.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_136.png)

首先，我们通过编写过滤函数，从原始股票池中剔除了停牌股、ST股和上市时间过短的新股，得到了一个符合基本交易条件的股票池。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_138.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_140.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_142.png)

其次，我们使用`get_fundamentals`函数查询了所需的财务指标（市净率和市值），并对查询结果进行了转置和空值处理，最终得到了可用于后续因子分析和策略回测的结构化数据。

![](img/176178ea1afbc1d4c4b0554db1dd0de9_144.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_146.png)

![](img/176178ea1afbc1d4c4b0554db1dd0de9_148.png)

掌握数据的过滤与获取是构建有效量化模型的基础，它为后续的因子计算、组合构建和绩效评估提供了可靠的数据支持。