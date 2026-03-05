# Python金融量化实战课程：P46：策略初始化与数据读取 📊

![](img/d2ce0bd4f033df794f19f2c271ad8afb_1.png)

在本节课中，我们将学习如何初始化一个量化交易策略并读取所需数据。我们将从指定股票池开始，设置调仓定时器，并编写核心函数来获取和准备用于因子打分的财务数据。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_3.png)

## 策略初始化

![](img/d2ce0bd4f033df794f19f2c271ad8afb_5.png)

![](img/d2ce0bd4f033df794f19f2c271ad8afb_7.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体初始化一个策略。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_9.png)

![](img/d2ce0bd4f033df794f19f2c271ad8afb_11.png)

首先，我们需要在策略的 `context` 对象中指定选股的范围。为了获得更理想的效果，我们选择在沪深300指数的成分股中进行选股。

```python
# 在context中指定选股池为沪深300指数
context.hs300 = ‘沪深300’
```

接下来，我们需要定义一个变量来存储股票的排名结果，这个结果将在后续的评分函数中生成。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_13.png)

```python
# 定义一个变量来存储股票排名
context.ranks = None
```

![](img/d2ce0bd4f033df794f19f2c271ad8afb_15.png)

![](img/d2ce0bd4f033df794f19f2c271ad8afb_17.png)

然后，我们设置一个定时器来执行周期性的调仓操作。这里我们选择按月调仓。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_19.png)

```python
# 设置按月调仓的定时器
schedule_function(rebalance, date_rule=month_end(), time_rule=market_open())
```

![](img/d2ce0bd4f033df794f19f2c271ad8afb_21.png)

![](img/d2ce0bd4f033df794f19f2c271ad8afb_23.png)

## 定义调仓函数

![](img/d2ce0bd4f033df794f19f2c271ad8afb_25.png)

既然我们设置了调仓函数 `rebalance`，下面就来定义它。这个函数的核心任务是获取评分后的股票列表。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_27.png)

![](img/d2ce0bd4f033df794f19f2c271ad8afb_29.png)

```python
def rebalance(context, data):
    # 调用函数获取评分后的股票列表
    context.ranks = get_stocks(context)
```

![](img/d2ce0bd4f033df794f19f2c271ad8afb_31.png)

## 数据获取与处理函数

![](img/d2ce0bd4f033df794f19f2c271ad8afb_33.png)

在 `rebalance` 函数中，我们调用了 `get_stocks` 函数。现在，我们来详细实现这个函数，它的核心是查询并处理财务数据以进行因子评分。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_35.png)

以下是 `get_stocks` 函数中需要完成的步骤：

![](img/d2ce0bd4f033df794f19f2c271ad8afb_37.png)

1.  **构建查询语句**：我们需要查询多个财务指标。
2.  **执行数据查询**：从数据接口获取原始数据。
3.  **数据格式转换**：将获取的数据转换为便于处理的格式（如DataFrame）。
4.  **区分指标类型**：根据因子打分逻辑，将指标分为“越高越好”和“越低越好”两类。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_39.png)

首先，我们构建查询语句。我们以六个常见指标为例，前三个是“越高越好”的指标。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_41.png)

![](img/d2ce0bd4f033df794f19f2c271ad8afb_43.png)

```python
def get_stocks(context):
    # 步骤1：构建查询语句，获取“越高越好”的指标
    query_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)
        fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)
        fundamentals.financial_indicator.roa        # 总资产收益率 (ROA)
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 过滤条件：仅限沪深300成分股
    )
```

![](img/d2ce0bd4f033df794f19f2c271ad8afb_45.png)

同样，我们构建另一个查询来获取“越低越好”的指标。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_47.png)

```python
    # 构建查询语句，获取“越低越好”的指标
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率 (PB)
        fundamentals.eod_derivative_indicator.market_cap # 总市值
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 过滤条件：仅限沪深300成分股
    )
```

![](img/d2ce0bd4f033df794f19f2c271ad8afb_49.png)

![](img/d2ce0bd4f033df794f19f2c271ad8afb_51.png)

接着，我们执行查询并将返回的数据转换为以股票为索引、指标为列的DataFrame格式，方便后续处理。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_53.png)

![](img/d2ce0bd4f033df794f19f2c271ad8afb_55.png)

```python
    # 步骤2 & 3：执行查询并转换数据格式
    fundamental_up = get_fundamentals(query_up).T # “越高越好”指标数据
    fundamental_down = get_fundamentals(query_down).T # “越低越好”指标数据
```

至此，我们成功获取了两组数据：
*   `fundamental_up`：包含每股收益、净资产收益率、总资产收益率。数值**越高**，通常认为股票越好。
*   `fundamental_down`：包含资产负债率、市净率、总市值。数值**越低**，通常认为股票越好。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_57.png)

这些数据将作为下一阶段因子打分的基础。

![](img/d2ce0bd4f033df794f19f2c271ad8afb_59.png)

## 总结

![](img/d2ce0bd4f033df794f19f2c271ad8afb_61.png)

![](img/d2ce0bd4f033df794f19f2c271ad8afb_63.png)

本节课中我们一起学习了量化策略初始化的关键步骤：
1.  我们在 `context` 中定义了选股池（沪深300）和存储排名的变量。
2.  我们设置了按月调仓的定时器，并定义了 `rebalance` 函数框架。
3.  我们重点实现了 `get_stocks` 函数，学会了如何构建查询语句来获取多类财务指标数据，并将数据转换为适合分析的 `DataFrame` 格式，为后续的因子评分做好了数据准备。