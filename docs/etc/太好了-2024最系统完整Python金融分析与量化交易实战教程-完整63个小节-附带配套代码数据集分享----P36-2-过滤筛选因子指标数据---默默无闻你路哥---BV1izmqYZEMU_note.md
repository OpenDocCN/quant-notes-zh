# Python金融分析与量化交易实战：P36：2-过滤筛选因子指标数据 📊

![](img/e6acbf557527ac5b4ea665bd3b192b03_0.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_2.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_4.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_6.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的财务因子指标数据。这是构建量化交易策略中非常关键的一步，目的是剔除无效或不符合策略要求的股票，并提取有效的分析数据。

![](img/e6acbf557527ac5b4ea665bd3b192b03_8.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_10.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_12.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_14.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现股票的筛选和因子数据的获取。

![](img/e6acbf557527ac5b4ea665bd3b192b03_16.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_18.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_20.png)

## 过滤不想要的股票 🚫

![](img/e6acbf557527ac5b4ea665bd3b192b03_22.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_24.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_26.png)

在构建股票池时，我们需要排除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一功能的步骤：

![](img/e6acbf557527ac5b4ea665bd3b192b03_28.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_30.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_32.png)

首先，我们需要编写几个辅助函数来分别过滤掉这些股票。

![](img/e6acbf557527ac5b4ea665bd3b192b03_34.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_36.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_38.png)

### 1. 过滤停牌股票
停牌的股票无法进行交易，因此需要被剔除。我们可以使用API提供的函数来判断股票是否全年停牌。

![](img/e6acbf557527ac5b4ea665bd3b192b03_40.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_42.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_44.png)

```python
def filter_suspended_stocks(stock_list):
    """
    过滤掉停牌的股票。
    :param stock_list: 初始股票列表
    :return: 过滤掉停牌股后的股票列表
    """
    filtered_list = []
    for stock in stock_list:
        # 判断股票是否全年停牌
        if not is_suspended(stock):
            filtered_list.append(stock)
    return filtered_list
```

![](img/e6acbf557527ac5b4ea665bd3b192b03_46.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_48.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_50.png)

### 2. 过滤ST股票
ST股通常存在较高风险，许多策略会选择避开它们。

![](img/e6acbf557527ac5b4ea665bd3b192b03_52.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_54.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_56.png)

```python
def filter_st_stocks(stock_list):
    """
    过滤掉ST股票。
    :param stock_list: 经过初步筛选的股票列表
    :return: 过滤掉ST股后的股票列表
    """
    filtered_list = []
    for stock in stock_list:
        # 判断股票是否为ST股
        if not is_st(stock):
            filtered_list.append(stock)
    return filtered_list
```

![](img/e6acbf557527ac5b4ea665bd3b192b03_58.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_60.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_62.png)

### 3. 过滤新股
刚上市不久的股票，其价格和交易数据可能不稳定，通常也需要被过滤掉。这里我们假设上市天数少于180天的股票为新股。

![](img/e6acbf557527ac5b4ea665bd3b192b03_64.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_66.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_68.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_70.png)

```python
def filter_new_stocks(stock_list):
    """
    过滤掉上市时间过短的新股。
    :param stock_list: 经过前两步筛选的股票列表
    :return: 过滤掉新股后的股票列表
    """
    filtered_list = []
    for stock in stock_list:
        # 判断股票上市天数是否大于180天
        if days_from_listed(stock) > 180:
            filtered_list.append(stock)
    return filtered_list
```

![](img/e6acbf557527ac5b4ea665bd3b192b03_72.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_74.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_76.png)

## 执行多步过滤 🔄

![](img/e6acbf557527ac5b4ea665bd3b192b03_78.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_80.png)

编写好过滤函数后，我们需要按顺序对初始股票池执行这些过滤操作。

![](img/e6acbf557527ac5b4ea665bd3b192b03_82.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_84.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_86.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_88.png)

```python
# 假设 initial_stocks 是初始获取的所有股票代码列表
initial_stocks = get_all_stocks()

![](img/e6acbf557527ac5b4ea665bd3b192b03_90.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_92.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_94.png)

# 第一步：过滤停牌股
filtered_stocks = filter_suspended_stocks(initial_stocks)

![](img/e6acbf557527ac5b4ea665bd3b192b03_96.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_98.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_100.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_102.png)

# 第二步：过滤ST股
filtered_stocks = filter_st_stocks(filtered_stocks)

![](img/e6acbf557527ac5b4ea665bd3b192b03_104.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_106.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_108.png)

# 第三步：过滤新股
final_stocks = filter_new_stocks(filtered_stocks)
```

![](img/e6acbf557527ac5b4ea665bd3b192b03_110.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_112.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_114.png)

经过以上三步，`final_stocks` 列表中就包含了我们策略真正想要分析的、符合条件的股票。

![](img/e6acbf557527ac5b4ea665bd3b192b03_116.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_118.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_120.png)

## 查询所需的因子指标 📈

![](img/e6acbf557527ac5b4ea665bd3b192b03_122.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_124.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_126.png)

股票池确定后，下一步是获取这些股票的具体财务指标数据。在本例中，我们关注**市净率（PB）**和**市值（Market Cap）**这两个因子。

![](img/e6acbf557527ac5b4ea665bd3b192b03_128.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_130.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_132.png)

以下是查询这些指标数据的代码：

![](img/e6acbf557527ac5b4ea665bd3b192b03_134.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_136.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_138.png)

```python
# 导入必要的库，假设使用 get_fundamentals 函数来查询基本面数据
from quant_api import get_fundamentals

![](img/e6acbf557527ac5b4ea665bd3b192b03_140.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_142.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_144.png)

# 构建查询语句，查询市净率(pb_ratio)和市值(market_cap)
query = query(
    fundamentals.pb_ratio,
    fundamentals.market_cap
)

![](img/e6acbf557527ac5b4ea665bd3b192b03_146.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_148.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_150.png)

# 执行查询，并指定只查询我们最终筛选出的股票
# 注意：这里假设 final_stocks 是股票代码列表
factor_data = get_fundamentals(query, filter=fundamentals.code.in_(final_stocks))
```

![](img/e6acbf557527ac5b4ea665bd3b192b03_152.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_154.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_156.png)

## 处理查询结果 🔧

![](img/e6acbf557527ac5b4ea665bd3b192b03_158.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_160.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_162.png)

直接查询返回的数据格式可能不是我们想要的（通常是横表）。为了便于后续分析，我们通常需要将其转换为以股票为行的竖表格式，并处理可能存在的空值。

![](img/e6acbf557527ac5b4ea665bd3b192b03_164.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_166.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_168.png)

```python
# 对数据进行转置，使每一行代表一只股票
factor_data_transposed = factor_data.T

![](img/e6acbf557527ac5b4ea665bd3b192b03_170.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_172.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_174.png)

# 删除包含空值的行，确保数据完整性
factor_data_clean = factor_data_transposed.dropna()
```

![](img/e6acbf557527ac5b4ea665bd3b192b03_176.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_178.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_180.png)

现在，`factor_data_clean` 就是一个干净、结构化的 `DataFrame`，其中每一行对应一只股票，列则包含其市净率和市值数据，可以直接用于后续的因子分析和选股策略。

![](img/e6acbf557527ac5b4ea665bd3b192b03_182.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_184.png)

![](img/e6acbf557527ac5b4ea665bd3b192b03_186.png)

本节课中我们一起学习了如何对股票池进行多条件筛选，以及如何查询和处理特定的财务因子数据。这是连接数据获取与策略逻辑的核心环节，为后续的因子打分和股票排序奠定了坚实的基础。