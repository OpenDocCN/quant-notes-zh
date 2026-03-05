# Python金融量化：P27：获取因子指标数据 📊

![](img/fa053e9783885b03e5c3233b5bba34de_0.png)

![](img/fa053e9783885b03e5c3233b5bba34de_2.png)

在本节课中，我们将学习如何使用量化分析工具包，获取指定时间段内所有股票的特定因子指标数据。我们将从获取交易日列表开始，逐步完成数据查询，并最终获得可用于分析的因子数据。

![](img/fa053e9783885b03e5c3233b5bba34de_4.png)

---

![](img/fa053e9783885b03e5c3233b5bba34de_6.png)

## 导入工具包

![](img/fa053e9783885b03e5c3233b5bba34de_8.png)

首先，我们需要导入必要的工具包。`alphalens` 是进行因子分析的核心库，`utils` 模块用于数据处理，`plotting` 模块用于绘图，其他模块则用于后续的分析任务。

![](img/fa053e9783885b03e5c3233b5bba34de_10.png)

```python
import alphalens
from alphalens import utils, plotting
# 其他用于分析的模块
```

---

## 第一步：选择日期范围

![](img/fa053e9783885b03e5c3233b5bba34de_12.png)

![](img/fa053e9783885b03e5c3233b5bba34de_14.png)

上一节我们介绍了需要导入的模块，本节中我们来看看如何获取分析所需的时间段数据。与回测框架不同，因子分析模块没有内置的每日执行函数。因此，我们需要手动获取多天的数据。

![](img/fa053e9783885b03e5c3233b5bba34de_16.png)

我们需要先选择一个日期范围，例如从2019年1月1日到2020年1月1日，并将这个时间段内的所有交易日提取出来。这些日期将作为参数传递给后续的数据查询函数。

![](img/fa053e9783885b03e5c3233b5bba34de_18.png)

![](img/fa053e9783885b03e5c3233b5bba34de_20.png)

以下是获取交易日期的函数：

```python
def get_trading_date(start_date, end_date):
    """
    获取指定开始日期和结束日期之间的所有交易日。
    参数:
        start_date (str): 开始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        trading_dates: 交易日列表。
    """
    trading_dates = ... # 使用相应API获取日期列表的代码
    return trading_dates

![](img/fa053e9783885b03e5c3233b5bba34de_22.png)

![](img/fa053e9783885b03e5c3233b5bba34de_24.png)

# 示例：获取2019-01-01到2020-01-01的交易日
trading_dates = get_trading_date('2019-01-01', '2020-01-01')
```

执行此步骤后，我们就得到了分析所需时间段内的所有交易日列表。

![](img/fa053e9783885b03e5c3233b5bba34de_26.png)

---

![](img/fa053e9783885b03e5c3233b5bba34de_28.png)

## 第二步：获取股票池与构建查询

![](img/fa053e9783885b03e5c3233b5bba34de_30.png)

在获取了交易日数据后，接下来我们需要确定分析哪些股票以及查询什么因子。在本例中，我们将以沪深A股的所有股票作为股票池，并以市盈率（PE Ratio）作为示例因子进行分析。

以下是准备股票池和构建查询语句的步骤：

1.  **获取股票池**：获取沪深A股的所有股票代码。
2.  **构建查询语句**：使用 `query` 函数构建一个查询，指定要获取的因子（如 `valuation.pe_ratio`），并过滤出属于我们股票池的股票。

```python
# 1. 获取股票池（示例，实际代码取决于数据源API）
stock_pool = get_all_a_shares() # 假设的函数，获取所有A股代码

![](img/fa053e9783885b03e5c3233b5bba34de_32.png)

# 2. 构建查询语句
q = query(
    valuation.pe_ratio # 查询市盈率因子
).filter(
    valuation.pe_ratio != None # 过滤掉PE率为空的数据
).filter(
    valuation.code.in_(stock_pool) # 只查询股票池中的股票
)
```

![](img/fa053e9783885b03e5c3233b5bba34de_34.png)

![](img/fa053e9783885b03e5c3233b5bba34de_36.png)

这段代码定义了一个查询 `q`，它旨在获取股票池中所有股票的市盈率数据。

---

![](img/fa053e9783885b03e5c3233b5bba34de_38.png)

![](img/fa053e9783885b03e5c3233b5bba34de_40.png)

## 第三步：执行查询并获取数据

构建好查询语句后，我们需要执行它来获取数据。查询执行时需要指定具体的查询日期。我们可以先尝试查询第一个交易日（`trading_dates[0]`）的数据，以验证操作是否正确。

![](img/fa053e9783885b03e5c3233b5bba34de_42.png)

![](img/fa053e9783885b03e5c3233b5bba34de_44.png)

以下是执行查询的代码：

![](img/fa053e9783885b03e5c3233b5bba34de_46.png)

```python
# 执行查询，获取指定日期的因子数据
# 假设 get_fundamentals 是执行查询的API函数
factor_data = get_fundamentals(q, date=trading_dates[0])

![](img/fa053e9783885b03e5c3233b5bba34de_48.png)

# 查看数据的形状和前几行
print(factor_data.shape)
print(factor_data.iloc[0:5]) # 查看前5条数据
```

![](img/fa053e9783885b03e5c3233b5bba34de_50.png)

执行后，我们将得到一个数据集。数据形状可能是多维的，但核心的因子数据位于最后一个维度。打印出的前几行数据会显示股票代码及其对应的因子值（例如市盈率）。此时，数据对应的是单个交易日（如2019年1月2日）的情况。

![](img/fa053e9783885b03e5c3233b5bba34de_52.png)

---

![](img/fa053e9783885b03e5c3233b5bba34de_54.png)

![](img/fa053e9783885b03e5c3233b5bba34de_56.png)

## 总结

![](img/fa053e9783885b03e5c3233b5bba34de_58.png)

![](img/fa053e9783885b03e5c3233b5bba34de_60.png)

本节课中我们一起学习了因子分析数据准备的核心步骤：

![](img/fa053e9783885b03e5c3233b5bba34de_62.png)

1.  **选择日期范围**：定义了 `get_trading_date` 函数来获取特定时间段内的所有交易日。
2.  **准备股票池与查询**：确定了分析对象（所有A股）和目标因子（市盈率），并构建了相应的查询语句 `q`。
3.  **执行查询**：使用 `get_fundamentals` 函数执行查询，成功获取了指定交易日单个因子的截面数据。

![](img/fa053e9783885b03e5c3233b5bba34de_64.png)

![](img/fa053e9783885b03e5c3233b5bba34de_66.png)

目前，我们完成了**获取指定日期单日因子数据**的操作。接下来的任务是将这一过程扩展到整个时间段内的每一天，以获取面板数据用于深入的因子分析。