# Python金融分析与量化交易实战教程：P34：02-2-过滤筛选因子指标数据 📊

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_1.png)

在本节课中，我们将学习如何对股票池进行过滤筛选，并获取我们所需的因子指标数据。这是构建量化交易策略的关键一步，目的是剔除无效或不符合条件的股票，并提取用于分析和决策的核心财务数据。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_3.png)

上一节我们介绍了如何获取初始的股票池，本节中我们来看看如何对这些股票进行清洗和筛选。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_5.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_7.png)

## 过滤不想要的股票 🚫

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_9.png)

在量化策略中，我们通常需要剔除一些不符合交易条件的股票，例如停牌股、ST股和新上市股票。以下是实现这一功能的步骤。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_11.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_13.png)

首先，我们需要编写一个过滤函数。这个函数接收一个股票列表，并返回过滤后的列表。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_15.png)

```python
def filter_stocks(stock_list):
    # 初始化一个空列表，用于存放过滤后的股票
    filtered_stocks = []
    # 遍历传入的股票列表
    for stock in stock_list:
        # 判断该股票是否全年停牌
        if not is_suspended(stock):
            # 如果不是停牌股，则加入过滤列表
            filtered_stocks.append(stock)
    # 返回过滤后的股票列表
    return filtered_stocks
```

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_17.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_19.png)

接下来，我们还需要判断股票是否为ST股。逻辑与判断停牌类似。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_21.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_23.png)

```python
def filter_st_stocks(stock_list):
    filtered_stocks = []
    for stock in stock_list:
        # 判断该股票是否为ST股
        if not is_st(stock):
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_25.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_27.png)

最后，我们需要过滤掉新上市的股票。这里我们通过判断股票上市天数是否大于某个阈值（例如180天）来实现。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_29.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_31.png)

```python
def filter_new_stocks(stock_list):
    filtered_stocks = []
    for stock in stock_list:
        # 判断该股票上市天数是否大于180天
        if days_from_listed(stock) > 180:
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_33.png)

现在，我们有了三个独立的过滤函数。在实际应用中，我们需要依次调用它们来对初始股票池进行层层筛选。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_35.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_37.png)

```python
# 假设 initial_stocks 是初始获取的所有股票列表
# 第一步：过滤停牌股
filtered_by_suspension = filter_stocks(initial_stocks)
# 第二步：过滤ST股
filtered_by_st = filter_st_stocks(filtered_by_suspension)
# 第三步：过滤新股
final_stocks = filter_new_stocks(filtered_by_st)
```

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_39.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_41.png)

通过以上步骤，我们就得到了一个经过初步清洗的、符合基本交易条件的股票池。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_43.png)

## 查询所需的因子指标 🔍

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_45.png)

在得到干净的股票池后，下一步是获取我们策略所需的因子指标数据，例如市盈率（PE）和市值（Market Cap）。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_47.png)

以下是查询这些指标的方法。我们使用 `get_fundamentals` 函数，并需要构建一个查询语句。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_49.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_51.png)

```python
# 构建查询语句，获取市盈率(pe_ratio)和市值(market_cap)指标
query = query(
    fundamentals.eod_derivative_indicator.pe_ratio,
    fundamentals.eod_derivative_indicator.market_cap
).filter(
    fundamentals.stockcode.in_(final_stocks)  # 只查询我们过滤后股票池中的股票
)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_53.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_55.png)

# 执行查询
factor_data = get_fundamentals(query)
```

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_57.png)

查询返回的数据格式通常是横向的，即每一行代表一个指标，每一列代表一只股票。但为了便于后续分析，我们通常需要将其转置，使每一行代表一只股票，每一列代表一个指标。同时，我们还需要处理可能存在的空值。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_59.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_61.png)

```python
# 转置数据框，使股票为行，指标为列
factor_data_transposed = factor_data.T
# 删除包含空值的行（简单处理）
factor_data_clean = factor_data_transposed.dropna()
```

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_63.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_65.png)

这样，我们就得到了一个干净、结构化的因子指标数据框 `factor_data_clean`，可以用于后续的因子分析和选股策略。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_67.png)

## 总结 📝

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_69.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_71.png)

本节课中我们一起学习了量化策略中数据准备的两个核心环节。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_73.png)

首先，我们编写了过滤函数，用于从初始股票池中剔除**停牌股**、**ST股**和**新上市股票**，确保后续分析基于有效且稳定的标的。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_75.png)

其次，我们使用 `get_fundamentals` 函数查询了所需的**因子指标**（如市盈率和市值），并对查询结果进行了转置和空值处理，得到了便于分析的数据结构。

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_77.png)

![](img/5c3ddf251a3ca7886af5c58da3bc71ce_79.png)

这些步骤是构建任何量化交易模型的基础，干净、准确的数据是做出有效决策的前提。在接下来的课程中，我们将利用这些处理好的数据来构建和测试具体的选股策略。