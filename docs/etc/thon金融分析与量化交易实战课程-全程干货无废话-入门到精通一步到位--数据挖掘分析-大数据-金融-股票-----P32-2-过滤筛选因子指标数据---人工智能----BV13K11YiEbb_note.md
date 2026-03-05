# Python金融分析与量化交易实战课程：P32：2-过滤筛选因子指标数据 📊

![](img/6a88f499bb59ea7b94fb529fe6d7f432_1.png)

## 概述
在本节课中，我们将学习如何对股票池进行过滤筛选，并获取我们所需的因子指标数据。这是构建量化交易策略的关键步骤，目的是剔除不符合条件的股票，并计算其财务指标。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_3.png)

## 过滤不想要的股票
上一节我们介绍了如何获取股票池，本节中我们来看看如何过滤掉我们不想要的股票。例如，停牌的股票、ST/*ST股票以及新上市的股票通常不适合纳入我们的策略。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_5.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_7.png)

我们需要编写几个函数来完成过滤工作。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_9.png)

### 1. 过滤停牌股票
首先，我们需要过滤掉停牌的股票。我们可以使用一个API来判断股票是否全天停牌。

以下是过滤停牌股票的代码：

![](img/6a88f499bb59ea7b94fb529fe6d7f432_11.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_13.png)

```python
def filter_suspended_stocks(stock_list):
    """
    过滤掉停牌的股票。
    """
    filtered_stocks = []
    for stock in stock_list:
        if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/6a88f499bb59ea7b94fb529fe6d7f432_15.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_17.png)

### 2. 过滤ST/*ST股票
接下来，我们需要过滤掉ST和*ST股票。这些股票通常存在财务或其他异常状况。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_19.png)

以下是过滤ST/*ST股票的代码：

![](img/6a88f499bb59ea7b94fb529fe6d7f432_21.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_23.png)

```python
def filter_st_stocks(stock_list):
    """
    过滤掉ST和*ST股票。
    """
    filtered_stocks = []
    for stock in stock_list:
        if not is_st(stock):  # 假设 is_st 是判断ST/*ST的API
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/6a88f499bb59ea7b94fb529fe6d7f432_25.png)

### 3. 过滤新上市股票
最后，我们需要过滤掉新上市的股票。例如，我们可以设定上市天数必须大于180天（约半年）。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_27.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_29.png)

以下是过滤新上市股票的代码：

![](img/6a88f499bb59ea7b94fb529fe6d7f432_31.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_33.png)

```python
def filter_new_stocks(stock_list):
    """
    过滤掉上市天数过少（例如少于180天）的股票。
    """
    filtered_stocks = []
    for stock in stock_list:
        if get_listed_days(stock) > 180:  # 假设 get_listed_days 是获取上市天数的API
            filtered_stocks.append(stock)
    return filtered_stocks
```

![](img/6a88f499bb59ea7b94fb529fe6d7f432_35.png)

## 执行过滤操作
现在，我们已经定义了三个过滤函数。接下来，我们将它们依次应用到我们的初始股票池上。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_37.png)

以下是执行过滤操作的步骤：

![](img/6a88f499bb59ea7b94fb529fe6d7f432_39.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_41.png)

1.  首先，过滤掉停牌的股票。
2.  然后，从剩余股票中过滤掉ST/*ST股票。
3.  最后，过滤掉新上市的股票。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_43.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_45.png)

```python
# 假设 all_stocks 是初始的股票列表
filtered_by_suspension = filter_suspended_stocks(all_stocks)
filtered_by_st = filter_st_stocks(filtered_by_suspension)
final_stocks = filter_new_stocks(filtered_by_st)
```

经过以上步骤，`final_stocks` 就是我们过滤后得到的、符合基本条件的股票池。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_47.png)

## 查询所需的因子指标
上一节我们过滤了股票池，本节中我们来看看如何为这些股票查询我们需要的因子指标。在本策略中，我们关注**市净率（PB）**和**市值（Market Cap）**这两个指标。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_49.png)

我们使用 `get_fundamentals` 函数来查询财务数据。该函数需要传入一个查询语句（query）来指定要获取的指标。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_51.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_53.png)

以下是查询市净率和市值的代码：

![](img/6a88f499bb59ea7b94fb529fe6d7f432_55.png)

```python
# 构建查询语句，查询市净率(pb_ratio)和市值(market_cap)
query = query(
    fundamentals.eod_derivative_indicator.pb_ratio,
    fundamentals.eod_derivative_indicator.market_cap
).filter(
    fundamentals.income_statement.stockcode.in_(final_stocks)  # 只查询我们过滤后的股票
)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_57.png)

# 执行查询
factor_data = get_fundamentals(query)
```

![](img/6a88f499bb59ea7b94fb529fe6d7f432_59.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_61.png)

## 处理查询结果
查询返回的数据格式可能不是我们直接想要的（通常是横表，我们需要竖表）。此外，数据中可能存在空值。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_63.png)

我们需要对数据进行转置，并处理空值。一个简单的方法是删除包含空值的行。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_65.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_67.png)

以下是处理数据的代码：

```python
# 转置数据，使每一行代表一只股票
factor_data = factor_data.T

![](img/6a88f499bb59ea7b94fb529fe6d7f432_69.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_71.png)

# 删除包含空值（NaN）的行
factor_data = factor_data.dropna()
```

![](img/6a88f499bb59ea7b94fb529fe6d7f432_73.png)

经过这些操作，`factor_data` 就是一个干净的数据框，其中包含了我们最终股票池中每只股票的市净率和市值数据，可以用于后续的因子分析和选股。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_75.png)

## 总结
本节课中我们一起学习了量化交易策略中数据准备的关键步骤。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_77.png)

1.  **股票过滤**：我们编写了三个函数，分别用于过滤停牌股票、ST/*ST股票和新上市股票，确保股票池的质量。
2.  **指标查询**：我们使用 `get_fundamentals` 函数，通过构建查询语句，获取了过滤后股票的市净率（PB）和市值（Market Cap）指标。
3.  **数据处理**：我们对查询结果进行了转置和空值处理，得到了结构清晰、可供分析的数据。

![](img/6a88f499bb59ea7b94fb529fe6d7f432_79.png)

![](img/6a88f499bb59ea7b94fb529fe6d7f432_81.png)

这些步骤为我们后续的因子打分、股票排序和构建投资组合打下了坚实的基础。下一节，我们将学习如何利用这些因子数据对股票进行评分和筛选。