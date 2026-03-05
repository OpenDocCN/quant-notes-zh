# Python金融量化交易：P42：获取因子指标数据 📊

![](img/9e8c877bd4f52aec63477898f6ceaf4a_1.png)

在本节课中，我们将学习如何使用 `alphalens` 库获取股票因子数据。我们将从指定日期范围开始，查询特定因子（如市盈率）在所有股票中的数值，并初步查看数据格式。

![](img/9e8c877bd4f52aec63477898f6ceaf4a_3.png)

---

![](img/9e8c877bd4f52aec63477898f6ceaf4a_5.png)

## 导入必要工具包

首先，我们已经导入了所有必要的工具包。`alphalens` 库中的几个模块将帮助我们完成任务。

*   `utils` 模块用于处理数据。
*   `plotting` 模块用于绘图。
*   其余模块将用于后续的因子分析。

![](img/9e8c877bd4f52aec63477898f6ceaf4a_7.png)

---

![](img/9e8c877bd4f52aec63477898f6ceaf4a_9.png)

## 选择日期数据

![](img/9e8c877bd4f52aec63477898f6ceaf4a_11.png)

上一节我们介绍了所需的工具包。本节中，我们来看看如何获取分析所需的日期数据。

与回测框架不同，当前模块没有自动按日执行的 `handle_bar` 函数。因此，我们需要手动获取多天的数据。第一步是选择一个日期范围。

![](img/9e8c877bd4f52aec63477898f6ceaf4a_13.png)

以下是获取交易日期的步骤：

![](img/9e8c877bd4f52aec63477898f6ceaf4a_15.png)

1.  定义一个函数 `get_trading_date`。
2.  传入两个参数：起始日期（如 `2019-01-01`）和结束日期（如 `2020-01-01`）。
3.  函数将返回该时间段内所有的交易日列表。

代码示例如下：
```python
def get_trading_date(start_date, end_date):
    trading_dates = get_trading_dates(start_date, end_date)
    return trading_dates
```
执行此函数后，我们就获得了指定时间段内的所有交易日列表。

![](img/9e8c877bd4f52aec63477898f6ceaf4a_17.png)

---

![](img/9e8c877bd4f52aec63477898f6ceaf4a_19.png)

![](img/9e8c877bd4f52aec63477898f6ceaf4a_21.png)

## 获取因子指标数据

![](img/9e8c877bd4f52aec63477898f6ceaf4a_23.png)

在获得了交易日列表后，本节我们将进行核心操作：查询并获取具体的因子指标数据。

![](img/9e8c877bd4f52aec63477898f6ceaf4a_25.png)

我们需要查询一个特定的因子（例如市盈率 `pe_ratio`）在所有股票中的数值。首先，我们需要获取股票池。

![](img/9e8c877bd4f52aec63477898f6ceaf4a_27.png)

![](img/9e8c877bd4f52aec63477898f6ceaf4a_29.png)

以下是获取因子数据的步骤：

![](img/9e8c877bd4f52aec63477898f6ceaf4a_31.png)

1.  **定义股票池**：获取所有A股股票代码，例如沪深300成分股，并将其存储在一个列表中。
    ```python
    stock_pool = get_index_stocks('000300.XSHG')
    ```
2.  **构建查询语句**：使用 `query` 函数构建查询。我们需要指定要查询的因子，并过滤出属于我们股票池的股票。
    ```python
    q = query(fundamentals.eod_derivative_indicator.pe_ratio).filter(fundamentals.stockcode.in_(stock_pool))
    ```
    *   `fundamentals.eod_derivative_indicator.pe_ratio` 指定查询市盈率因子。
    *   `.filter(...)` 用于筛选股票代码在 `stock_pool` 列表中的股票。
3.  **执行查询**：使用 `get_fundamentals` 函数执行查询，并传入查询语句 `q` 和具体的查询日期（例如交易日列表中的第一天）。
    ```python
    factor_data = get_fundamentals(q, trading_dates[0])
    ```
4.  **查看数据**：获取的数据是一个三维结构。通常我们只关心最后一个维度的具体数值。可以使用 `.iloc` 方法查看前几条数据。
    ```python
    print(factor_data.iloc[0, 0, :5]) # 查看前5只股票的因子数据
    ```

![](img/9e8c877bd4f52aec63477898f6ceaf4a_33.png)

执行以上代码后，我们将得到指定日期（例如2019年1月2日）下，股票池中所有股票的市盈率数据。输出结果包含股票代码和对应的因子值。

![](img/9e8c877bd4f52aec63477898f6ceaf4a_35.png)

---

![](img/9e8c877bd4f52aec63477898f6ceaf4a_37.png)

## 当前进展与后续目标

![](img/9e8c877bd4f52aec63477898f6ceaf4a_39.png)

![](img/9e8c877bd4f52aec63477898f6ceaf4a_41.png)

本节课中，我们一起学习了如何获取指定单个日期的因子数据。

![](img/9e8c877bd4f52aec63477898f6ceaf4a_43.png)

![](img/9e8c877bd4f52aec63477898f6ceaf4a_45.png)

我们已经完成了**获取指定日期因子数据**的操作。接下来，我们的目标是将这个操作扩展到整个日期范围，即获取**一段时间内**每一天的因子数据，为后续的因子分析和有效性检验做准备。这将是下一节的重点内容。

![](img/9e8c877bd4f52aec63477898f6ceaf4a_47.png)

![](img/9e8c877bd4f52aec63477898f6ceaf4a_49.png)

---