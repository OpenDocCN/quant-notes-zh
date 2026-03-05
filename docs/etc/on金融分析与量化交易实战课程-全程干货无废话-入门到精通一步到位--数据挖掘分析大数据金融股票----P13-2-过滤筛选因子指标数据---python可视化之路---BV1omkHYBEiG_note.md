# 金融数据分析：P13：2-过滤筛选因子指标数据 📊

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_0.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_2.png)

在本节课中，我们将学习如何对股票池进行过滤筛选，并获取我们所需的财务指标数据。这是构建量化交易策略中非常关键的一步，目的是剔除无效或不符合条件的股票，并精确提取用于分析的因子数据。

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_4.png)

## 过滤不想要的股票 🚫

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_6.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_8.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_10.png)

上一节我们介绍了如何获取初始的股票列表。本节中，我们来看看如何对这些股票进行初步筛选，剔除那些我们不希望纳入分析范围的股票，例如停牌股、ST股和新上市股票。

以下是实现这一功能的三个核心过滤函数：

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_12.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_14.png)

```python
def filter_suspended(stock_list):
    """
    判断股票是否全年停牌。
    """
    filtered_list = []
    for stock in stock_list:
        if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API
            filtered_list.append(stock)
    return filtered_list

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_16.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_18.png)

def filter_st(stock_list):
    """
    判断股票是否为ST股。
    """
    filtered_list = []
    for stock in stock_list:
        if not is_st(stock):  # 假设 is_st 是判断ST股的API
            filtered_list.append(stock)
    return filtered_list

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_20.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_22.png)

def filter_new(stock_list):
    """
    判断股票是否为新股（例如上市天数少于180天）。
    """
    filtered_list = []
    for stock in stock_list:
        if days_from_listed(stock) > 180:  # 假设 days_from_listed 是获取上市天数的API
            filtered_list.append(stock)
    return filtered_list
```

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_24.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_26.png)

在实际应用中，我们可以依次调用这些函数，对初始股票池进行层层过滤：

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_28.png)

```python
# 假设 all_stocks 是初始获取的所有股票列表
filtered_stocks = filter_suspended(all_stocks)
filtered_stocks = filter_st(filtered_stocks)
filtered_stocks = filter_new(filtered_stocks)
```

经过这三步过滤，我们得到了一个相对“干净”的股票池，剔除了停牌股、ST股和上市时间过短的新股。

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_30.png)

## 查询想要的指标 🔍

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_32.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_34.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_36.png)

在得到过滤后的股票列表后，下一步就是获取这些股票的具体财务指标数据，以便进行后续的因子分析和策略构建。

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_38.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_40.png)

我们将使用 `get_fundamentals` 函数来查询数据。这里，我们计划获取两个常用因子：**市净率（PB）** 和 **市值（Market Cap）**。

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_42.png)

以下是查询指标的代码实现：

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_44.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_46.png)

```python
# 构建查询语句，获取市净率(pb_ratio)和市值(market_cap)
query = query(fundamentals.eod_derivative_indicator.pb_ratio,
              fundamentals.eod_derivative_indicator.market_cap)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_48.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_50.png)

# 执行查询，并指定只查询我们过滤后股票列表中的股票
df = get_fundamentals(query, filter=fundamentals.stockcode.in_(filtered_stocks))
```

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_52.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_54.png)

> **注意**：`fundamentals.eod_derivative_indicator.pb_ratio` 和 `fundamentals.eod_derivative_indicator.market_cap` 是示例字段名，实际使用时需根据所用数据平台的API文档进行调整。

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_56.png)

## 处理查询结果 📈

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_58.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_60.png)

直接查询返回的数据格式（DataFrame）可能并不直接符合我们的分析需求。通常，返回的数据是宽表格式，每一行代表一个指标，每一列代表一只股票。

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_62.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_64.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_66.png)

我们更常见的需求是让每一行代表一只股票，每一列代表一个指标。因此，需要对数据进行**转置**操作。同时，数据中可能存在空值（NaN），为了简化处理，我们可以选择直接删除包含空值的行。

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_68.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_70.png)

以下是处理查询结果的代码：

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_72.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_74.png)

```python
# 对查询结果进行转置，使行代表股票，列代表指标
df = df.T

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_76.png)

# 删除包含任何空值的行
df = df.dropna()
```

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_78.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_80.png)

经过转置和清理空值后，`df` 这个DataFrame就变成了一个规整的数据表，其中索引（index）是股票代码，列是我们查询的财务指标（如pb_ratio, market_cap），可以直接用于后续的排序、筛选和建模分析。

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_82.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_84.png)

## 总结 📝

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_86.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_88.png)

本节课中我们一起学习了量化分析中数据准备阶段的两个核心步骤：

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_90.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_92.png)

1.  **股票过滤**：我们编写了三个函数，分别用于过滤掉停牌股、ST股和新上市股票，确保股票池的质量。
2.  **指标查询与处理**：我们使用 `get_fundamentals` 函数查询了目标股票的市净率和市值数据，并对返回的数据进行了转置和空值清理，使其成为适合分析的格式。

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_94.png)

![](img/22a1e4e2fb8edbaae8e47f55ad449c40_96.png)

通过本课的学习，你已经掌握了从海量股票中精准筛选目标、并提取关键财务数据的方法，这是构建任何量化选股策略的坚实基础。下一节，我们将利用这些处理好的数据，开始构建具体的因子选股策略。