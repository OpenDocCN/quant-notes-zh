# Python金融分析与量化交易：P36：过滤筛选因子指标数据 📊

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_0.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_2.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的因子指标数据。这是构建量化交易策略中非常关键的一步，目的是剔除无效或不符合条件的股票，并提取用于后续分析的财务数据。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_4.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_6.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现股票池的筛选和因子数据的获取。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_8.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_10.png)

## 股票池初步筛选 🧹

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_12.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_14.png)

在构建股票池时，我们通常需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一筛选过程的步骤。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_16.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_18.png)

首先，我们需要编写几个过滤函数。这些函数将接收一个股票列表作为输入，并返回过滤后的列表。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_20.png)

### 1. 过滤停牌股票

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_22.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_24.png)

停牌的股票无法进行交易，因此需要被剔除。我们可以使用API提供的函数来判断股票是否全天停牌。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_26.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_28.png)

```python
def filter_suspended_stocks(stock_list):
    """
    过滤掉停牌的股票。
    :param stock_list: 初始股票列表
    :return: 过滤掉停牌股后的股票列表
    """
    filtered_list = []
    for stock in stock_list:
        # 判断股票是否全天停牌
        if not is_suspended(stock):
            filtered_list.append(stock)
    return filtered_list
```

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_30.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_32.png)

### 2. 过滤ST股票

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_34.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_36.png)

ST股票通常存在较高风险，许多策略会选择避开它们。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_38.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_40.png)

```python
def filter_st_stocks(stock_list):
    """
    过滤掉ST股票。
    :param stock_list: 经过停牌过滤后的股票列表
    :return: 过滤掉ST股后的股票列表
    """
    filtered_list = []
    for stock in stock_list:
        # 判断股票是否为ST股
        if not is_st(stock):
            filtered_list.append(stock)
    return filtered_list
```

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_42.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_44.png)

### 3. 过滤新股

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_46.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_48.png)

刚上市的新股价格波动可能较大，且历史数据不足，因此也常常被过滤掉。这里我们假设过滤上市天数少于180天的股票。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_50.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_52.png)

```python
def filter_new_stocks(stock_list):
    """
    过滤掉上市时间过短的新股。
    :param stock_list: 经过ST过滤后的股票列表
    :return: 过滤掉新股后的股票列表
    """
    filtered_list = []
    for stock in stock_list:
        # 判断股票上市天数是否大于180天
        if get_listed_days(stock) > 180:
            filtered_list.append(stock)
    return filtered_list
```

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_54.png)

现在，我们可以按顺序应用这些过滤函数，对初始股票池进行层层筛选。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_56.png)

```python
# 假设 initial_stocks 是初始的股票代码列表
initial_stocks = ['000001.XSHE', '600000.XSHG', ...]

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_58.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_60.png)

# 第一步：过滤停牌股
stocks_no_suspended = filter_suspended_stocks(initial_stocks)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_62.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_64.png)

# 第二步：过滤ST股
stocks_no_st = filter_st_stocks(stocks_no_suspended)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_66.png)

# 第三步：过滤新股
final_stock_pool = filter_new_stocks(stocks_no_st)
```

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_68.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_70.png)

通过以上步骤，我们得到了一个经过初步筛选的、相对“干净”的股票池。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_72.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_74.png)

## 查询因子指标数据 📈

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_76.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_78.png)

在得到最终的股票池后，下一步是获取我们策略所需的因子指标数据，例如市盈率（PE）和市值（Market Cap）。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_80.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_82.png)

我们将使用 `get_fundamentals` 函数来查询这些数据。该函数需要指定查询的指标（query）和要查询的股票范围（filter）。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_84.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_86.png)

以下是查询市盈率和市值的代码示例：

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_88.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_90.png)

```python
# 导入必要的库（假设环境已配置）
# from jqdata import get_fundamentals

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_92.png)

# 定义查询语句，获取市盈率(pe_ratio)和市值(market_cap)
query = query(
    fundamentals.eod_derivative_indicator.pe_ratio,
    fundamentals.eod_derivative_indicator.market_cap
)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_94.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_96.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_98.png)

# 在查询中过滤，只查询我们最终股票池中的股票
# 注意：这里假设 final_stock_pool 是一个包含股票代码的列表
filter_condition = fundamentals.stockcode.in_(final_stock_pool)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_100.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_102.png)

# 执行查询
factor_data = get_fundamentals(query, filter=filter_condition)
```

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_104.png)

查询返回的数据 `factor_data` 通常是一个 `DataFrame`，其结构可能不符合我们的预期（股票在列，指标在行）。我们需要对其进行转置，并处理可能存在的空值。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_106.png)

```python
# 对数据进行转置，使每一行代表一只股票，每一列代表一个指标
factor_data_transposed = factor_data.T

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_108.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_110.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_112.png)

# 删除包含空值的行（简单处理）
factor_data_clean = factor_data_transposed.dropna()
```

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_114.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_116.png)

现在，`factor_data_clean` 就是一个整洁的数据框，包含了我们筛选后股票的市盈率和市值数据，可以用于后续的因子分析和策略构建。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_118.png)

## 总结 🎯

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_120.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_122.png)

本节课中我们一起学习了量化策略中两个重要的预处理步骤：

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_124.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_126.png)

1.  **股票池筛选**：我们编写了三个过滤函数，分别用于剔除停牌股、ST股和上市时间过短的新股，从而得到一个更高质量、更适合交易的股票池。
2.  **因子数据获取**：我们使用 `get_fundamentals` 函数，通过指定查询指标和股票范围，获取了所需的财务因子数据（如市盈率和市值），并对数据进行了转置和清洗，使其格式便于后续分析。

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_128.png)

![](img/a7edf11f145ad7a2f2ac026e5bd86bfa_130.png)

这些步骤是连接数据获取与策略逻辑的桥梁，确保了后续分析所基于的数据是有效且可靠的。在下一节课中，我们将学习如何利用这些清洗后的因子数据来构建具体的选股策略。