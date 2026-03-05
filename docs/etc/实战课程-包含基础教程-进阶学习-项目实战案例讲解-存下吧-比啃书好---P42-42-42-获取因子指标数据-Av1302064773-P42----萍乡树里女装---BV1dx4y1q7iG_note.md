# 量化交易教程：P42：获取因子指标数据

在本节课中，我们将学习如何使用Python工具包获取指定时间段内的股票因子指标数据。我们将从导入必要的模块开始，逐步完成选择日期范围、获取股票池、构建查询语句以及执行数据获取的完整流程。

## 导入工具包

![](img/b861222555d4f65794fe07edf20f5393_1.png)

上一节我们介绍了量化分析的基本框架，本节中我们来看看具体的数据获取操作。首先，需要导入后续分析将用到的所有工具包。

以下是导入的模块及其作用：
*   `pandas` 和 `numpy`：用于数据处理和计算。
*   `matplotlib`：用于数据可视化绘图。
*   `alphalens` 和 `zipline`：用于后续的因子分析。

![](img/b861222555d4f65794fe07edf20f5393_3.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import alphalens
import zipline
```

## 选择日期范围

![](img/b861222555d4f65794fe07edf20f5393_5.png)

与回测框架不同，当前模块不会自动按日处理数据。因此，我们需要手动指定并获取需要分析的日期范围。

第一步是选择一个日期范围。例如，我们需要获取从2019年1月1日到2020年1月1日之间的所有交易日数据。

```python
trading_days = get_trading_days(start_date='2019-01-01', end_date='2020-01-01')
```

![](img/b861222555d4f65794fe07edf20f5393_7.png)

![](img/b861222555d4f65794fe07edf20f5393_8.png)

![](img/b861222555d4f65794fe07edf20f5393_9.png)

## 获取股票池

![](img/b861222555d4f65794fe07edf20f5393_10.png)

在获取因子数据之前，需要确定分析对象，即股票池。这里我们选择获取A股市场所有股票作为我们的股票池。

![](img/b861222555d4f65794fe07edf20f5393_12.png)

以下是获取股票池的代码：
```python
universe = get_all_securities(types=['stock'])
```

![](img/b861222555d4f65794fe07edf20f5393_13.png)

![](img/b861222555d4f65794fe07edf20f5393_14.png)

## 构建并执行因子查询

![](img/b861222555d4f65794fe07edf20f5393_16.png)

有了日期范围和股票池，接下来就可以构建查询语句来获取特定的因子数据。我们以市盈率（PE）因子为例。

![](img/b861222555d4f65794fe07edf20f5393_18.png)

以下是构建查询语句的步骤：
1.  指定要查询的因子名称（例如 `pe_ratio`）。
2.  在查询中应用过滤器（`filter`），确保只获取股票池中的股票数据。
3.  执行查询，获取指定日期的因子数据。

![](img/b861222555d4f65794fe07edf20f5393_20.png)

![](img/b861222555d4f65794fe07edf20f5393_21.png)

```python
# 定义查询语句
q = query(fundamentals.income_statement.pe_ratio).filter(fundamentals.income_statement.stockcode.in_(universe.index))

![](img/b861222555d4f65794fe07edf20f5393_23.png)

# 执行查询，获取第一个交易日（trading_days[0]）的数据
factor_data = get_fundamentals(q, trading_days[0])
```

![](img/b861222555d4f65794fe07edf20f5393_25.png)

执行查询后，我们可以查看获取到的数据。数据是一个三维结构，通常我们只关心最后一个维度的具体数值。

![](img/b861222555d4f65794fe07edf20f5393_27.png)

```python
# 查看数据形状和前几条记录
print(factor_data.shape)
print(factor_data.iloc[0, 0, :5])
```

![](img/b861222555d4f65794fe07edf20f5393_28.png)

## 扩展至多日数据

![](img/b861222555d4f65794fe07edf20f5393_30.png)

![](img/b861222555d4f65794fe07edf20f5393_31.png)

目前，我们完成了获取**指定单日**因子数据的操作。但在实际分析中，我们通常需要获取**一段时间内连续多日**的数据。

![](img/b861222555d4f65794fe07edf20f5393_32.png)

那么，如何将单日查询扩展为多日查询呢？这将是后续课程中需要解决的关键问题，通常需要通过循环遍历日期列表或使用批量查询接口来实现。

![](img/b861222555d4f65794fe07edf20f5393_34.png)

![](img/b861222555d4f65794fe07edf20f5393_35.png)

![](img/b861222555d4f65794fe07edf20f5393_36.png)

本节课中我们一起学习了量化分析中获取因子指标数据的基础步骤，包括设置日期范围、建立股票池、构建查询语句以及获取单日因子数据。这是进行深入因子分析和策略回测的重要数据准备阶段。