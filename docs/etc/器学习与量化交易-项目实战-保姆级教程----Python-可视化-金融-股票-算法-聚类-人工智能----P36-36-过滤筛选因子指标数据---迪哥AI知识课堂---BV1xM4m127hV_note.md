# 机器学习与量化交易：P36：过滤筛选因子指标数据 📊

![](img/4beee5426ac8e1a04762c2ef829e472f_0.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_2.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_4.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_6.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的因子指标数据。这是构建量化交易策略中数据准备的关键一步。

![](img/4beee5426ac8e1a04762c2ef829e472f_8.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_10.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_12.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_14.png)

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何在实际操作中过滤掉不符合条件的股票，并提取有效的因子数据。

![](img/4beee5426ac8e1a04762c2ef829e472f_16.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_18.png)

## 过滤不想要的股票 🚫

![](img/4beee5426ac8e1a04762c2ef829e472f_20.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_22.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_24.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_26.png)

在构建股票池时，我们首先需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一目标的步骤：

![](img/4beee5426ac8e1a04762c2ef829e472f_28.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_30.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_32.png)

首先，我们需要编写一个过滤函数，用于判断股票是否停牌。

![](img/4beee5426ac8e1a04762c2ef829e472f_34.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_36.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_38.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_40.png)

```python
def filter_suspended_stocks(stock_list):
    filtered_stocks = []
    for stock in stock_list:
        if not is_suspended(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/4beee5426ac8e1a04762c2ef829e472f_42.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_44.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_46.png)

接下来，我们还需要过滤掉ST股。其逻辑与过滤停牌股类似。

![](img/4beee5426ac8e1a04762c2ef829e472f_48.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_50.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_52.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_54.png)

```python
def filter_st_stocks(stock_list):
    filtered_stocks = []
    for stock in stock_list:
        if not is_st(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/4beee5426ac8e1a04762c2ef829e472f_56.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_58.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_60.png)

最后，我们需要过滤掉上市时间过短的新股。这里我们假设上市天数少于180天的股票为新股。

![](img/4beee5426ac8e1a04762c2ef829e472f_62.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_64.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_66.png)

```python
def filter_new_stocks(stock_list):
    filtered_stocks = []
    for stock in stock_list:
        if days_from_listed(stock) > 180:
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/4beee5426ac8e1a04762c2ef829e472f_68.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_70.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_72.png)

## 执行多步过滤 🔄

![](img/4beee5426ac8e1a04762c2ef829e472f_74.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_76.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_78.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_80.png)

现在，我们将上述三个过滤函数串联起来，对初始股票池进行逐步筛选。

![](img/4beee5426ac8e1a04762c2ef829e472f_82.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_84.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_86.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_88.png)

```python
# 假设 initial_stocks 是初始的股票列表
filtered_by_suspension = filter_suspended_stocks(initial_stocks)
filtered_by_st = filter_st_stocks(filtered_by_suspension)
final_filtered_stocks = filter_new_stocks(filtered_by_st)
```

![](img/4beee5426ac8e1a04762c2ef829e472f_90.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_92.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_94.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_96.png)

通过以上步骤，我们得到了一个经过初步清洗的、更符合交易逻辑的股票池。

![](img/4beee5426ac8e1a04762c2ef829e472f_98.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_100.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_102.png)

## 查询所需的因子指标 📈

![](img/4beee5426ac8e1a04762c2ef829e472f_104.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_106.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_108.png)

在获得干净的股票池后，下一步是获取我们策略中需要的因子指标数据，例如市净率（PB）和市值（Market Cap）。

![](img/4beee5426ac8e1a04762c2ef829e472f_110.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_112.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_114.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_116.png)

以下是使用 `get_fundamentals` API 查询这些指标的方法：

![](img/4beee5426ac8e1a04762c2ef829e472f_118.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_120.png)

```python
# 构建查询语句，获取市净率(pb_ratio)和市值(market_cap)数据
query = query(fundamentals.eod_derivative_indicator.pb_ratio,
              fundamentals.eod_derivative_indicator.market_cap)

![](img/4beee5426ac8e1a04762c2ef829e472f_122.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_124.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_126.png)

# 将查询范围限定在我们过滤后的股票池中
# 假设 final_filtered_stocks 是股票代码列表
filter_condition = fundamentals.stockcode.in_(final_filtered_stocks)

![](img/4beee5426ac8e1a04762c2ef829e472f_128.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_130.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_132.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_134.png)

# 执行查询
factor_data = get_fundamentals(query, filter=filter_condition)
```

![](img/4beee5426ac8e1a04762c2ef829e472f_136.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_138.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_140.png)

## 数据格式转换与处理 🔧

![](img/4beee5426ac8e1a04762c2ef829e472f_142.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_144.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_146.png)

查询返回的数据格式可能不符合我们的分析需求。通常，我们需要将数据从宽格式转换为长格式，并处理可能存在的空值。

![](img/4beee5426ac8e1a04762c2ef829e472f_148.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_150.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_152.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_154.png)

```python
# 对数据进行转置，使每一行代表一只股票，每一列代表一个因子
factor_data_transposed = factor_data.T

![](img/4beee5426ac8e1a04762c2ef829e472f_156.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_158.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_160.png)

# 删除包含空值的行，确保数据完整性
factor_data_clean = factor_data_transposed.dropna()
```

![](img/4beee5426ac8e1a04762c2ef829e472f_162.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_164.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_166.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_168.png)

经过转置和清洗，我们得到了一个干净、结构化的因子数据表，可以用于后续的模型构建和分析。

![](img/4beee5426ac8e1a04762c2ef829e472f_170.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_172.png)

![](img/4beee5426ac8e1a04762c2ef829e472f_174.png)

本节课中我们一起学习了如何对股票池进行过滤筛选，以及如何查询和处理因子指标数据。这是将理论策略转化为实际代码操作的重要基础步骤。