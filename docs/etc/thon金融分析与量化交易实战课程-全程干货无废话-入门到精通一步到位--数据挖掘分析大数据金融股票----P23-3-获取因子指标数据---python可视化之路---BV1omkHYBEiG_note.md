# 金融数据分析：P23：3-获取因子指标数据 📊

![](img/aa3b4e88d3296e357b830eeeb6333c39_1.png)

![](img/aa3b4e88d3296e357b830eeeb6333c39_3.png)

在本节课中，我们将学习如何使用Python获取金融因子指标数据。这是进行因子分析和量化交易策略构建的基础步骤。我们将从导入必要的工具包开始，逐步完成数据查询与获取的完整流程。

![](img/aa3b4e88d3296e357b830eeeb6333c39_5.png)

## 导入工具包 🧰

![](img/aa3b4e88d3296e357b830eeeb6333c39_7.png)

首先，我们需要导入所有必要的工具包。这些工具将帮助我们处理数据、进行可视化以及后续的分析工作。

```python
# 导入数据处理、可视化及分析所需的模块
from alphalens.utils import get_clean_factor_and_forward_returns
from alphalens.tears import create_summary_tearsheet
from alphalens.performance import factor_information_coefficient
from alphalens.plotting import plot_ic_ts
```

![](img/aa3b4e88d3296e357b830eeeb6333c39_9.png)

## 选择日期范围 📅

![](img/aa3b4e88d3296e357b830eeeb6333c39_11.png)

上一节我们介绍了工具包的导入，本节中我们来看看如何获取特定时间段的数据。与回测框架不同，因子分析模块没有内置的每日数据获取功能，因此我们需要手动指定并获取日期序列。

![](img/aa3b4e88d3296e357b830eeeb6333c39_13.png)

以下是获取交易日期的步骤：

![](img/aa3b4e88d3296e357b830eeeb6333c39_15.png)

1.  定义一个函数 `get_trading_dates`，它接受开始日期和结束日期两个参数。
2.  使用数据接口获取该时间段内所有的交易日。
3.  返回一个包含所有交易日的列表。

![](img/aa3b4e88d3296e357b830eeeb6333c39_17.png)

```python
def get_trading_dates(start_date, end_date):
    """
    获取指定日期范围内的所有交易日。
    参数:
        start_date (str): 开始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        trading_dates (list): 交易日列表。
    """
    # 此处调用数据接口获取交易日历，示例中使用伪代码
    trading_dates = data_api.get_trading_dates(start_date, end_date)
    return trading_dates

![](img/aa3b4e88d3296e357b830eeeb6333c39_19.png)

# 示例：获取2019年全年的交易日
trading_dates = get_trading_dates('2019-01-01', '2020-01-01')
```

![](img/aa3b4e88d3296e357b830eeeb6333c39_21.png)

![](img/aa3b4e88d3296e357b830eeeb6333c39_23.png)

## 构建股票池与查询语句 📈

获取日期后，下一步是确定我们要分析哪些股票以及具体的因子指标。我们将构建一个股票池，并编写查询语句来获取特定因子的数据。

![](img/aa3b4e88d3296e357b830eeeb6333c39_25.png)

以下是构建查询的步骤：

![](img/aa3b4e88d3296e357b830eeeb6333c39_27.png)

1.  定义要分析的因子名称，例如 `pe_ratio`（市盈率）。
2.  获取全市场A股股票代码，构建股票池。
3.  使用 `query` 语句，指定要查询的因子字段，并过滤出属于我们股票池的股票。

![](img/aa3b4e88d3296e357b830eeeb6333c39_29.png)

```python
# 1. 定义要分析的因子
factor_name = 'pe_ratio'

# 2. 获取全A股股票代码，构建股票池
all_stocks = get_all_securities(['stock']).index.tolist()
stock_pool = all_stocks  # 本例中我们使用全部股票

# 3. 构建查询语句
q = query(
    fundamentals.eod_derivative_indicator.pe_ratio  # 查询市盈率因子
).filter(
    fundamentals.eod_derivative_indicator.stockcode.in_(stock_pool)  # 过滤股票池
)
```

![](img/aa3b4e88d3296e357b830eeeb6333c39_31.png)

## 执行查询并获取数据 🔍

查询语句构建完成后，我们需要执行它以获取数据。这里需要注意，查询需要针对具体的交易日进行。

![](img/aa3b4e88d3296e357b830eeeb6333c39_33.png)

以下是执行查询的步骤：

![](img/aa3b4e88d3296e357b830eeeb6333c39_35.png)

1.  使用 `get_fundamentals` 函数执行查询。
2.  传入我们构建好的查询语句 `q` 和具体的交易日 `date`。
3.  函数将返回一个包含指定日期、指定股票池的因子值的数据表。

![](img/aa3b4e88d3296e357b830eeeb6333c39_37.png)

```python
# 指定查询的日期（例如，取日期列表中的第一天）
query_date = trading_dates[0]

![](img/aa3b4e88d3296e357b830eeeb6333c39_39.png)

# 执行查询
factor_data = get_fundamentals(q, query_date)

# 查看数据的结构和前几行
print(factor_data.shape)
print(factor_data.iloc[:5])
```

![](img/aa3b4e88d3296e357b830eeeb6333c39_41.png)

执行后，`factor_data` 将是一个 `DataFrame`，其中索引是股票代码，列是我们查询的因子（如 `pe_ratio`），值是对应的因子数值。这个数据表代表了在 `query_date` 那一天，所有股票池中股票的市盈率情况。

![](img/aa3b4e88d3296e357b830eeeb6333c39_43.png)

## 总结与回顾 🎯

![](img/aa3b4e88d3296e357b830eeeb6333c39_45.png)

![](img/aa3b4e88d3296e357b830eeeb6333c39_47.png)

本节课中我们一起学习了获取金融因子指标数据的完整流程。

![](img/aa3b4e88d3296e357b830eeeb6333c39_49.png)

我们首先导入了 `alphalens` 等相关工具包。接着，我们定义了函数来获取特定时间段的交易日列表。然后，我们确定了分析目标（市盈率因子）并构建了全A股股票池。通过编写 `query` 语句，我们精确指定了需要获取的数据字段和范围。最后，我们执行查询，成功获取了指定交易日的因子数据表。

![](img/aa3b4e88d3296e357b830eeeb6333c39_51.png)

![](img/aa3b4e88d3296e357b830eeeb6333c39_53.png)

现在，我们已经掌握了获取单日因子数据的方法。在接下来的课程中，我们将学习如何循环获取多日数据，并利用这些数据进行深入的因子有效性分析。