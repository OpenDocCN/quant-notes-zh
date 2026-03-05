# 量化投资基础：P39：3-获取因子指标数据 📊

在本节课中，我们将学习如何使用量化分析工具包，获取指定时间段内所有股票的特定因子数据。这是进行因子分析的第一步，我们将从获取交易日数据开始，逐步完成对单一因子（如市盈率）的批量查询。

## 导入工具包

首先，我们需要导入必要的工具包。`alphalens` 是进行因子分析的专用库，我们从中导入几个关键模块。

```python
from alphalens.utils import get_clean_factor_and_forward_returns
from alphalens.tears import create_full_tear_sheet
import pandas as pd
import numpy as np
```

其中，`utils` 模块用于数据处理，`tears` 模块用于生成分析报告。此外，我们还导入了 `pandas` 和 `numpy` 用于常规的数据操作。

## 获取交易日数据

上一节我们介绍了工具包的导入，本节中我们来看看如何获取数据。与回测框架不同，当前模块没有自动按日获取数据的机制。因此，我们需要手动获取指定时间段内的所有交易日数据。

以下是获取交易日数据的步骤：

1.  **定义函数**：创建一个函数，用于获取指定起止日期之间的所有交易日。
2.  **调用API**：使用平台提供的 `get_trading_dates` 函数。
3.  **验证结果**：打印获取的日期列表，确保数据正确。

```python
def get_trading_date(start_date, end_date):
    """
    获取指定时间段内的交易日列表。
    参数:
        start_date (str): 开始日期，格式 'YYYY-MM-DD'
        end_date (str): 结束日期，格式 'YYYY-MM-DD'
    返回:
        trading_dates (list): 交易日列表
    """
    trading_dates = get_trading_dates(start_date, end_date).tolist()
    return trading_dates

![](img/288f8532e3778a8ec3302636e4f71191_1.png)

# 获取2019年全年的交易日
trading_dates = get_trading_date('2019-01-01', '2020-01-01')
print(f"共获取到 {len(trading_dates)} 个交易日")
print(trading_dates[:5])  # 打印前5个交易日
```

![](img/288f8532e3778a8ec3302636e4f71191_3.png)

运行上述代码后，我们就得到了一个包含所有目标交易日的列表 `trading_dates`，这是后续查询的基础。

![](img/288f8532e3778a8ec3302636e4f71191_5.png)

## 构建股票池

![](img/288f8532e3778a8ec3302636e4f71191_7.png)

有了交易日数据，接下来我们需要确定要对哪些股票进行因子分析。这里，我们选择获取沪深300指数的所有成分股作为我们的股票池。

以下是构建股票池的步骤：

![](img/288f8532e3778a8ec3302636e4f71191_9.png)

1.  **调用API**：使用 `get_index_stocks` 函数获取指数成分股。
2.  **指定指数**：传入指数代码，例如 `‘000300.XSHG’` 代表沪深300指数。
3.  **保存结果**：将获取的股票代码列表保存为 `stock_pool`。

![](img/288f8532e3778a8ec3302636e4f71191_11.png)

![](img/288f8532e3778a8ec3302636e4f71191_13.png)

```python
# 获取沪深300指数成分股代码
stock_pool = get_index_stocks('000300.XSHG')
print(f"股票池中共有 {len(stock_pool)} 只股票")
print(stock_pool[:10])  # 打印前10只股票代码
```

这样，我们就得到了一个包含多只股票的代码列表 `stock_pool`。

![](img/288f8532e3778a8ec3302636e4f71191_15.png)

## 查询单日因子数据

![](img/288f8532e3778a8ec3302636e4f71191_17.png)

现在，我们有了日期和股票，可以开始查询因子数据了。我们以市盈率（P/E Ratio）因子为例，演示如何查询**某一天**所有股票池内股票的该因子值。

![](img/288f8532e3778a8ec3302636e4f71191_19.png)

以下是查询单日因子数据的步骤：

![](img/288f8532e3778a8ec3302636e4f71191_21.png)

1.  **构建查询语句**：使用 `query` 函数，并指定要查询的因子字段。
2.  **添加过滤条件**：使用 `filter` 方法，确保只查询股票池内的股票。
3.  **执行查询**：使用 `get_fundamentals` 函数，传入查询语句和具体的查询日期。
4.  **查看数据**：检查返回数据的结构和内容。

![](img/288f8532e3778a8ec3302636e4f71191_23.png)

![](img/288f8532e3778a8ec3302636e4f71191_25.png)

```python
# 1. 构建查询语句
q = query(
    fundamentals.eod_derivative_indicator.pe_ratio  # 查询市盈率因子
).filter(
    fundamentals.eod_derivative_indicator.code.in_(stock_pool)  # 过滤股票池
)

# 2. 执行查询（查询第一个交易日的数据）
single_date = trading_dates[0]
factor_data = get_fundamentals(q, date=single_date)

![](img/288f8532e3778a8ec3302636e4f71191_27.png)

# 3. 查看数据
print(f"查询日期：{single_date}")
print(f"数据形状：{factor_data.shape}")
print(factor_data.iloc[0, 0, :5])  # 查看前5只股票的因子数据
```

![](img/288f8532e3778a8ec3302636e4f71191_29.png)

**核心概念解释**：
*   `query(...)`：构建数据查询的蓝图。
*   `.filter(...)`：在查询蓝图上添加筛选条件。
*   `get_fundamentals(q, date=...)`：执行查询，返回指定日期的基本面数据。

执行后，`factor_data` 是一个三维数据结构，通常我们只关心最后一个维度，它包含了股票代码与对应因子值的映射。

![](img/288f8532e3778a8ec3302636e4f71191_31.png)

![](img/288f8532e3778a8ec3302636e4f71191_33.png)

## 总结与展望

![](img/288f8532e3778a8ec3302636e4f71191_35.png)

本节课中我们一起学习了量化因子分析数据准备的核心步骤：

![](img/288f8532e3778a8ec3302636e4f71191_37.png)

![](img/288f8532e3778a8ec3302636e4f71191_39.png)

1.  **获取交易日数据**：确定了分析的时间范围。
2.  **构建股票池**：确定了分析的对象范围。
3.  **查询单日因子**：成功获取了特定一天、特定股票池的单个因子数据。

![](img/288f8532e3778a8ec3302636e4f71191_41.png)

我们目前完成了对**单一日期的数据获取**。然而，因子分析通常需要对一个时间序列进行分析。在下一节中，我们将学习如何**循环获取一段时间内每一天的因子数据**，并将其整合成一个规整的 `DataFrame`，为后续的因子有效性分析做好准备。