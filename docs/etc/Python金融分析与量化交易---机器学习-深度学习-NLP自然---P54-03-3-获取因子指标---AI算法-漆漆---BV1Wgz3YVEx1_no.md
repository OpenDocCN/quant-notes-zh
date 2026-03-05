# 人工智能金融量化：P54：03-3-获取因子指标数据 📊

在本节课中，我们将学习如何使用Python工具包获取金融因子指标数据。这是进行因子分析的第一步，我们将从指定日期范围内获取所有A股股票的特定因子数据。

---

上一节我们介绍了因子分析所需的工具包。本节中，我们来看看如何具体获取数据。

首先，我们需要选择一个日期范围。因为因子分析需要获取多天的数据，而不是像回测框架那样每天执行一次，所以我们需要自己编写代码来获取指定时间段内的所有交易日数据。

以下是获取交易日期的步骤：

1.  定义一个函数 `get_trading_date`，它接收两个参数：起始日期和结束日期。
2.  使用工具包中的函数获取该时间段内所有的交易日列表。
3.  将结果返回，以便后续使用。

![](img/7a5b1afa186f8b519bda725ca0554c82_1.png)

```python
# 获取交易日数据
trading_dates = get_trading_date('2019-01-01', '2020-01-01')
```

![](img/7a5b1afa186f8b519bda725ca0554c82_3.png)

![](img/7a5b1afa186f8b519bda725ca0554c82_5.png)

执行这段代码后，我们就得到了从2019年1月1日到2020年1月1日之间所有的交易日列表。

![](img/7a5b1afa186f8b519bda725ca0554c82_7.png)

---

![](img/7a5b1afa186f8b519bda725ca0554c82_9.png)

获取了日期数据后，接下来我们需要获取具体的因子指标。我们将以市盈率（P/E Ratio）为例，查询所有A股股票的这个因子数据。

![](img/7a5b1afa186f8b519bda725ca0554c82_11.png)

![](img/7a5b1afa186f8b519bda725ca0554c82_13.png)

以下是获取因子指标的步骤：

![](img/7a5b1afa186f8b519bda725ca0554c82_15.png)

1.  定义要查询的因子名称，例如 `pe_ratio`。
2.  获取所有A股股票的代码列表，作为我们的股票池。
3.  构建一个查询语句（query），指定要查询的因子和过滤条件（股票代码在股票池内）。
4.  执行查询，获取指定日期的因子数据。

![](img/7a5b1afa186f8b519bda725ca0554c82_17.png)

![](img/7a5b1afa186f8b519bda725ca0554c82_19.png)

```python
# 定义股票池（所有A股）
stock_pool = get_all_securities(['stock']).index.tolist()

![](img/7a5b1afa186f8b519bda725ca0554c82_21.png)

# 构建查询语句
q = query(
    fundamentals.eod_derivative_indicator.pe_ratio
).filter(
    fundamentals.eod_derivative_indicator.stockcode.in_(stock_pool)
)

![](img/7a5b1afa186f8b519bda725ca0554c82_23.png)

![](img/7a5b1afa186f8b519bda725ca0554c82_25.png)

# 执行查询，获取第一个交易日的数据
factor_data = get_fundamentals(q, date=trading_dates[0])
```

![](img/7a5b1afa186f8b519bda725ca0554c82_27.png)

执行查询后，`factor_data` 是一个三维数据结构。通常我们只关心最后一个维度的数据，它包含了股票代码和对应的因子值。通过查看数据形状和部分内容，可以确认数据已成功获取。

![](img/7a5b1afa186f8b519bda725ca0554c82_29.png)

```python
# 查看数据形状和内容示例
print(factor_data.shape)
print(factor_data.iloc[0, 0, :5])  # 查看前5条数据
```

![](img/7a5b1afa186f8b519bda725ca0554c82_31.png)

![](img/7a5b1afa186f8b519bda725ca0554c82_33.png)

目前，我们完成了获取**指定单日**因子数据的操作。

![](img/7a5b1afa186f8b519bda725ca0554c82_35.png)

![](img/7a5b1afa186f8b519bda725ca0554c82_37.png)

---

![](img/7a5b1afa186f8b519bda725ca0554c82_39.png)

![](img/7a5b1afa186f8b519bda725ca0554c82_41.png)

本节课中我们一起学习了如何获取金融因子数据。我们首先学会了获取指定日期范围内的交易日列表，然后构建查询语句并成功获取了特定因子（如市盈率）在单日的全市场数据。这是进行后续因子分析和量化策略构建的基础。下一节，我们将学习如何获取多日数据并进行初步分析。