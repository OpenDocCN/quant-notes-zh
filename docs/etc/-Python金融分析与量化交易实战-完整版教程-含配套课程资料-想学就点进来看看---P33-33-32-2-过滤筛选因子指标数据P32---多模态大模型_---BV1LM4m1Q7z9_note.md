# Python金融分析与量化交易实战：P33：过滤筛选因子指标数据

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_0.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_2.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_4.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_6.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_8.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_10.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_12.png)

## 概述
在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的财务指标数据。这是构建量化交易策略中数据处理的关键一步。

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_14.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_16.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_18.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现股票数据的过滤与指标查询。

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_20.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_22.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_24.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_26.png)

## 过滤股票池
首先，我们需要从初始的股票池中剔除不符合交易条件的股票。这些条件通常包括停牌、ST标记以及上市时间过短的新股。

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_28.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_30.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_32.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_34.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_36.png)

以下是实现过滤功能的三个核心函数：

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_38.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_40.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_42.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_44.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_46.png)

1.  **过滤停牌股票**
    此函数用于判断并剔除当前处于停牌状态的股票。
    ```python
    def filter_suspended_stocks(stock_list):
        filtered_stocks = []
        for stock in stock_list:
            if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API
                filtered_stocks.append(stock)
        return filtered_stocks
    ```

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_48.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_50.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_52.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_54.png)

2.  **过滤ST股票**
    此函数用于判断并剔除被标记为ST（特别处理）的股票。
    ```python
    def filter_st_stocks(stock_list):
        filtered_stocks = []
        for stock in stock_list:
            if not is_st_stock(stock):  # 假设 is_st_stock 是判断ST股的API
                filtered_stocks.append(stock)
        return filtered_stocks
    ```

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_56.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_58.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_60.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_62.png)

3.  **过滤新股**
    此函数用于过滤掉上市时间过短的股票，例如上市天数少于180天的股票。
    ```python
    def filter_new_stocks(stock_list):
        filtered_stocks = []
        for stock in stock_list:
            if get_days_from_listed(stock) > 180:  # 假设 get_days_from_listed 获取上市天数
                filtered_stocks.append(stock)
        return filtered_stocks
    ```

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_64.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_66.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_68.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_70.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_72.png)

在实际应用中，我们可以链式调用这些函数，对初始股票池进行逐层筛选：
```python
# 假设 all_stocks 是初始股票代码列表
filtered_by_suspension = filter_suspended_stocks(all_stocks)
filtered_by_st = filter_st_stocks(filtered_by_suspension)
final_stock_list = filter_new_stocks(filtered_by_st)
```

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_74.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_76.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_78.png)

## 查询财务指标数据
完成股票筛选后，下一步是获取这些股票的特定财务指标，例如市盈率（P/E Ratio）和市值（Market Cap），用于后续的因子分析。

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_80.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_82.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_84.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_86.png)

以下是查询指标数据的步骤：

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_88.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_90.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_92.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_94.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_96.png)

1.  **使用查询API**
    我们使用 `get_fundamentals` 函数来查询财务数据。需要构建一个查询语句（query）来指定所需的指标。
    ```python
    query_statement = query(
        fundamentals.eod_derivative_indicator.pe_ratio,  # 市盈率
        fundamentals.eod_derivative_indicator.market_cap   # 总市值
    )
    ```

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_98.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_100.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_102.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_104.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_106.png)

2.  **指定查询对象**
    在查询中，我们需要通过 `filter` 条件限定只查询我们筛选后的股票列表。
    ```python
    query_statement = query_statement.filter(
        fundamentals.income_statement.stockcode.in_(final_stock_list)
    )
    ```

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_108.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_110.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_112.png)

3.  **执行查询与数据整理**
    执行查询后，得到的数据格式可能需要进行转置（行变列，列变行），并处理可能存在的空值（NaN），以便于后续分析。
    ```python
    # 执行查询
    factor_data = get_fundamentals(query_statement)

    # 数据转置，使每行代表一只股票
    factor_data = factor_data.T

    # 删除包含空值的行
    factor_data = factor_data.dropna()
    ```

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_114.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_116.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_118.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_120.png)

![](img/37e1b594cd1db0bfd61f25aab3a62fcf_122.png)

## 总结
本节课中我们一起学习了量化策略数据准备阶段的两个核心操作。
首先，我们编写了函数来过滤掉停牌股、ST股和新股，得到了一个干净、可交易的股票池。
接着，我们使用财务数据查询接口，获取了目标股票池的市盈率和市值指标，并对查询结果进行了格式整理，为下一步的因子分析和策略构建打下了坚实的数据基础。