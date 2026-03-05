# Python金融分析与量化交易实战课程：P37：3-获取因子指标数据 📊

在本节课中，我们将学习如何使用量化分析工具包，获取指定时间段内所有股票的特定因子指标数据。我们将从获取交易日列表开始，逐步完成数据查询，并最终获得可用于分析的因子数据。

## 概述

上一节我们介绍了量化分析所需的基本工具包。本节中，我们来看看如何具体获取因子数据。与回测框架不同，因子分析模块没有内置的每日数据获取机制，因此我们需要手动编写代码来获取多日数据。

## 第一步：选择日期范围

首先，我们需要确定分析的时间段。这意味着要获取从起始日期到结束日期之间的所有交易日列表。

以下是获取交易日列表的步骤：

1.  定义一个函数 `get_trading_date`。
2.  函数接收两个参数：起始日期（如 `‘2019-01-01’`）和结束日期（如 `‘2020-01-01’`）。
3.  函数返回该时间段内所有的交易日列表。

我们通过以下代码实现：

![](img/cb4ba269ed51faa23c63893e8d9ba24b_1.png)

```python
def get_trading_date(start_date, end_date):
    trading_dates = get_trading_dates(start_date, end_date)
    return trading_dates
```

![](img/cb4ba269ed51faa23c63893e8d9ba24b_3.png)

执行这段代码后，我们就得到了 `2019-01-01` 到 `2020-01-01` 之间所有的交易日，存储在 `trading_dates` 变量中。

![](img/cb4ba269ed51faa23c63893e8d9ba24b_5.png)

![](img/cb4ba269ed51faa23c63893e8d9ba24b_7.png)

## 第二步：获取因子指标数据

有了交易日列表后，接下来我们需要获取具体的因子指标数据。这涉及到对股票池进行数据查询。

![](img/cb4ba269ed51faa23c63893e8d9ba24b_9.png)

以下是获取因子指标数据的步骤：

![](img/cb4ba269ed51faa23c63893e8d9ba24b_11.png)

![](img/cb4ba269ed51faa23c63893e8d9ba24b_13.png)

1.  **确定分析因子**：首先选择要分析的因子，例如市盈率（`pe_ratio`）。
2.  **确定股票池**：定义要分析的股票范围，这里我们选择全部A股。
3.  **构建查询语句**：使用工具包提供的 `query` 和 `filter` 功能，构建针对指定因子和股票池的查询。
4.  **执行查询**：在特定的交易日，执行构建好的查询语句以获取数据。

我们通过以下代码实现：

![](img/cb4ba269ed51faa23c63893e8d9ba24b_15.png)

```python
# 1. 定义股票池（全部A股）
stock_pool = index_components(‘000300.XSHG‘)

![](img/cb4ba269ed51faa23c63893e8d9ba24b_17.png)

![](img/cb4ba269ed51faa23c63893e8d9ba24b_19.png)

# 2. 构建查询语句
q = query(fundamentals.eod_derivative_indicator.pe_ratio
         ).filter(fundamentals.eod_derivative_indicator.code.in_(stock_pool))

![](img/cb4ba269ed51faa23c63893e8d9ba24b_21.png)

# 3. 执行查询（以第一个交易日为例）
specific_date = trading_dates[0]
factor_data = get_fundamentals(q, specific_date)
```

![](img/cb4ba269ed51faa23c63893e8d9ba24b_23.png)

执行查询后，`factor_data` 变量中存储了指定交易日所有股票的市盈率数据。数据是一个三维结构，但有效数据集中在最后一个维度。我们可以使用 `.iloc[0, 0, :5]` 来查看前5条股票的数据。

![](img/cb4ba269ed51faa23c63893e8d9ba24b_25.png)

## 当前进度总结

![](img/cb4ba269ed51faa23c63893e8d9ba24b_27.png)

至此，我们已经完成了**获取指定日期单日因子数据**的任务。我们学会了如何获取交易日列表、构建股票池、编写查询语句并在特定日期执行查询以获取数据。

![](img/cb4ba269ed51faa23c63893e8d9ba24b_29.png)

## 下一步任务

![](img/cb4ba269ed51faa23c63893e8d9ba24b_31.png)

目前，我们仅获取了单日的数据。然而，量化分析通常需要一段时间序列内的数据。因此，接下来的任务是将上述单日查询过程循环应用于 `trading_dates` 列表中的每一个交易日，从而获取整个分析时间段内的完整面板数据，为后续的因子分析和筛选打下基础。

![](img/cb4ba269ed51faa23c63893e8d9ba24b_33.png)

![](img/cb4ba269ed51faa23c63893e8d9ba24b_35.png)

## 本节课总结

![](img/cb4ba269ed51faa23c63893e8d9ba24b_37.png)

在本节课中，我们一起学习了获取因子指标数据的完整流程：
1.  我们首先定义了分析的时间范围并获取了对应的交易日列表。
2.  接着，我们选择了目标因子（如市盈率）并确定了分析的股票池。
3.  然后，我们通过构建 `query` 语句并添加 `filter` 条件，创建了精确的数据查询指令。
4.  最后，我们在指定日期执行了查询，成功获得了因子数据，并了解了数据的基本结构。

![](img/cb4ba269ed51faa23c63893e8d9ba24b_39.png)

![](img/cb4ba269ed51faa23c63893e8d9ba24b_41.png)

通过以上步骤，我们掌握了从零开始获取特定金融因子数据的关键方法，这是进行量化因子分析与策略构建的重要第一步。