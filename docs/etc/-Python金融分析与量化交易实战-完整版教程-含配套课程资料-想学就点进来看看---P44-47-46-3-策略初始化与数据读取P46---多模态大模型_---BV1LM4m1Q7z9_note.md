# Python金融分析与量化交易实战：P44：策略初始化与数据读取

![](img/cdb922580ac0e252abcacb011aaecc4c_0.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_2.png)

## 概述
在本节课中，我们将学习如何为一个量化交易策略进行初始化，并读取用于因子打分选股所需的财务数据。我们将从指定股票池开始，逐步完成数据查询与获取的代码编写。

![](img/cdb922580ac0e252abcacb011aaecc4c_4.png)

## 策略初始化

![](img/cdb922580ac0e252abcacb011aaecc4c_6.png)

首先，我们需要在新的策略文件中进行初始化设置。我们将指定股票池，并定义后续调仓所需的变量。

![](img/cdb922580ac0e252abcacb011aaecc4c_8.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_10.png)

### 指定股票池
为了获得更好的策略表现，我们将选择沪深300指数成分股作为我们的股票池。在 `context` 对象中，我们指定这个池子。

![](img/cdb922580ac0e252abcacb011aaecc4c_12.png)

```python
# 在 context 中指定股票池为沪深300
context.hs300 = '沪深300'
```

![](img/cdb922580ac0e252abcacb011aaecc4c_14.png)

### 定义调仓变量
接下来，我们需要定义一个变量来存储股票的排名情况，以便在调仓时选择排名靠前的股票。

```python
# 在 context 中定义变量，用于存储股票排名
context.rank = None
```

![](img/cdb922580ac0e252abcacb011aaecc4c_16.png)

## 设置定时调仓

![](img/cdb922580ac0e252abcacb011aaecc4c_18.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_20.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_22.png)

我们将设置一个按月调仓的定时器。这需要定义一个调仓函数，并将其与定时器绑定。

### 定义定时器
使用 `run_monthly` 函数来设置按月调仓。第一个参数是我们即将定义的调仓函数名，第二个参数指定调仓日。

![](img/cdb922580ac0e252abcacb011aaecc4c_24.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_26.png)

```python
# 设置按月调仓，每月第一个交易日执行 rebalance 函数
run_monthly(rebalance, 1)
```

### 创建调仓函数框架
现在，我们来创建调仓函数 `rebalance` 的框架。这个函数的核心任务是获取评分后的股票列表。

```python
def rebalance(context):
    # 调仓函数的主体
    # 核心任务是获取评分后的股票排名
    pass
```

![](img/cdb922580ac0e252abcacb011aaecc4c_28.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_30.png)

## 数据获取与处理

![](img/cdb922580ac0e252abcacb011aaecc4c_32.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_34.png)

上一节我们设置了调仓框架，本节中我们来看看如何获取用于打分的财务数据。我们将编写一个专门的函数 `get_stocks` 来完成数据查询和初步处理。

![](img/cdb922580ac0e252abcacb011aaecc4c_36.png)

### 定义数据获取函数
我们创建一个名为 `get_stocks` 的函数，它负责查询财务数据并返回处理好的 `DataFrame`。

![](img/cdb922580ac0e252abcacb011aaecc4c_38.png)

```python
def get_stocks(context):
    # 此函数用于获取并处理财务数据
    pass
```

![](img/cdb922580ac0e252abcacb011aaecc4c_40.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_42.png)

### 构建查询语句 (Query)
在 `get_stocks` 函数内部，第一步是构建查询语句。我们将查询多个财务指标。以下是需要查询的指标列表，分为“越高越好”和“越低越好”两类。

![](img/cdb922580ac0e252abcacb011aaecc4c_44.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_46.png)

**越高越好的指标：**
1.  **每股收益 (EPS)**：`fundamentals.eod_derivative_indicator.eps`
2.  **净资产收益率 (ROE)**：`fundamentals.financial_indicator.roe`
3.  **投入资本回报率 (ROIC)**：`fundamentals.financial_indicator.roic`

![](img/cdb922580ac0e252abcacb011aaecc4c_48.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_50.png)

**越低越好的指标：**
1.  **资产负债率**：`fundamentals.financial_indicator.debt_to_asset_ratio`
2.  **市净率 (PB)**：`fundamentals.eod_derivative_indicator.pb_ratio`
3.  **市值**：`fundamentals.eod_derivative_indicator.market_cap`

对应的查询代码如下：

![](img/cdb922580ac0e252abcacb011aaecc4c_52.png)

```python
# 构建查询语句，获取“越高越好”的指标
query_up = query(
    fundamentals.eod_derivative_indicator.eps, # 每股收益
    fundamentals.financial_indicator.roe,      # 净资产收益率
    fundamentals.financial_indicator.roic      # 投入资本回报率
).filter(
    fundamentals.stockcode.in_(context.hs300) # 过滤条件：股票在沪深300池中
)

![](img/cdb922580ac0e252abcacb011aaecc4c_54.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_56.png)

# 构建查询语句，获取“越低越好”的指标
query_down = query(
    fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率
    fundamentals.eod_derivative_indicator.pb_ratio,       # 市净率
    fundamentals.eod_derivative_indicator.market_cap      # 市值
).filter(
    fundamentals.stockcode.in_(context.hs300) # 过滤条件：股票在沪深300池中
)
```

![](img/cdb922580ac0e252abcacb011aaecc4c_58.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_60.png)

### 执行查询并处理数据
使用 `get_fundamentals` 函数执行查询。获取到的数据需要进行转置 `.T`，以便让股票代码成为行索引，指标成为列，方便后续按股票进行计算。

```python
# 执行查询，获取“越高越好”指标的数据，并进行转置
df_up = get_fundamentals(query_up).T

![](img/cdb922580ac0e252abcacb011aaecc4c_62.png)

# 执行查询，获取“越低越好”指标的数据，并进行转置
df_down = get_fundamentals(query_down).T
```

![](img/cdb922580ac0e252abcacb011aaecc4c_64.png)

![](img/cdb922580ac0e252abcacb011aaecc4c_66.png)

现在，我们得到了两个 `DataFrame`：`df_up` 包含越高越好的指标，`df_down` 包含越低越好的指标。每个 `DataFrame` 的形状大约是 `(300, 3)`，对应300支股票和3个指标。

![](img/cdb922580ac0e252abcacb011aaecc4c_68.png)

## 总结
本节课中我们一起学习了量化策略初始化的关键步骤。我们首先在 `context` 中指定了沪深300作为股票池，并定义了存储排名的变量。然后，我们设置了按月调仓的定时器，并创建了 `rebalance` 函数的框架。最后，我们深入编写了 `get_stocks` 函数，详细讲解了如何构建查询语句来获取每股收益、净资产收益率等关键财务指标，并正确执行查询和转置数据，为下一节课的因子评分计算做好了准备。