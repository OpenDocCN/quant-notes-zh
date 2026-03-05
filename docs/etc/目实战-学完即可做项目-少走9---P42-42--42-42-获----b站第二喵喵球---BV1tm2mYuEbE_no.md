# 量化交易实战课程：P42：获取因子指标数据

在本节课中，我们将学习如何使用Python量化工具包，获取指定时间段内所有股票的特定因子数据。这是进行因子分析的第一步。

![](img/c9735c73793b0022089f33bf0abdbd22_1.png)

上一节我们介绍了量化分析所需的环境和工具包。本节中，我们来看看如何具体获取用于分析的因子数据。

## 导入工具包

![](img/c9735c73793b0022089f33bf0abdbd22_3.png)

首先，我们需要导入分析所需的工具包。`alphalens` 是进行因子分析的常用库，我们将使用其中的几个模块。

```python
# 导入数据处理、绘图和分析模块
from alphalens.utils import get_clean_factor_and_forward_returns
from alphalens.tears import create_full_tear_sheet
from alphalens.performance import factor_information_coefficient
from alphalens.plotting import plot_ic_ts
```

## 获取交易日数据

与回测框架不同，因子分析模块没有内置的每日数据获取循环。因此，我们需要手动获取一个时间段内的所有交易日数据。

![](img/c9735c73793b0022089f33bf0abdbd22_5.png)

以下是获取交易日数据的步骤：

1.  定义起始日期和结束日期。
2.  使用工具函数获取该时间段内所有的交易日列表。

![](img/c9735c73793b0022089f33bf0abdbd22_7.png)

我们编写一个函数来完成这个任务。

```python
def get_trading_dates(start_date, end_date):
    """
    获取指定时间段内的所有交易日。
    参数:
        start_date (str): 起始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        pandas.DatetimeIndex: 交易日列表。
    """
    # 这里使用平台提供的交易日获取函数，例如 `get_trading_dates`
    trading_dates = get_trading_dates(start_date, end_date)
    return trading_dates
```

现在，我们调用这个函数来获取2019年全年的交易日数据。

![](img/c9735c73793b0022089f33bf0abdbd22_9.png)

```python
# 获取2019-01-01到2020-01-01之间的所有交易日
all_trading_dates = get_trading_dates('2019-01-01', '2020-01-01')
print(all_trading_dates[:5])  # 查看前5个交易日
```

![](img/c9735c73793b0022089f33bf0abdbd22_11.png)

## 获取股票池与因子数据

![](img/c9735c73793b0022089f33bf0abdbd22_13.png)

有了交易日列表后，下一步是获取我们要分析的股票集合（股票池）以及这些股票在特定日期的因子值。

![](img/c9735c73793b0022089f33bf0abdbd22_15.png)

以下是获取因子指标数据的步骤：

1.  定义股票池。这里我们选择获取所有A股股票。
2.  构建查询语句，指定要获取的因子（例如市盈率 `pe_ratio`）。
3.  执行查询，获取指定日期的因子数据。

![](img/c9735c73793b0022089f33bf0abdbd22_17.png)

首先，我们获取股票池。

![](img/c9735c73793b0022089f33bf0abdbd22_19.png)

![](img/c9735c73793b0022089f33bf0abdbd22_21.png)

```python
# 获取所有A股股票代码，例如使用 `get_all_securities` 函数
stock_pool = get_all_securities(['stock']).index.tolist()
print(f"股票池中共有 {len(stock_pool)} 只股票")
```

接下来，我们构建查询语句来获取市盈率因子。查询语句通常较长，我们可以参考平台的API文档。

![](img/c9735c73793b0022089f33bf0abdbd22_23.png)

```python
# 构建查询语句，获取市盈率(pe_ratio)因子
# 此查询语句格式依赖于具体的数据平台API
query_statement = query(
    fundamentals.eod_derivative_indicator.pe_ratio
).filter(
    fundamentals.eod_derivative_indicator.stockcode.in_(stock_pool)
)
```

![](img/c9735c73793b0022089f33bf0abdbd22_25.png)

最后，我们执行查询。由于我们有多个交易日，需要循环获取每一天的数据。我们先以第一个交易日为例。

![](img/c9735c73793b0022089f33bf0abdbd22_27.png)

```python
# 获取第一个交易日的日期
single_date = all_trading_dates[0]

![](img/c9735c73793b0022089f33bf0abdbd22_29.png)

# 执行查询，获取该日所有股票的市盈率数据
factor_data = get_fundamentals(query_statement, single_date)

![](img/c9735c73793b0022089f33bf0abdbd22_31.png)

![](img/c9735c73793b0022089f33bf0abdbd22_33.png)

# 查看数据的形状和前几行
print(factor_data.shape)
print(factor_data.iloc[0, 0, :5])  # 查看前5只股票的数据
```

请注意，返回的数据可能是一个三维结构，但通常只有最后一个维度（股票维度）包含有效数据。我们需要根据实际情况提取和处理。

![](img/c9735c73793b0022089f33bf0abdbd22_35.png)

## 循环获取多日数据

![](img/c9735c73793b0022089f33bf0abdbd22_37.png)

目前，我们只获取了一天的数据。要进行完整的因子分析，需要获取整个时间段内每一天的数据。

我们现在要做的，就是将单日查询的逻辑放入循环中，遍历 `all_trading_dates` 列表，获取每一天的因子数据，并将它们整合起来。

![](img/c9735c73793b0022089f33bf0abdbd22_39.png)

![](img/c9735c73793b0022089f33bf0abdbd22_41.png)

```python
# 初始化一个列表或字典来存储多日数据
all_factor_data = []

![](img/c9735c73793b0022089f33bf0abdbd22_43.png)

for date in all_trading_dates:
    # 获取单日数据
    daily_data = get_fundamentals(query_statement, date)
    # 存储数据，通常需要将日期作为索引或列与数据一起保存
    # ... (数据处理和存储逻辑)
    all_factor_data.append(daily_data)

![](img/c9735c73793b0022089f33bf0abdbd22_45.png)

![](img/c9735c73793b0022089f33bf0abdbd22_47.png)

# 将所有日期的数据合并为一个DataFrame
# final_factor_df = pd.concat(all_factor_data, axis=...)
```

![](img/c9735c73793b0022089f33bf0abdbd22_49.png)

本节课中我们一起学习了量化因子分析的数据准备阶段。我们掌握了如何获取指定时间段的交易日列表，如何构建股票池，以及如何查询并获取单日及多日的特定因子数据。这是后续进行因子有效性检验、分组收益分析等深入研究的基石。在下一节中，我们将利用这些数据开始进行因子的初步分析。