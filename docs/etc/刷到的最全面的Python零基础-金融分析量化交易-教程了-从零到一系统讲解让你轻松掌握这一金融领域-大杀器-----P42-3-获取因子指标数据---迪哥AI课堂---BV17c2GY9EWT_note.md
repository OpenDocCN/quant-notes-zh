# Python金融分析量化交易：P42：3-获取因子指标数据 📊

![](img/cb19fb3837db13c028bc1c58746e7319_1.png)

在本节课中，我们将学习如何使用`Alphalens`库获取特定时间段的因子指标数据。我们将从获取交易日列表开始，然后构建查询语句，最终获取并查看指定因子在特定日期的数据。

![](img/cb19fb3837db13c028bc1c58746e7319_3.png)

## 概述

上一节我们介绍了工具包的导入。本节中，我们来看看如何获取用于因子分析的原始数据。与回测框架不同，`Alphalens`没有每日自动执行的`handle_bar`函数，因此我们需要手动获取多日的数据。

![](img/cb19fb3837db13c028bc1c58746e7319_5.png)

## 第一步：选择日期数据

首先，我们需要确定分析的时间范围，并获取该时间段内所有的交易日列表。这是后续查询数据的基础。

以下是获取交易日期的函数：

![](img/cb19fb3837db13c028bc1c58746e7319_7.png)

```python
def get_trading_date(start_date, end_date):
    """
    获取指定时间段内的所有交易日。
    参数:
        start_date (str): 开始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        trading_dates: 交易日列表。
    """
    trading_dates = get_trading_dates(start_date, end_date)
    return trading_dates
```

![](img/cb19fb3837db13c028bc1c58746e7319_9.png)

我们调用该函数，获取2019年全年的交易日：

![](img/cb19fb3837db13c028bc1c58746e7319_11.png)

```python
trading_dates = get_trading_date('2019-01-01', '2020-01-01')
```

这样，我们就得到了`trading_dates`，即2019年所有交易日的列表。

## 第二步：获取因子指标

![](img/cb19fb3837db13c028bc1c58746e7319_13.png)

有了交易日列表后，接下来我们需要获取具体的因子数据。这包括指定要分析的因子和股票池，然后执行查询。

![](img/cb19fb3837db13c028bc1c58746e7319_15.png)

以下是该步骤的关键操作：

1.  **指定分析因子**：我们以`p_retro`因子为例。
2.  **构建股票池**：这里我们选择获取所有A股股票（沪深300指数成分股`000300.XSHG`）作为初始股票池。
3.  **构建查询语句**：使用`query`函数构建数据查询语句，并过滤出股票池中的股票。
4.  **执行查询**：针对特定的交易日，执行查询以获取该日的因子数据。

![](img/cb19fb3837db13c028bc1c58746e7319_17.png)

核心代码如下：

![](img/cb19fb3837db13c028bc1c58746e7319_19.png)

![](img/cb19fb3837db13c028bc1c58746e7319_21.png)

```python
# 1. 指定分析因子
factor_name = ‘p_retro’

# 2. 构建股票池（示例为沪深300成分股）
stock_pool = get_index_stocks(‘000300.XSHG’)

![](img/cb19fb3837db13c028bc1c58746e7319_23.png)

![](img/cb19fb3837db13c028bc1c58746e7319_25.png)

# 3. 构建查询语句
q = query(fundamentals.financial_indicator.p_retro
         ).filter(fundamentals.financial_indicator.stockcode.in_(stock_pool))

![](img/cb19fb3837db13c028bc1c58746e7319_27.png)

# 4. 执行查询（以第一个交易日为例）
specific_date = trading_dates[0]
factor_data = get_fundamentals(q, specific_date)
```

![](img/cb19fb3837db13c028bc1c58746e7319_29.png)

执行后，`factor_data`变量中存储了在`specific_date`这一天，股票池中所有股票的`p_retro`因子值。

![](img/cb19fb3837db13c028bc1c58746e7319_31.png)

## 第三步：查看与分析数据

![](img/cb19fb3837db13c028bc1c58746e7319_33.png)

获取数据后，我们需要查看其结构以进行后续分析。`Alphalens`返回的数据是一个三维数据结构，但通常我们只关心最后一个维度的具体数值。

![](img/cb19fb3837db13c028bc1c58746e7319_35.png)

以下是查看数据的方法：

![](img/cb19fb3837db13c028bc1c58746e7319_37.png)

```python
# 查看前5条数据
# 索引 [0, 0, :] 表示取前两个维度为0，查看所有第三维度的数据
sample_data = factor_data.iloc[0, 0, :5]
print(sample_data)
```

![](img/cb19fb3837db13c028bc1c58746e7319_39.png)

输出结果将显示股票代码及其对应的因子值。请注意，结果中不包含日期信息，因为日期已在查询时作为参数传入（例如`trading_dates[0]`对应2019年1月2日）。

![](img/cb19fb3837db13c028bc1c58746e7319_41.png)

![](img/cb19fb3837db13c028bc1c58746e7319_43.png)

## 总结

![](img/cb19fb3837db13c028bc1c58746e7319_45.png)

本节课中我们一起学习了获取因子指标数据的完整流程：
1.  我们首先使用`get_trading_date`函数获取了指定时间段的交易日列表。
2.  接着，我们通过指定因子、构建股票池和查询语句，使用`get_fundamentals`函数获取了特定交易日的因子数据。
3.  最后，我们查看了数据的结构，了解到数据以三维形式存储，并学会了如何提取具体的数值进行分析。

![](img/cb19fb3837db13c028bc1c58746e7319_47.png)

![](img/cb19fb3837db13c028bc1c58746e7319_49.png)

目前，我们完成了获取**指定日期**因子数据的操作。在接下来的课程中，我们将学习如何循环获取**一段时间内**的因子数据，并进行更深入的因子分析。