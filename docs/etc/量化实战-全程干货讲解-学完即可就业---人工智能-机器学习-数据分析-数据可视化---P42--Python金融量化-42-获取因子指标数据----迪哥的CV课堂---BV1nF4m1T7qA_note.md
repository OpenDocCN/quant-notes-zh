# Python金融量化：P42：获取因子指标数据 📊

在本节课中，我们将学习如何使用Python获取金融因子指标数据。这是进行因子分析和量化交易策略构建的基础步骤。我们将从导入必要的工具包开始，逐步完成数据获取的完整流程。

---

![](img/4f2a2bfa40796afd8fb25779e17748f3_1.png)

## 导入工具包 🧰

![](img/4f2a2bfa40796afd8fb25779e17748f3_3.png)

首先，我们需要导入所有必要的工具包。这些工具将帮助我们处理数据、绘图以及后续的分析任务。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
# 其他用于分析的模块将在后续使用
```

`pandas` 和 `numpy` 用于数据处理，`matplotlib` 用于绘图。其他用于因子分析的模块我们稍后会用到。

---

## 选择日期范围 📅

![](img/4f2a2bfa40796afd8fb25779e17748f3_5.png)

上一节我们介绍了工具包的导入，本节中我们来看看如何获取数据。与回测框架不同，当前模块没有自动按日获取数据的功能。因此，我们需要自己编写代码来获取指定日期范围内的数据。

第一步是选择一个日期范围。这意味着我们需要获取从起始日期到结束日期之间的所有交易日。

以下是获取交易日期的步骤：

1.  定义一个函数 `get_trading_dates`。
2.  函数接收两个参数：起始日期 `start_date` 和结束日期 `end_date`。
3.  函数返回该时间段内所有的交易日列表。

```python
def get_trading_dates(start_date, end_date):
    """
    获取指定日期范围内的所有交易日。
    参数:
        start_date (str): 起始日期，格式为‘YYYY-MM-DD’。
        end_date (str): 结束日期，格式为‘YYYY-MM-DD’。
    返回:
        list: 交易日期的列表。
    """
    # 这里需要调用相应API获取交易日历，此处为示例逻辑
    trading_dates = [...] # 假设这是获取到的交易日列表
    return trading_dates

![](img/4f2a2bfa40796afd8fb25779e17748f3_7.png)

# 示例：获取2019年全年交易日
dates = get_trading_dates('2019-01-01', '2020-01-01')
print(f"共获取到{len(dates)}个交易日")
```

这样，我们就得到了所需时间段内的所有交易日列表，这是后续查询数据的基础。

---

![](img/4f2a2bfa40796afd8fb25779e17748f3_9.png)

## 构建股票池 📈

![](img/4f2a2bfa40796afd8fb25779e17748f3_11.png)

获取日期后，下一步是确定我们要分析哪些股票。这里我们以获取A股市场所有股票为例，构建一个股票池。

以下是构建股票池的步骤：

1.  调用API获取全市场股票代码列表。
2.  将股票代码列表存储在一个变量中，作为我们的股票池。

```python
# 假设使用 `get_all_securities` 函数获取所有股票代码
stock_pool = get_all_securities(['stock']).index.tolist()
print(f"股票池中共有{len(stock_pool)}只股票")
```

变量 `stock_pool` 现在包含了我们要分析的所有股票代码，形成了后续查询的股票池。

![](img/4f2a2bfa40796afd8fb25779e17748f3_13.png)

---

## 查询因子指标 🔍

有了日期和股票池，我们现在可以查询具体的因子指标数据了。本节中，我们以市盈率（PE）因子为例进行演示。

![](img/4f2a2bfa40796afd8fb25779e17748f3_15.png)

查询需要构建一个查询语句（Query），指定要获取的因子字段，并过滤出我们股票池中的股票。

![](img/4f2a2bfa40796afd8fb25779e17748f3_17.png)

以下是构建查询语句的步骤：

1.  从 `fundamentals` 模块中导入需要的因子表（例如 `valuation` 表包含PE）。
2.  使用 `query` 函数构建查询，使用 `filter` 条件将股票代码限制在我们的股票池内。

```python
from fundamentals import valuation

# 构建查询语句
q = query(valuation.pe_ratio).filter(valuation.code.in_(stock_pool))
```

![](img/4f2a2bfa40796afd8fb25779e17748f3_19.png)

这段代码的意思是：查询 `valuation` 表中的 `pe_ratio`（市盈率）字段，并且只查询那些股票代码在我们 `stock_pool` 列表中的记录。

![](img/4f2a2bfa40796afd8fb25779e17748f3_21.png)

---

![](img/4f2a2bfa40796afd8fb25779e17748f3_23.png)

## 执行查询并获取数据 ⚙️

查询语句构建好后，需要执行它来获取实际数据。执行查询时需要指定具体的查询日期。

以下是执行查询的步骤：

1.  使用 `get_fundamentals` 函数执行查询。
2.  将之前构建的查询语句 `q` 和具体的查询日期 `date` 作为参数传入。

![](img/4f2a2bfa40796afd8fb25779e17748f3_25.png)

```python
# 假设我们查询第一个交易日的数据
target_date = dates[0]
factor_data = get_fundamentals(q, target_date)

![](img/4f2a2bfa40796afd8fb25779e17748f3_27.png)

# 查看数据的形状和前几行
print(f"数据维度: {factor_data.shape}")
print(factor_data.iloc[:5]) # 查看前5行数据
```

![](img/4f2a2bfa40796afd8fb25779e17748f3_29.png)

执行后，`factor_data` 变量中存储了指定日期、指定股票池内所有股票的PE比率数据。数据通常是一个多维结构，我们需要关注的是包含实际数值的维度。

---

![](img/4f2a2bfa40796afd8fb25779e17748f3_31.png)

## 数据结果解析 📋

现在，让我们查看一下获取到的数据。数据通常以DataFrame的形式返回，包含股票代码和对应的因子值。

![](img/4f2a2bfa40796afd8fb25779e17748f3_33.png)

```python
# 查看获取到的因子数据样例
print(factor_data.head())
```
输出可能类似于：
```
            code  pe_ratio
0  000001.XSHE    15.6
1  000002.XSHE    22.3
2  000004.XSHE    30.1
...
```
这表示在查询日期 `target_date`，股票 `000001.XSHE` 的市盈率是15.6。

![](img/4f2a2bfa40796afd8fb25779e17748f3_35.png)

至此，我们已经完成了单日因子数据的获取。回顾一下，我们目前完成的是**获取指定日期的因子数据**。

---

## 扩展至多日数据 🔄

![](img/4f2a2bfa40796afd8fb25779e17748f3_37.png)

在实际分析中，我们往往需要一段时间序列的因子数据，而不仅仅是单日数据。

思路是循环遍历我们之前获取的交易日列表 `dates`，对每一个日期都执行一次上述的查询操作，并将结果整合起来。

![](img/4f2a2bfa40796afd8fb25779e17748f3_39.png)

以下是获取多日数据的逻辑框架：

```python
# 初始化一个空列表或字典来存储所有日期的数据
all_data = []

for date in dates:
    # 对每个交易日执行查询
    daily_data = get_fundamentals(q, date)
    # 为每日数据添加日期列，以便区分
    daily_data['date'] = date
    # 将每日数据添加到总列表中
    all_data.append(daily_data)

![](img/4f2a2bfa40796afd8fb25779e17748f3_41.png)

# 将所有日期的数据合并成一个大的DataFrame
multi_day_factor_data = pd.concat(all_data, ignore_index=True)
```

![](img/4f2a2bfa40796afd8fb25779e17748f3_43.png)

这样，`multi_day_factor_data` 就包含了指定日期范围内、股票池内所有股票每天的PE比率数据，可以用于后续的时间序列分析或截面分析。

![](img/4f2a2bfa40796afd8fb25779e17748f3_45.png)

---

![](img/4f2a2bfa40796afd8fb25779e17748f3_47.png)

## 总结 🎯

本节课中我们一起学习了获取金融因子指标数据的完整流程。

![](img/4f2a2bfa40796afd8fb25779e17748f3_49.png)

1.  **导入工具包**：准备了数据处理和分析所需的环境。
2.  **选择日期范围**：定义了分析的时间窗口，获取了交易日列表。
3.  **构建股票池**：确定了要分析的目标股票集合。
4.  **查询因子指标**：以PE比率为例，构建了具体的数据库查询语句。
5.  **执行查询**：在指定日期执行查询，获得了截面数据。
6.  **解析数据**：查看了获取到的数据结构与内容。
7.  **扩展思路**：了解了如何将单日查询循环扩展至获取多日时间序列数据。

![](img/4f2a2bfa40796afd8fb25779e17748f3_51.png)

掌握这一步是进行因子有效性检验、构建多因子模型以及量化策略回测的基石。在接下来的课程中，我们将利用这些数据开展进一步的分析。