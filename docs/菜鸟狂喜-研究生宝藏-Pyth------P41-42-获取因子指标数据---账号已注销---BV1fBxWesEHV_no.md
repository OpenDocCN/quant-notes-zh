# Python金融量化分析：P41：获取因子指标数据

![](img/f78597787a67433a3bcfe370445aae89_1.png)

在本节课中，我们将学习如何使用`alphalens`库获取特定时间段内股票的因子指标数据。我们将从导入必要的工具包开始，逐步完成选择日期范围、构建股票池、编写查询语句以及执行数据获取的完整流程。

![](img/f78597787a67433a3bcfe370445aae89_3.png)

## 导入工具包

首先，我们需要导入分析所需的工具包。`alphalens`库提供了因子分析的核心功能，`pandas`用于数据处理，`matplotlib`用于绘图。

![](img/f78597787a67433a3bcfe370445aae89_5.png)

```python
import alphalens
import pandas as pd
import matplotlib.pyplot as plt
```

上一节我们介绍了工具包的导入，本节中我们来看看如何利用这些工具进行因子分析的第一步：获取数据。

## 选择日期数据

与回测框架不同，`alphalens`的因子分析模块没有内置的每日数据获取机制。因此，我们需要手动指定一个日期范围，并获取该时间段内所有的交易日数据。

![](img/f78597787a67433a3bcfe370445aae89_7.png)

以下是获取交易日期的步骤：

1.  定义一个函数 `get_trading_date`，它接受开始日期和结束日期两个参数。
2.  使用 `pd.date_range` 函数生成该日期范围内的所有交易日序列。

![](img/f78597787a67433a3bcfe370445aae89_9.png)

```python
def get_trading_date(start_date, end_date):
    trading_dates = pd.date_range(start=start_date, end=end_date, freq='B')
    return trading_dates
```

![](img/f78597787a67433a3bcfe370445aae89_11.png)

我们来检查一下这个函数是否正确工作。例如，获取2019年全年的交易日：

```python
trading_dates = get_trading_date('2019-01-01', '2019-12-31')
print(trading_dates[:5])
```

这样，我们就得到了指定时间段内所有交易日的列表，为后续查询数据做好了准备。

![](img/f78597787a67433a3bcfe370445aae89_13.png)

## 获取因子指标数据

![](img/f78597787a67433a3bcfe370445aae89_15.png)

有了日期范围，接下来我们需要获取具体的因子指标数据。这个过程分为三步：确定要分析的因子、构建股票池、编写并执行查询。

### 1. 确定分析因子

我们以市盈率（P/E Ratio）因子为例，其代码通常为 `pe_ratio`。在实际操作中，你可以根据分析需求选择任何因子。

![](img/f78597787a67433a3bcfe370445aae89_17.png)

![](img/f78597787a67433a3bcfe370445aae89_19.png)

### 2. 构建股票池

![](img/f78597787a67433a3bcfe370445aae89_21.png)

为了简化，我们选择获取沪深300指数（`000300.XSHG`）的所有成分股作为我们的股票池。

```python
stock_pool = ['000300.XSHG']
```

![](img/f78597787a67433a3bcfe370445aae89_23.png)

### 3. 编写并执行查询

![](img/f78597787a67433a3bcfe370445aae89_25.png)

我们需要使用 `jqdatasdk`（假设已安装并授权）的 `query` 和 `get_fundamentals` 函数来获取数据。查询语句（Query）用于指定要获取的因子字段和过滤条件。

![](img/f78597787a67433a3bcfe370445aae89_27.png)

以下是查询特定日期因子数据的代码：

![](img/f78597787a67433a3bcfe370445aae89_29.png)

```python
# 导入jqdatasdk（如果尚未导入）
from jqdatasdk import query, get_fundamentals

![](img/f78597787a67433a3bcfe370445aae89_31.png)

![](img/f78597787a67433a3bcfe370445aae89_33.png)

# 定义查询语句
q = query(
    # 这里填写具体的因子字段，例如：valuation.pe_ratio
    # 请根据jqdatasdk的API文档填写准确字段名
).filter(
    # 过滤条件：股票代码在我們的股票池中
    # 字段名可能为 `code` 或 `symbol`，请根据文档确认
)

# 执行查询，获取指定日期的数据
# 我们取日期列表中的第一天作为示例
target_date = trading_dates[0]
factor_data = get_fundamentals(q, date=target_date)

![](img/f78597787a67433a3bcfe370445aae89_35.png)

# 查看获取到的数据
print(factor_data.iloc[0, 0, :5])  # 查看前5条数据
```

![](img/f78597787a67433a3bcfe370445aae89_37.png)

**核心概念解释**：
*   **查询语句 (Query)**：一个定义了数据获取规则的**对象**。它告诉系统“我要什么数据”（字段）和“我要哪些数据”（过滤条件）。
*   **`get_fundamentals` 函数**：执行查询的**函数**。它需要两个关键参数：查询语句对象 `q` 和具体的查询日期 `date`。

执行后，`factor_data` 将是一个包含股票代码、对应日期和因子值的数据结构。初次获取的数据可能是一个三维结构，通常我们只关心最后一个维度的具体数值。

![](img/f78597787a67433a3bcfe370445aae89_39.png)

![](img/f78597787a67433a3bcfe370445aae89_41.png)

现在，我们已经完成了获取指定单日因子数据的操作。

![](img/f78597787a67433a3bcfe370445aae89_43.png)

![](img/f78597787a67433a3bcfe370445aae89_45.png)

## 总结

![](img/f78597787a67433a3bcfe370445aae89_47.png)

本节课中我们一起学习了获取因子指标数据的基本流程：
1.  **选择日期**：使用 `pd.date_range` 函数生成指定时间段的交易日列表。
2.  **确定因子与股票池**：明确要分析的因子（如 `pe_ratio`）和股票范围（如沪深300成分股）。
3.  **编写查询**：利用 `query()` 函数构建查询语句，指定字段和过滤条件。
4.  **执行获取**：使用 `get_fundamentals(q, date=某个交易日)` 函数获取具体日期的因子数据。

![](img/f78597787a67433a3bcfe370445aae89_49.png)

目前，我们成功获取了**单一日期**的因子数据。在接下来的课程中，我们将学习如何循环遍历整个日期列表，获取**多日**的因子数据，并将其整理成适合 `alphalens` 进行深入分析的格式。