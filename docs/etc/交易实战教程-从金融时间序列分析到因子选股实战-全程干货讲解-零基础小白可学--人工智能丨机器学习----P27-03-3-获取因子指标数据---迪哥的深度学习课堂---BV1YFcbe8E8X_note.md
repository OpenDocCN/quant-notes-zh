# Python金融分析与量化交易实战教程：P27：03-3-获取因子指标数据 📊

在本节课中，我们将学习如何使用`alphalens`库获取因子指标数据。我们将从获取交易日数据开始，然后构建查询语句来获取特定因子（如市盈率）在所有股票中的数值，并最终理解数据的结构。

---

## 概述

上一节我们介绍了量化分析的基本框架和工具包导入。本节中，我们来看看如何具体获取用于因子分析的金融数据。核心任务是：**获取指定时间段内，所有股票的特定因子数据**。

---

## 第一步：获取交易日数据

与回测框架不同，`alphalens`模块没有内置的每日数据获取机制。因此，我们需要手动获取一系列交易日数据，以便后续查询。

以下是获取交易日数据的步骤：

1.  **定义函数**：创建一个函数来获取指定起始日期和结束日期之间的所有交易日。
2.  **调用函数**：传入具体的日期范围（例如2019-01-01至2020-01-01）。
3.  **检查结果**：验证获取的交易日列表是否正确。

```python
# 获取交易日数据
def get_trading_dates(start_date, end_date):
    """
    获取指定日期范围内的所有交易日。
    参数:
        start_date (str): 起始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        trading_dates (list): 交易日列表。
    """
    trading_dates = get_trading_dates(start_date, end_date)
    return trading_dates

# 示例：获取2019年全年的交易日
trading_dates = get_trading_dates('2019-01-01', '2020-01-01')
print(trading_dates[:5])  # 打印前5个交易日进行检查
```

执行上述代码后，我们就得到了一个包含所有目标交易日的列表 `trading_dates`。

---

## 第二步：获取股票池与构建查询

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_1.png)

有了交易日数据，接下来我们需要获取要分析的股票列表（股票池），并构建查询特定因子数据的语句。

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_3.png)

以下是获取因子指标数据的步骤：

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_5.png)

1.  **确定股票池**：这里我们选择获取沪深300指数的所有成分股作为我们的股票池。
2.  **选择因子**：确定要分析的因子，例如市盈率（`pe_ratio`）。
3.  **构建查询语句**：使用`query`函数构建一个查询，指定要获取的因子字段，并过滤出属于我们股票池的股票。

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_7.png)

```python
# 1. 获取股票池（以沪深300为例）
stock_pool = get_index_stocks('000300.XSHG')  # 获取沪深300成分股代码
print(f"股票池中共有 {len(stock_pool)} 只股票")

# 2. 构建查询语句
# 假设我们要查询的因子是市盈率 (pe_ratio)
factor_name = 'pe_ratio'

# 构建查询
q = query(
    fundamentals.eod_derivative_indicator.pe_ratio  # 查询市盈率字段
).filter(
    fundamentals.eod_derivative_indicator.code.in_(stock_pool)  # 过滤股票池中的股票
)
```

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_9.png)

这段代码定义了一个查询`q`，它的含义是：**获取股票池中所有股票在指定日期的市盈率数据**。

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_11.png)

---

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_13.png)

## 第三步：执行查询并理解数据结构

查询语句构建好后，我们需要传入具体的日期来执行查询，并获取数据。

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_15.png)

以下是执行查询的步骤：

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_17.png)

1.  **执行查询**：使用`get_fundamentals`函数，传入查询语句`q`和指定的交易日。
2.  **检查数据**：查看返回数据的形状和内容，理解其结构。

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_19.png)

```python
# 执行查询，获取第一个交易日（2019-01-02）的数据
# 注意：trading_dates[0] 是第一个交易日
target_date = trading_dates[0]
factor_data = get_fundamentals(q, target_date)

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_21.png)

# 检查数据的形状
print(f"数据形状 (shape): {factor_data.shape}")

# 查看前5条数据
print(factor_data.iloc[0, 0, :5])  # 通常数据是三维的，我们主要关注最后一个维度
```

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_23.png)

**代码与结果解释**：
*   `factor_data.shape` 的输出可能类似于 `(1, 1, 300)`。这表示数据是一个三维结构。
    *   前两个维度（1,1）通常是占位符，没有实际数据意义。
    *   最后一个维度（300）对应我们股票池中的300只股票，这里存储着每只股票的市盈率数值。
*   `factor_data.iloc[0, 0, :5]` 会打印出前5只股票的市盈率数据。

此时，我们已经成功获取了**特定一天**的因子数据。数据中包含了股票代码和对应的因子值。

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_25.png)

---

## 当前进展与下一步

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_27.png)

现在我们已经完成了**获取指定日期的因子数据**。

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_29.png)

但是，我们的目标是分析一段时间内（例如一整年）的因子表现。因此，当前的代码只获取了一天的数据，这还不够。

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_31.png)

在下一节中，我们将学习如何**循环遍历所有交易日**，获取完整的时序因子数据，为后续的因子有效性分析和选股策略构建打下基础。

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_33.png)

---

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_35.png)

## 总结

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_37.png)

本节课中我们一起学习了：
1.  **获取交易日列表**：使用 `get_trading_dates` 函数获取特定时间段的交易日。
2.  **构建股票池与查询**：确定分析范围（如沪深300），并使用 `query()` 和 `filter()` 构建获取特定因子（如市盈率）的查询语句。
3.  **执行查询与理解数据**：使用 `get_fundamentals(q, date)` 执行查询，并理解了返回的三维数据结构，其中核心数据存储在最后一个维度中。

![](img/4b6b48220a65bc5b55b4dc8a4b0c8dcf_39.png)

通过以上步骤，我们掌握了从金融数据平台获取原始因子数据的基本方法。这是进行量化因子分析的第一步。