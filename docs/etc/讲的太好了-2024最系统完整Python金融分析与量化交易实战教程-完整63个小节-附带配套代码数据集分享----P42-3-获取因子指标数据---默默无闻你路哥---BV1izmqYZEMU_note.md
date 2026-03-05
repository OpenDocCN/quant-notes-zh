# Python金融分析与量化交易实战教程：P42：3-获取因子指标数据

在本节课中，我们将学习如何获取指定时间段内的因子指标数据。与回测框架不同，因子分析需要我们自己处理日期循环和数据获取。我们将从选择日期范围开始，逐步完成查询特定因子（如市盈率PE）在选定股票池中数据的过程。

## 导入工具包

上一节我们介绍了课程的整体框架，本节中我们来看看具体的代码实现。首先，我们需要导入必要的工具包。

![](img/aae0ff188cb04396b4cfa594a964e3fe_1.png)

![](img/aae0ff188cb04396b4cfa594a964e3fe_2.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from jaqs_fxdayu.data import DataView
from jaqs_fxdayu.data import RemoteDataService
```

以下是导入模块的说明：
*   `pandas` 和 `numpy` 用于数据处理。
*   `matplotlib.pyplot` 用于后续绘图。
*   `DataView` 和 `RemoteDataService` 是用于金融数据分析和获取的核心模块。

![](img/aae0ff188cb04396b4cfa594a964e3fe_4.png)

## 选择日期数据

工具包导入完毕后，我们需要获取分析所需的时间段。因子分析需要一次性获取多日的数据，因此第一步是确定一个日期范围。

我们定义一个函数来获取指定时间段内的所有交易日列表。

![](img/aae0ff188cb04396b4cfa594a964e3fe_6.png)

```python
def get_trading_dates(start_date='2019-01-01', end_date='2020-01-01'):
    """
    获取指定起始日期和结束日期之间的交易日列表。
    参数:
        start_date (str): 起始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        list: 交易日期的列表。
    """
    # 此处需要根据实际数据源API获取交易日列表，以下为示例
    # 假设 data_api 是已初始化的数据服务对象
    trade_dates = data_api.trade_calendar(start_date, end_date)
    return trade_dates
```

执行以下代码来获取并查看我们选定的日期范围：

```python
trade_days = get_trading_dates('2019-01-01', '2020-01-01')
print(f"共获取到 {len(trade_days)} 个交易日")
print("前5个交易日：", trade_days[:5])
```

![](img/aae0ff188cb04396b4cfa594a964e3fe_8.png)

这样，我们就得到了从2019年1月1日到2020年1月1日之间所有的交易日，为后续按日查询数据做好了准备。

![](img/aae0ff188cb04396b4cfa594a964e3fe_9.png)

## 获取股票池

确定了时间范围后，接下来我们需要确定要分析哪些股票。这里我们以沪深300指数成分股为例，构建我们的股票池。

```python
# 获取沪深300指数成分股代码列表
# 假设 index_component 函数可以获取指定指数的成分股
hs300_symbols = index_component('000300.SH')
print(f"股票池中共有 {len(hs300_symbols)} 只股票")
print("股票代码示例：", hs300_symbols[:5])
```

`hs300_symbols` 这个列表就是我们后续查询因子数据时所要覆盖的股票集合。

![](img/aae0ff188cb04396b4cfa594a964e3fe_11.png)

![](img/aae0ff188cb04396b4cfa594a964e3fe_12.png)

## 查询单日因子数据

有了日期和股票池，我们现在可以尝试查询某一特定日期的因子数据了。我们以市盈率（PE）这个因子为例。

![](img/aae0ff188cb04396b4cfa594a964e3fe_14.png)

以下是构建查询语句并获取数据的步骤：

![](img/aae0ff188cb04396b4cfa594a964e3fe_15.png)

![](img/aae0ff188cb04396b4cfa594a964e3fe_16.png)

```python
# 1. 创建查询对象
from jaqs_fxdayu.data import Fundamental
q = Fundamental.query()

# 2. 指定要查询的因子字段，这里查询市盈率(pe_ttm)
q = q.filter(Fundamental.pe_ttm > 0)  # 可以添加过滤条件，例如只查询市盈率为正的

# 3. 执行查询，获取指定日期的数据
# 假设 trade_days 是之前获取的交易日列表，我们取第一个交易日进行测试
target_date = trade_days[0]
factor_data = q.filter(Fundamental.symbol.in_(hs300_symbols)).get(target_date)

![](img/aae0ff188cb04396b4cfa594a964e3fe_18.png)

![](img/aae0ff188cb04396b4cfa594a964e3fe_20.png)

# 4. 查看获取到的数据
print(f"查询日期：{target_date}")
print(f"数据形状（shape）：{factor_data.shape}")
print("前5条数据：")
print(factor_data.head())
```

请注意，实际查询语句的字段名（如 `pe_ttm`, `symbol`）需要根据你所使用的数据源（`jaqs`）的具体API进行调整。`get(target_date)` 方法用于获取指定日期的截面数据。

![](img/aae0ff188cb04396b4cfa594a964e3fe_22.png)

## 获取多日因子数据

![](img/aae0ff188cb04396b4cfa594a964e3fe_23.png)

上一节我们学会了获取单日数据，但因子分析通常需要对一个时间段进行研究。本节中我们来看看如何循环获取多日数据。

我们需要遍历之前获取的 `trade_days` 列表，逐日查询，并将结果整合。

![](img/aae0ff188cb04396b4cfa594a964e3fe_25.png)

![](img/aae0ff188cb04396b4cfa594a964e3fe_26.png)

```python
# 初始化一个空字典或列表来存储每日数据
all_factor_data = {}

for single_date in trade_days:
    try:
        # 执行与单日查询相同的操作
        daily_data = q.filter(Fundamental.symbol.in_(hs300_symbols)).get(single_date)
        # 将数据存储起来，以日期为键
        all_factor_data[single_date] = daily_data
    except Exception as e:
        print(f"获取日期 {single_date} 的数据时出错：{e}")
        continue

![](img/aae0ff188cb04396b4cfa594a964e3fe_28.png)

print(f"成功获取了 {len(all_factor_data)} 天的因子数据。")
```

![](img/aae0ff188cb04396b4cfa594a964e3fe_29.png)

循环完成后，`all_factor_data` 这个字典就包含了我们所需时间段内，股票池中所有股票每天的市盈率数据。你可以将其转换为 `pandas DataFrame` 以便进行更深入的分析，例如：

```python
# 将所有日期的数据合并成一个大的DataFrame
factor_df = pd.concat(all_factor_data, names=['trade_date', 'symbol'])
print(factor_df.head())
```

![](img/aae0ff188cb04396b4cfa594a964e3fe_31.png)

![](img/aae0ff188cb04396b4cfa594a964e3fe_32.png)

## 总结

![](img/aae0ff188cb04396b4cfa594a964e3fe_33.png)

本节课中我们一起学习了获取因子指标数据的完整流程。

![](img/aae0ff188cb04396b4cfa594a964e3fe_35.png)

我们首先**导入必要的分析工具包**。接着，**定义了日期范围**并获取了交易日列表。然后，**构建了股票池**（以沪深300成分股为例）。之后，我们**编写了查询语句**，成功**获取了单日的特定因子（PE）数据**。最后，我们通过**日期循环**，扩展了单日查询的逻辑，**实现了对多日因子数据的批量获取**。

![](img/aae0ff188cb04396b4cfa594a964e3fe_36.png)

![](img/aae0ff188cb04396b4cfa594a964e3fe_37.png)

掌握这一步是进行后续因子有效性检验、因子合成和因子选股策略构建的基础。在下一节中，我们将利用本节获取到的数据，开始进行因子的初步分析和可视化。