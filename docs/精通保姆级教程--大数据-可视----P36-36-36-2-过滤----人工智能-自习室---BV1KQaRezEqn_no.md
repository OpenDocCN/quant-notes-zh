# Python金融量化交易：P36：过滤筛选因子指标数据 📊

![](img/256446a5e615f65a1489056637e9f633_0.png)

![](img/256446a5e615f65a1489056637e9f633_2.png)

在本节课中，我们将学习如何对股票池进行过滤和筛选，以剔除不符合交易条件的股票（如停牌股、ST股、新股），并获取我们所需的财务指标数据（如市盈率、市值），为后续的量化策略分析做准备。

![](img/256446a5e615f65a1489056637e9f633_4.png)

![](img/256446a5e615f65a1489056637e9f633_6.png)

![](img/256446a5e615f65a1489056637e9f633_8.png)

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何在实际操作中清洗和准备数据。

![](img/256446a5e615f65a1489056637e9f633_10.png)

![](img/256446a5e615f65a1489056637e9f633_12.png)

## 过滤不想要的股票 🚫

![](img/256446a5e615f65a1489056637e9f633_14.png)

![](img/256446a5e615f65a1489056637e9f633_16.png)

![](img/256446a5e615f65a1489056637e9f633_18.png)

在构建股票池时，我们首先需要剔除一些不适合交易的股票。以下是需要过滤的几种情况：

![](img/256446a5e615f65a1489056637e9f633_20.png)

![](img/256446a5e615f65a1489056637e9f633_22.png)

*   **停牌股票**：无法进行买卖交易。
*   **ST/S\*ST股票**：存在退市风险或其他特别处理的股票。
*   **新上市股票**：上市时间过短，数据不充分，价格波动可能异常。

![](img/256446a5e615f65a1489056637e9f633_24.png)

![](img/256446a5e615f65a1489056637e9f633_26.png)

我们将编写三个独立的过滤函数来处理这些情况。

![](img/256446a5e615f65a1489056637e9f633_28.png)

![](img/256446a5e615f65a1489056637e9f633_30.png)

![](img/256446a5e615f65a1489056637e9f633_32.png)

### 1. 过滤停牌股票

![](img/256446a5e615f65a1489056637e9f633_34.png)

![](img/256446a5e615f65a1489056637e9f633_36.png)

判断股票是否全年停牌，如果是则将其从股票列表中移除。

![](img/256446a5e615f65a1489056637e9f633_38.png)

![](img/256446a5e615f65a1489056637e9f633_40.png)

![](img/256446a5e615f65a1489056637e9f633_42.png)

```python
def filter_paused(stock_list):
    """
    过滤停牌股票
    """
    filtered_list = []
    for stock in stock_list:
        # 使用API判断股票是否全年停牌
        if not is_all_day_paused(stock):
            filtered_list.append(stock)
    return filtered_list
```

![](img/256446a5e615f65a1489056637e9f633_44.png)

![](img/256446a5e615f65a1489056637e9f633_46.png)

### 2. 过滤ST/S\*ST股票

![](img/256446a5e615f65a1489056637e9f633_48.png)

![](img/256446a5e615f65a1489056637e9f633_50.png)

![](img/256446a5e615f65a1489056637e9f633_52.png)

判断股票是否为ST或S\*ST股票，如果是则将其过滤掉。

![](img/256446a5e615f65a1489056637e9f633_54.png)

![](img/256446a5e615f65a1489056637e9f633_56.png)

![](img/256446a5e615f65a1489056637e9f633_58.png)

```python
def filter_st(stock_list):
    """
    过滤ST/S*ST股票
    """
    filtered_list = []
    for stock in stock_list:
        # 使用API判断股票是否为ST/S*ST股
        if not is_st_stock(stock):
            filtered_list.append(stock)
    return filtered_list
```

![](img/256446a5e615f65a1489056637e9f633_60.png)

![](img/256446a5e615f65a1489056637e9f633_62.png)

![](img/256446a5e615f65a1489056637e9f633_64.png)

### 3. 过滤新上市股票

![](img/256446a5e615f65a1489056637e9f633_66.png)

![](img/256446a5e615f65a1489056637e9f633_68.png)

判断股票上市天数是否过短（例如少于180天），如果是则将其视为新股并过滤。

![](img/256446a5e615f65a1489056637e9f633_70.png)

![](img/256446a5e615f65a1489056637e9f633_72.png)

```python
def filter_new(stock_list):
    """
    过滤新上市股票（例如上市天数少于180天）
    """
    filtered_list = []
    for stock in stock_list:
        # 获取股票上市天数
        days_listed = get_listed_days(stock)
        # 判断是否满足最小上市天数要求
        if days_listed > 180:
            filtered_list.append(stock)
    return filtered_list
```

![](img/256446a5e615f65a1489056637e9f633_74.png)

![](img/256446a5e615f65a1489056637e9f633_76.png)

![](img/256446a5e615f65a1489056637e9f633_78.png)

完成函数定义后，我们可以按顺序对初始股票列表进行逐层过滤。

![](img/256446a5e615f65a1489056637e9f633_80.png)

![](img/256446a5e615f65a1489056637e9f633_82.png)

![](img/256446a5e615f65a1489056637e9f633_84.png)

```python
# 假设 initial_stocks 是初始的股票代码列表
initial_stocks = ['000001.XSHE', '600000.XSHG', ...]

![](img/256446a5e615f65a1489056637e9f633_86.png)

![](img/256446a5e615f65a1489056637e9f633_88.png)

# 第一步：过滤停牌股
filtered_by_pause = filter_paused(initial_stocks)

![](img/256446a5e615f65a1489056637e9f633_90.png)

![](img/256446a5e615f65a1489056637e9f633_92.png)

![](img/256446a5e615f65a1489056637e9f633_94.png)

# 第二步：过滤ST股
filtered_by_st = filter_st(filtered_by_pause)

![](img/256446a5e615f65a1489056637e9f633_96.png)

![](img/256446a5e615f65a1489056637e9f633_98.png)

# 第三步：过滤新股
final_stock_list = filter_new(filtered_by_st)
```

![](img/256446a5e615f65a1489056637e9f633_100.png)

![](img/256446a5e615f65a1489056637e9f633_102.png)

![](img/256446a5e615f65a1489056637e9f633_104.png)

经过以上三步，`final_stock_list` 中剩下的就是我们认为可以进行后续分析的合格股票。

![](img/256446a5e615f65a1489056637e9f633_106.png)

![](img/256446a5e615f65a1489056637e9f633_108.png)

## 查询所需的财务指标 📈

![](img/256446a5e615f65a1489056637e9f633_110.png)

![](img/256446a5e615f65a1489056637e9f633_112.png)

![](img/256446a5e615f65a1489056637e9f633_114.png)

在得到过滤后的股票池后，下一步是获取这些股票的特定财务指标数据。在本例中，我们关注**市盈率（PE Ratio）**和**市值（Market Cap）**这两个因子。

![](img/256446a5e615f65a1489056637e9f633_116.png)

![](img/256446a5e615f65a1489056637e9f633_118.png)

我们将使用 `get_fundamentals` API 来查询数据。该函数需要传入一个查询语句（query）来指定要获取的指标。

![](img/256446a5e615f65a1489056637e9f633_120.png)

![](img/256446a5e615f65a1489056637e9f633_122.png)

以下是查询市盈率和市值的代码：

![](img/256446a5e615f65a1489056637e9f633_124.png)

![](img/256446a5e615f65a1489056637e9f633_126.png)

```python
# 构建查询语句，查询市盈率(pe_ratio)和市值(market_cap)
query = query(
    fundamentals.eod_derivative_indicator.pe_ratio,
    fundamentals.eod_derivative_indicator.market_cap
)

![](img/256446a5e615f65a1489056637e9f633_128.png)

![](img/256446a5e615f65a1489056637e9f633_130.png)

![](img/256446a5e615f65a1489056637e9f633_132.png)

# 执行查询，并指定只查询我们最终股票池中的股票
fundamental_data = get_fundamentals(query, entry_date='2023-10-27', filter= fundamentals.stockcode.in_(final_stock_list))
```

![](img/256446a5e615f65a1489056637e9f633_134.png)

![](img/256446a5e615f65a1489056637e9f633_136.png)

**代码解释**：
*   `fundamentals.eod_derivative_indicator.pe_ratio`：指定查询市盈率指标。
*   `fundamentals.eod_derivative_indicator.market_cap`：指定查询市值指标。
*   `filter= fundamentals.stockcode.in_(final_stock_list)`：这是一个过滤条件，确保只查询 `final_stock_list` 中包含的股票数据。

![](img/256446a5e615f65a1489056637e9f633_138.png)

![](img/256446a5e615f65a1489056637e9f633_140.png)

![](img/256446a5e615f65a1489056637e9f633_142.png)

## 数据整理与转置 🔄

![](img/256446a5e615f65a1489056637e9f633_144.png)

![](img/256446a5e615f65a1489056637e9f633_146.png)

直接查询返回的数据格式可能不适合后续分析。通常，返回的 `DataFrame` 的索引是日期，列是各个股票的代码，而我们需要的是行为股票、列为指标的数据格式。

![](img/256446a5e615f65a1489056637e9f633_148.png)

![](img/256446a5e615f65a1489056637e9f633_150.png)

![](img/256446a5e615f65a1489056637e9f633_152.png)

因此，我们需要对数据进行转置，并处理可能存在的空值。

![](img/256446a5e615f65a1489056637e9f633_154.png)

![](img/256446a5e615f65a1489056637e9f633_156.png)

![](img/256446a5e615f65a1489056637e9f633_158.png)

```python
# 对查询结果进行转置，使行代表股票，列代表指标
transposed_data = fundamental_data.T

![](img/256446a5e615f65a1489056637e9f633_160.png)

![](img/256446a5e615f65a1489056637e9f633_162.png)

# 删除包含空值（NaN）的行，确保数据完整性
cleaned_data = transposed_data.dropna()
```

![](img/256446a5e615f65a1489056637e9f633_164.png)

![](img/256446a5e615f65a1489056637e9f633_166.png)

![](img/256446a5e615f65a1489056637e9f633_168.png)

经过 `dropna()` 处理后，`cleaned_data` 就是一个干净、格式规整的数据集，每一行代表一只股票，每一列代表一个财务指标（市盈率或市值），可以直接用于后续的因子打分、排序和选股策略。

![](img/256446a5e615f65a1489056637e9f633_170.png)

![](img/256446a5e615f65a1489056637e9f633_172.png)

本节课中我们一起学习了量化交易中数据准备的关键步骤。我们首先定义了三个过滤函数，用于从股票池中剔除停牌股、ST股和新股，得到了一个干净的候选股票列表。接着，我们使用 `get_fundamentals` 函数查询了这些股票的市盈率和市值指标。最后，我们对查询结果进行了转置和空值清理，得到了结构清晰、可直接用于分析的数据表格。这些步骤是构建任何量化策略模型前必不可少的数据基础工作。