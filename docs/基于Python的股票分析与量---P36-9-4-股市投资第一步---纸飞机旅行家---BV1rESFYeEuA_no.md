# 基于Python的股票分析与量化交易入门到实践 - P36：9.4 股市投资第一步-Python基本面量化选股_量化选股--规模类因子选股 📊

## 概述
在本节课中，我们将要学习如何通过规模类因子进行量化选股。规模类因子是反映公司整体规模情况的重要指标，主要体现市值大小对投资收益的影响。我们将详细介绍其核心概念、常用指标，并通过Python代码进行实战演示。

## 什么是规模类因子？
上一节我们介绍了如何通过利润因子进行选股，本节中我们来看看规模类因子。规模类因子反映公司的整体规模情况，主要体现市值大小对投资收益的影响。

规模类因子主要有以下四个常用指标：
*   总市值
*   流通市值
*   总股本
*   流通股本

接下来，我们将对这四个指标逐一进行分解。

## 核心指标详解

### 总市值
总市值是指在特定时间点，公司所有股票的总价值。

其计算公式为：
**总市值 = 总股本数 × 股价**

总市值的作用是表示个股的权重大小或大盘的规模大小。需要明确的是，总市值越大并不代表股票越好，总市值小也不代表股票不行。它仅用于表征个股权重或整体市场规模。总市值的变化情况可以反映整体盘面走势、股票的流通性以及活跃资本的情况。

在代码中，我们使用 `valuation` 类中的 `market_cap` 属性来获取总市值。

```python
# 示例：获取总市值
total_market_cap = get_fundamentals(query(valuation.market_cap))
```

### 流通市值
流通市值是指在特定时间点，公司可交易的流通股票的总价值。

其计算公式为：
**流通市值 = 流通股股数 × 股价**

并非所有股票都可以自由交易。例如，公司高管、董事持有的股票在上市后一段时间内可能被锁定，或因质押等原因无法交易，这些股票不会被计入流通市值。流通市值占总市值的比重越大，通常说明该股票的市场价格越能反映其真实价值。

在代码中，我们使用 `valuation` 类中的 `circulating_market_cap` 属性来获取流通市值。

```python
# 示例：获取流通市值
circulating_market_cap = get_fundamentals(query(valuation.circulating_market_cap))
```

### 总股本
总股本是指公司已发行的普通股股份总数，包括A股、B股和H股。
*   **A股**：普通投资者可以用人民币购买的内地上市股票。
*   **B股**：以美元或港币等外币计价、面向境外投资者发行的股票。
*   **H股**：在中国内地注册、在香港上市的外资股。

总股本的单位通常是“万股”。普通投资者通常交易的是A股，目前H股也逐渐对内地投资者开放，而B股则较少被普通投资者交易。

在代码中，我们使用 `valuation` 类中的 `capitalization` 属性来获取总股本。

```python
# 示例：获取总股本
total_capital = get_fundamentals(query(valuation.capitalization))
```

![](img/6426f458aa68ceb196d2548c66f4160a_1.png)

### 流通股本
流通股本特指公司已发行的、在境内市场（A股）上市流通的股份数量，单位同样是“万股”。

在代码中，我们使用 `valuation` 类中的 `circulating_cap` 属性来获取流通股本。

```python
# 示例：获取流通股本
circulating_capital = get_fundamentals(query(valuation.circulating_cap))
```

以上是关于规模类因子四个核心指标的介绍。接下来，我们进入代码实战环节。

## 代码实战环节
以下是使用规模类因子进行筛选的实战示例。

### 1. 筛选高总市值股票
首先，我们来筛选总市值大于一定规模的股票，并找出市值最大的前5名。

```python
# 查询总市值大于1万亿元的股票，并按总市值降序排列取前5
q = query(
    fundamentals.eod_derivative_indicator.stock_code,
    fundamentals.eod_derivative_indicator.average_price
).filter(
    valuation.market_cap > 100000000000  # 1万亿元，单位是元
).order_by(
    valuation.market_cap.desc()
).limit(5)

df = get_fundamentals(q)
print(df)
```
运行此代码，可以获取在指定日期（如回测起始日2019-01-01）总市值排名前五的股票列表。

### 2. 筛选高流通市值股票
接下来，我们筛选流通市值大于5000亿元的股票。

```python
# 查询流通市值大于5000亿元的股票，并按流通市值降序排列取前5
q = query(
    fundamentals.eod_derivative_indicator.stock_code,
    valuation.circulating_market_cap
).filter(
    valuation.circulating_market_cap > 500000000000  # 5000亿元
).order_by(
    valuation.circulating_market_cap.desc()
).limit(5)

df = get_fundamentals(q)
print(df)
```

### 3. 筛选高总股本股票
然后，我们筛选总股本大于1000亿股的股票。注意，数据中股本单位常为“万股”，因此1000亿股即10,000,000万股。

```python
# 查询总股本大于1000亿股的股票，并按总股本降序排列取前5
q = query(
    fundamentals.eod_derivative_indicator.stock_code,
    valuation.capitalization
).filter(
    valuation.capitalization > 10000000  # 1000亿股，即10000,000万股
).order_by(
    valuation.capitalization.desc()
).limit(5)

![](img/6426f458aa68ceb196d2548c66f4160a_3.png)

df = get_fundamentals(q)
print(df)
```

### 4. 综合筛选：高流通市值与高流通股本
最后，我们进行一个综合筛选，找出同时满足流通市值大于5000亿元且流通股本大于1000亿股的股票。

```python
# 综合筛选：流通市值>5000亿 且 流通股本>1000亿股
q = query(
    fundamentals.eod_derivative_indicator.stock_code,
    valuation.circulating_market_cap,
    valuation.circulating_cap
).filter(
    valuation.circulating_market_cap > 500000000000,
    valuation.circulating_cap > 10000000
).order_by(
    valuation.circulating_cap.desc()
).limit(5)

df = get_fundamentals(q)
print(df)
```
运行此代码，返回的结果可能不足5只，这表示同时满足这两个高门槛条件的公司较少。

## 本章小结 🎯
本节课中，我们一起学习了规模类因子在量化选股中的应用。

我们首先介绍了规模类因子，它反映了股票的规模大小，但不能简单认为规模越大股票越好。它主要用于分析投资规模对企业市值的影响。

规模类因子有四个常用指标：
1.  **总市值**：公司所有股票的总价值（总股本×股价）。
2.  **流通市值**：可交易股票的总价值（流通股×股价），其占比越高，股价越接近公司真实价值。
3.  **总股本**：公司已发行的全部股份，包括A股、B股和H股。
4.  **流通股本**：特指在A股市场可流通交易的股份数量。

最后，我们通过Python代码实战，演示了如何利用这些指标筛选符合条件的股票，包括筛选高总市值、高流通市值、高总股本以及进行综合条件筛选。

![](img/6426f458aa68ceb196d2548c66f4160a_5.png)

希望本节能帮助你理解并初步应用规模类因子进行选股分析。