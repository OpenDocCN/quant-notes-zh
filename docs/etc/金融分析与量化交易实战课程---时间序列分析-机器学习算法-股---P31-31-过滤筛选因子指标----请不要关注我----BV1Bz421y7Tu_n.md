# 人生苦短，我学量化！：P31：过滤筛选因子指标数据

![](img/ab5f26bed880559e20fa23df9a408816_0.png)

![](img/ab5f26bed880559e20fa23df9a408816_2.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的财务指标数据。这是构建量化策略中非常关键的一步，目的是剔除无效或不符合策略逻辑的股票，并准备好用于后续分析的数据。

## 概述：为何需要筛选股票？

![](img/ab5f26bed880559e20fa23df9a408816_4.png)

![](img/ab5f26bed880559e20fa23df9a408816_6.png)

在构建股票策略时，我们通常不会使用市场上所有的股票。例如，停牌的股票无法交易，ST股风险较高，刚上市的新股数据不稳定。因此，我们需要编写函数来过滤掉这些我们不希望纳入策略的股票。

![](img/ab5f26bed880559e20fa23df9a408816_8.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何具体实现股票的筛选和指标获取。

![](img/ab5f26bed880559e20fa23df9a408816_10.png)

![](img/ab5f26bed880559e20fa23df9a408816_12.png)

## 第一步：编写股票过滤函数

![](img/ab5f26bed880559e20fa23df9a408816_14.png)

我们将编写三个独立的函数，分别用于过滤停牌股、ST股和新股。以下是实现这些过滤功能的具体步骤。

![](img/ab5f26bed880559e20fa23df9a408816_16.png)

![](img/ab5f26bed880559e20fa23df9a408816_18.png)

### 1. 过滤停牌股

![](img/ab5f26bed880559e20fa23df9a408816_20.png)

首先，我们判断股票是否全天停牌。如果停牌，则将其从股票池中剔除。

![](img/ab5f26bed880559e20fa23df9a408816_22.png)

![](img/ab5f26bed880559e20fa23df9a408816_24.png)

```python
def filter_suspended_stocks(stock_list):
    """
    过滤掉停牌的股票。
    :param stock_list: 初始股票代码列表
    :return: 过滤掉停牌股后的股票代码列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API判断股票是否全天停牌
        if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API函数
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/ab5f26bed880559e20fa23df9a408816_26.png)

**核心概念**：函数 `is_suspended(stock)` 是一个假设的API调用，用于返回某只股票是否处于全天停牌状态。我们遍历股票列表，只保留未停牌的股票。

![](img/ab5f26bed880559e20fa23df9a408816_28.png)

![](img/ab5f26bed880559e20fa23df9a408816_30.png)

### 2. 过滤ST股

![](img/ab5f26bed880559e20fa23df9a408816_32.png)

![](img/ab5f26bed880559e20fa23df9a408816_34.png)

接下来，我们过滤掉被标记为ST（特别处理）的股票，这类股票通常存在较高风险。

![](img/ab5f26bed880559e20fa23df9a408816_36.png)

```python
def filter_st_stocks(stock_list):
    """
    过滤掉ST股票。
    :param stock_list: 经过停牌过滤后的股票代码列表
    :return: 过滤掉ST股后的股票代码列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 使用API判断股票是否为ST股
        if not is_st(stock):  # 假设 is_st 是判断ST股的API函数
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/ab5f26bed880559e20fa23df9a408816_38.png)

![](img/ab5f26bed880559e20fa23df9a408816_40.png)

**核心概念**：函数 `is_st(stock)` 用于判断股票是否为ST股。逻辑与过滤停牌股类似。

![](img/ab5f26bed880559e20fa23df9a408816_42.png)

### 3. 过滤新股

![](img/ab5f26bed880559e20fa23df9a408816_44.png)

![](img/ab5f26bed880559e20fa23df9a408816_46.png)

最后，我们过滤掉上市时间过短的新股，以确保我们有足够的历史数据进行分析。例如，我们可以设定上市天数需大于180天。

![](img/ab5f26bed880559e20fa23df9a408816_48.png)

![](img/ab5f26bed880559e20fa23df9a408816_50.png)

```python
def filter_new_stocks(stock_list, min_days=180):
    """
    过滤掉上市时间过短的新股。
    :param stock_list: 经过前两步过滤后的股票代码列表
    :param min_days: 要求的最小上市天数，默认为180天
    :return: 过滤掉新股后的股票代码列表
    """
    filtered_stocks = []
    for stock in stock_list:
        # 获取股票上市以来的天数
        days_listed = get_days_listed(stock)  # 假设 get_days_listed 是获取上市天数的API函数
        if days_listed > min_days:
            filtered_stocks.append(stock)
    return filtered_stocks
```

**核心概念**：函数 `get_days_listed(stock)` 返回股票已上市的天数。我们通过比较该天数与设定的阈值（如180天）来筛选股票。

![](img/ab5f26bed880559e20fa23df9a408816_52.png)

## 第二步：串联执行过滤流程

![](img/ab5f26bed880559e20fa23df9a408816_54.png)

![](img/ab5f26bed880559e20fa23df9a408816_56.png)

编写好三个过滤函数后，我们需要将它们串联起来，对初始股票池进行逐层筛选。

![](img/ab5f26bed880559e20fa23df9a408816_58.png)

![](img/ab5f26bed880559e20fa23df9a408816_60.png)

以下是串联过滤的执行流程：

![](img/ab5f26bed880559e20fa23df9a408816_62.png)

![](img/ab5f26bed880559e20fa23df9a408816_64.png)

1.  首先，使用 `filter_suspended_stocks` 函数过滤停牌股。
2.  接着，将上一步的结果传入 `filter_st_stocks` 函数过滤ST股。
3.  最后，将第二步的结果传入 `filter_new_stocks` 函数过滤新股。

![](img/ab5f26bed880559e20fa23df9a408816_66.png)

![](img/ab5f26bed880559e20fa23df9a408816_68.png)

```python
# 假设 initial_stocks 是初始获取的所有股票代码列表
initial_stocks = ['000001.XSHE', '600519.XSHG', ...]  # 示例股票代码

![](img/ab5f26bed880559e20fa23df9a408816_70.png)

# 第一步：过滤停牌股
filtered_by_suspension = filter_suspended_stocks(initial_stocks)

![](img/ab5f26bed880559e20fa23df9a408816_72.png)

# 第二步：过滤ST股
filtered_by_st = filter_st_stocks(filtered_by_suspension)

![](img/ab5f26bed880559e20fa23df9a408816_74.png)

![](img/ab5f26bed880559e20fa23df9a408816_76.png)

# 第三步：过滤新股
final_stock_list = filter_new_stocks(filtered_by_st, min_days=180)

![](img/ab5f26bed880559e20fa23df9a408816_78.png)

print(f"最终筛选后的股票数量：{len(final_stock_list)}")
```

![](img/ab5f26bed880559e20fa23df9a408816_80.png)

![](img/ab5f26bed880559e20fa23df9a408816_82.png)

通过以上三步，我们就得到了一个经过初步清洗、更适合进行量化分析的股票池。

![](img/ab5f26bed880559e20fa23df9a408816_84.png)

## 第三步：获取财务指标数据

![](img/ab5f26bed880559e20fa23df9a408816_86.png)

![](img/ab5f26bed880559e20fa23df9a408816_88.png)

得到最终的股票列表后，下一步就是获取这些股票的财务指标数据，例如市盈率（PE）和市值（Market Cap）。

![](img/ab5f26bed880559e20fa23df9a408816_90.png)

以下是查询指标数据的步骤：

![](img/ab5f26bed880559e20fa23df9a408816_92.png)

![](img/ab5f26bed880559e20fa23df9a408816_94.png)

1.  使用 `get_fundamentals` 函数，并编写查询语句（Query）来指定需要获取的指标。
2.  在查询中，通过 `filter` 条件限定只查询我们最终股票列表中的股票。
3.  对查询结果进行转置和清洗，使其符合分析所需的格式（股票为行，指标为列）。

![](img/ab5f26bed880559e20fa23df9a408816_96.png)

```python
# 导入必要的库，假设已安装
from kuanke import get_fundamentals  # 示例，实际API可能不同

![](img/ab5f26bed880559e20fa23df9a408816_98.png)

![](img/ab5f26bed880559e20fa23df9a408816_100.png)

# 构建查询语句，获取市盈率(pe_ratio)和市值(market_cap)
query = """
    select
        code,
        pe_ratio,
        market_cap
    from
        fundamentals
    where
        code in {}
""".format(tuple(final_stock_list))  # 将股票列表转换为SQL查询中的元组格式

![](img/ab5f26bed880559e20fa23df9a408816_102.png)

# 执行查询
df_data = get_fundamentals(query)

![](img/ab5f26bed880559e20fa23df9a408816_104.png)

# 数据清洗与转换
# 1. 设置股票代码为索引
df_data.set_index('code', inplace=True)
# 2. 转置数据框，使股票为行，指标为列 (如果需要)
# df_data = df_data.T
# 3. 删除含有空值(NaN)的行
df_data_clean = df_data.dropna()

![](img/ab5f26bed880559e20fa23df9a408816_106.png)

![](img/ab5f26bed880559e20fa23df9a408816_108.png)

print(df_data_clean.head())
```

![](img/ab5f26bed880559e20fa23df9a408816_110.png)

![](img/ab5f26bed880559e20fa23df9a408816_112.png)

**核心概念**：
*   `get_fundamentals(query)`：这是一个假设的API函数，用于执行财务数据查询。
*   `df.dropna()`：Pandas库中的函数，用于删除包含空值的行，确保数据完整性。
*   数据转置：通过 `.T` 属性可以轻松实现DataFrame的行列转置，以适应不同的分析需求。

![](img/ab5f26bed880559e20fa23df9a408816_114.png)

## 总结

![](img/ab5f26bed880559e20fa23df9a408816_116.png)

本节课中我们一起学习了量化策略中数据准备的关键步骤。

![](img/ab5f26bed880559e20fa23df9a408816_118.png)

![](img/ab5f26bed880559e20fa23df9a408816_120.png)

首先，我们编写了三个过滤函数，用于从股票池中剔除**停牌股**、**ST股**和**上市时间过短的新股**，从而得到一个更干净、更稳定的备选股票集合。

![](img/ab5f26bed880559e20fa23df9a408816_122.png)

接着，我们使用 `get_fundamentals` 函数查询了这些股票的特定财务指标（如市盈率和市值），并对查询结果进行了简单的数据清洗（删除空值）和格式转换，为后续的因子分析和策略构建打下了坚实的数据基础。

![](img/ab5f26bed880559e20fa23df9a408816_124.png)

![](img/ab5f26bed880559e20fa23df9a408816_126.png)

通过本课的学习，你已经掌握了量化分析中预处理股票数据的基本方法。在接下来的课程中，我们将利用这些处理好的数据，进行更深入的因子分析和策略回测。