# Python金融分析与量化交易实战教程：P33：2-过滤筛选因子指标数据 📊

![](img/89db0909c72a9abd3bc708e777a9594e_1.png)

## 概述
在本节课中，我们将学习如何对股票池进行过滤和筛选，以剔除不符合交易条件的股票（如停牌股、ST股、新股），并获取我们所需的财务指标数据（如市盈率、市净率），为后续的量化策略构建准备干净、有效的数据基础。

![](img/89db0909c72a9abd3bc708e777a9594e_3.png)

![](img/89db0909c72a9abd3bc708e777a9594e_5.png)

![](img/89db0909c72a9abd3bc708e777a9594e_7.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何对原始股票数据进行预处理。

## 过滤不想要的股票 🚫
在构建股票池时，并非所有股票都适合纳入策略。我们需要过滤掉以下几类股票：
*   **停牌股票**：无法交易。
*   **ST/S*ST股票**：存在退市风险或其他特别处理情况。
*   **新上市股票**：上市时间过短，数据不充分，价格波动可能异常。

![](img/89db0909c72a9abd3bc708e777a9594e_9.png)

![](img/89db0909c72a9abd3bc708e777a9594e_11.png)

![](img/89db0909c72a9abd3bc708e777a9594e_13.png)

以下是实现过滤功能的步骤：

![](img/89db0909c72a9abd3bc708e777a9594e_15.png)

![](img/89db0909c72a9abd3bc708e777a9594e_17.png)

首先，我们需要编写一个过滤函数。该函数接收一个股票列表（stock list），并遍历列表中的每一只股票进行判断。

![](img/89db0909c72a9abd3bc708e777a9594e_19.png)

![](img/89db0909c72a9abd3bc708e777a9594e_21.png)

**1. 过滤停牌股票**
使用API判断股票是否全年停牌。如果停牌，则将其从列表中移除。
```python
def filter_suspended(stock_list):
    filtered_list = []
    for stock in stock_list:
        if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API
            filtered_list.append(stock)
    return filtered_list
```

![](img/89db0909c72a9abd3bc708e777a9594e_23.png)

![](img/89db0909c72a9abd3bc708e777a9594e_25.png)

**2. 过滤ST/S*ST股票**
逻辑与过滤停牌股类似，使用对应的API进行判断。
```python
def filter_st(stock_list):
    filtered_list = []
    for stock in stock_list:
        if not is_st(stock):  # 假设 is_st 是判断ST股的API
            filtered_list.append(stock)
    return filtered_list
```

![](img/89db0909c72a9abd3bc708e777a9594e_27.png)

![](img/89db0909c72a9abd3bc708e777a9594e_29.png)

![](img/89db0909c72a9abd3bc708e777a9594e_31.png)

**3. 过滤新上市股票**
通过判断股票上市天数是否超过设定的阈值（例如180天）来过滤新股。
```python
def filter_new(stock_list):
    filtered_list = []
    for stock in stock_list:
        if days_from_listed(stock) > 180:  # 假设 days_from_listed 是获取上市天数的API
            filtered_list.append(stock)
    return filtered_list
```

![](img/89db0909c72a9abd3bc708e777a9594e_33.png)

现在，我们已经定义了三个过滤函数。接下来，我们将按顺序应用这些函数来清洗我们的初始股票池。

![](img/89db0909c72a9abd3bc708e777a9594e_35.png)

![](img/89db0909c72a9abd3bc708e777a9594e_37.png)

![](img/89db0909c72a9abd3bc708e777a9594e_39.png)

首先，过滤掉停牌的股票。
```python
stocks = get_all_securities()  # 获取初始股票池
stocks = filter_suspended(stocks)
```

![](img/89db0909c72a9abd3bc708e777a9594e_41.png)

![](img/89db0909c72a9abd3bc708e777a9594e_43.png)

接着，在已过滤停牌股的基础上，继续过滤ST股。
```python
stocks = filter_st(stocks)
```

![](img/89db0909c72a9abd3bc708e777a9594e_45.png)

最后，过滤掉上市时间过短的新股。
```python
stocks = filter_new(stocks)
```

![](img/89db0909c72a9abd3bc708e777a9594e_47.png)

经过以上三步过滤，我们得到了一个相对干净、可用于后续分析的股票池。

![](img/89db0909c72a9abd3bc708e777a9594e_49.png)

![](img/89db0909c72a9abd3bc708e777a9594e_51.png)

![](img/89db0909c72a9abd3bc708e777a9594e_53.png)

## 查询所需的指标数据 🔍
股票池准备就绪后，下一步是获取构建因子策略所需的财务指标数据，例如市盈率（PE）和市值（Market Cap）。

![](img/89db0909c72a9abd3bc708e777a9594e_55.png)

以下是查询指标数据的步骤：

![](img/89db0909c72a9abd3bc708e777a9594e_57.png)

![](img/89db0909c72a9abd3bc708e777a9594e_59.png)

![](img/89db0909c72a9abd3bc708e777a9594e_61.png)

我们将使用 `get_fundamentals` 函数来查询数据。该函数需要传入一个查询语句（query），以指定要获取的指标。

![](img/89db0909c72a9abd3bc708e777a9594e_63.png)

![](img/89db0909c72a9abd3bc708e777a9594e_65.png)

**1. 构建查询语句**
在查询语句中，我们需要指定要查询的指标（如 `pe_ratio` 代表市盈率，`market_cap` 代表市值），并通过 `filter` 条件限定只查询我们股票池中的股票。
```python
query = query(
    fundamentals.eod_derivative_indicator.pe_ratio,
    fundamentals.eod_derivative_indicator.market_cap
).filter(
    fundamentals.income_statement.code.in_(stocks)  # 只查询过滤后股票池中的股票
)
```

![](img/89db0909c72a9abd3bc708e777a9594e_67.png)

**2. 执行查询并处理数据**
执行查询后，获得的数据格式通常是横表（指标为列，股票为行）。为了便于后续分析，我们通常需要将其转换为竖表（股票为列，指标为行），即进行转置操作。同时，处理可能存在的空值。
```python
fundamental_data = get_fundamentals(query)
# 进行数据转置，并丢弃包含空值的行
processed_data = fundamental_data.T.dropna()
```

![](img/89db0909c72a9abd3bc708e777a9594e_69.png)

![](img/89db0909c72a9abd3bc708e777a9594e_71.png)

这样，我们就得到了一个经过清洗和转置的、包含目标股票市盈率和市值的数据表。

![](img/89db0909c72a9abd3bc708e777a9594e_73.png)

## 总结
本节课中我们一起学习了量化策略数据准备的关键两步：
1.  **股票过滤**：通过编写并依次应用过滤函数，移除了停牌股、ST股和新股，确保了股票池的交易有效性和数据稳定性。
2.  **指标查询**：使用 `get_fundamentals` 函数查询所需的财务指标，并通过数据转置和空值处理，将原始数据转换为适合后续因子分析和策略回测的格式。

![](img/89db0909c72a9abd3bc708e777a9594e_75.png)

![](img/89db0909c72a9abd3bc708e777a9594e_77.png)

经过本节的预处理，我们为下一阶段——基于这些因子指标构建和测试具体的交易策略——打下了坚实的数据基础。