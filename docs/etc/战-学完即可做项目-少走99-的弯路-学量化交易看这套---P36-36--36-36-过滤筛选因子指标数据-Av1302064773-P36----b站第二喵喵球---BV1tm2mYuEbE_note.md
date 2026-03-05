# 量化交易实战：P36：过滤筛选因子指标数据 📊

![](img/17cb7ed376caa20b8ad400509e595cbb_0.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_2.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_4.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_6.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_8.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_10.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的因子指标数据。这是构建量化策略中非常关键的一步，目的是剔除无效或不符合策略要求的股票，并提取用于后续分析的核心财务数据。

![](img/17cb7ed376caa20b8ad400509e595cbb_12.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_14.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_16.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_18.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现数据的筛选与获取。

![](img/17cb7ed376caa20b8ad400509e595cbb_20.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_22.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_24.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_26.png)

## 第一步：编写股票筛选函数 🧹

![](img/17cb7ed376caa20b8ad400509e595cbb_28.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_30.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_32.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_34.png)

在构建股票池时，我们需要过滤掉一些不符合交易条件的股票，例如停牌股、ST股和上市时间过短的新股。以下是实现这一功能的三个核心筛选函数。

![](img/17cb7ed376caa20b8ad400509e595cbb_36.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_38.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_40.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_42.png)

### 1. 过滤停牌股票
此函数用于判断并剔除处于停牌状态的股票。

![](img/17cb7ed376caa20b8ad400509e595cbb_44.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_46.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_48.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_50.png)

```python
def filter_suspended_stocks(stock_list):
    """
    过滤停牌股票
    :param stock_list: 初始股票列表
    :return: 过滤掉停牌股后的股票列表
    """
    filtered_list = []
    for stock in stock_list:
        # 使用API判断股票是否全年停牌
        if not is_suspended(stock):
            filtered_list.append(stock)
    return filtered_list
```

![](img/17cb7ed376caa20b8ad400509e595cbb_52.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_54.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_56.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_58.png)

### 2. 过滤ST股票
此函数用于判断并剔除被标记为ST（特别处理）的股票。

![](img/17cb7ed376caa20b8ad400509e595cbb_60.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_62.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_64.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_66.png)

```python
def filter_st_stocks(stock_list):
    """
    过滤ST股票
    :param stock_list: 经过初步筛选的股票列表
    :return: 过滤掉ST股后的股票列表
    """
    filtered_list = []
    for stock in stock_list:
        # 使用API判断股票是否为ST股
        if not is_st(stock):
            filtered_list.append(stock)
    return filtered_list
```

![](img/17cb7ed376caa20b8ad400509e595cbb_68.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_70.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_72.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_74.png)

### 3. 过滤新股
此函数用于剔除上市时间过短（例如少于180天）的股票，以确保数据具有足够的统计意义。

![](img/17cb7ed376caa20b8ad400509e595cbb_76.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_78.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_80.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_82.png)

```python
def filter_new_stocks(stock_list, min_days=180):
    """
    过滤上市时间过短的新股
    :param stock_list: 经过前两步筛选的股票列表
    :param min_days: 要求的最小上市天数，默认为180天
    :return: 过滤掉新股后的股票列表
    """
    filtered_list = []
    for stock in stock_list:
        # 获取股票上市天数
        days_listed = get_listed_days(stock)
        # 判断是否满足最小上市天数要求
        if days_listed > min_days:
            filtered_list.append(stock)
    return filtered_list
```

![](img/17cb7ed376caa20b8ad400509e595cbb_84.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_86.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_88.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_90.png)

## 第二步：执行多级筛选流程 🔄

![](img/17cb7ed376caa20b8ad400509e595cbb_92.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_94.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_96.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_98.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_100.png)

编写好筛选函数后，我们需要将它们串联起来，对初始股票池进行逐层过滤。

![](img/17cb7ed376caa20b8ad400509e595cbb_102.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_104.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_106.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_108.png)

以下是执行筛选的步骤：
1.  首先，过滤掉停牌股票。
2.  接着，从剩余股票中过滤掉ST股票。
3.  最后，过滤掉上市时间不足的新股。

![](img/17cb7ed376caa20b8ad400509e595cbb_110.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_112.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_114.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_116.png)

```python
# 假设 initial_stocks 是初始获取的全部股票代码列表
initial_stocks = get_all_stocks()

![](img/17cb7ed376caa20b8ad400509e595cbb_118.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_120.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_122.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_124.png)

# 执行三级过滤
filtered_by_suspension = filter_suspended_stocks(initial_stocks)
filtered_by_st = filter_st_stocks(filtered_by_suspension)
final_stock_list = filter_new_stocks(filtered_by_st, min_days=180)
```

![](img/17cb7ed376caa20b8ad400509e595cbb_126.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_128.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_130.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_132.png)

## 第三步：查询目标因子指标 📈

![](img/17cb7ed376caa20b8ad400509e595cbb_134.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_136.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_138.png)

完成股票筛选后，下一步是从这些合格的股票中查询我们策略所需的因子指标，例如市净率（PB）和市值（Market Cap）。

![](img/17cb7ed376caa20b8ad400509e595cbb_140.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_142.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_144.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_146.png)

以下是查询特定因子数据的核心代码：

![](img/17cb7ed376caa20b8ad400509e595cbb_148.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_150.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_152.png)

```python
# 导入必要的库，假设为量化平台API
import quant_api as qa

![](img/17cb7ed376caa20b8ad400509e595cbb_154.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_156.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_158.png)

def query_factor_data(stock_list):
    """
    查询指定股票列表的因子数据
    :param stock_list: 最终筛选出的股票列表
    :return: 包含市净率和市值的DataFrame
    """
    # 构建查询语句，获取市净率(pb_ratio)和市值(market_cap)
    query = qa.query(
        qa.fundamentals.eod_derivative_indicator.pb_ratio,  # 市净率
        qa.fundamentals.eod_derivative_indicator.market_cap  # 总市值
    ).filter(
        qa.fundamentals.stockcode.in_(stock_list)  # 限定在我们筛选后的股票池中查询
    )
    
    # 执行查询
    factor_df = qa.get_fundamentals(query)
    
    # 对结果进行转置，使每一行代表一只股票，每一列代表一个因子
    factor_df = factor_df.T
    
    # 删除含有空值（NaN）的行，确保数据质量
    factor_df = factor_df.dropna()
    
    return factor_df

![](img/17cb7ed376caa20b8ad400509e595cbb_160.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_162.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_164.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_166.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_168.png)

# 执行查询
factor_data = query_factor_data(final_stock_list)
```

![](img/17cb7ed376caa20b8ad400509e595cbb_170.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_172.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_174.png)

**代码逻辑说明：**
*   `qa.query()` 用于指定要查询的财务指标字段。
*   `.filter()` 用于将查询范围限定在我们筛选后的 `final_stock_list` 中。
*   `get_fundamentals()` 执行查询并返回数据。
*   `.T` 进行矩阵转置，将数据格式转换为更易分析的“股票×因子”形式。
*   `.dropna()` 清除缺失值，避免无效数据影响后续计算。

![](img/17cb7ed376caa20b8ad400509e595cbb_176.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_178.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_180.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_182.png)

---

![](img/17cb7ed376caa20b8ad400509e595cbb_184.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_186.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_188.png)

![](img/17cb7ed376caa20b8ad400509e595cbb_190.png)

本节课中我们一起学习了量化策略数据准备阶段的关键操作。我们首先通过编写三个筛选函数，系统地剔除了停牌股、ST股和上市时间过短的新股，构建了一个干净的初级股票池。随后，我们使用特定的API查询语句，从这个股票池中提取了策略分析所需的市净率和市值等核心因子指标，并对数据进行了转置和清洗，为下一步的因子分析和选股策略打下了坚实的基础。