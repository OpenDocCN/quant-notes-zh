# 量化交易实战：P50：策略初始化与数据读取

![](img/b6c00e812ab7eb92154d69fc5966a26f_0.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_2.png)

在本节课中，我们将学习如何初始化一个量化交易策略，并读取用于选股的财务数据。我们将从定义股票池开始，逐步构建数据查询和获取的完整流程。

![](img/b6c00e812ab7eb92154d69fc5966a26f_4.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_6.png)

## 策略初始化

![](img/b6c00e812ab7eb92154d69fc5966a26f_8.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_10.png)

首先，我们需要创建一个新的策略文件，并开始实现基于因子打分的选股逻辑。我们将按照顺序一步步编写代码。

![](img/b6c00e812ab7eb92154d69fc5966a26f_12.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_14.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_16.png)

### 定义股票池与上下文变量

![](img/b6c00e812ab7eb92154d69fc5966a26f_18.png)

第一步是定义一个触发方法，并在 `context` 对象中指定我们的选股范围。为了获得更好的演示效果，我们选择在 **沪深300** 指数成分股中进行选股。

![](img/b6c00e812ab7eb92154d69fc5966a26f_20.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_22.png)

```python
# 在context中指定选股池为沪深300指数
context.hs300 = ‘沪深300’
```

![](img/b6c00e812ab7eb92154d69fc5966a26f_24.png)

接下来，我们需要在 `context` 中定义一个变量，用于存储后续计算出的股票排名结果，以便在调仓时使用。

![](img/b6c00e812ab7eb92154d69fc5966a26f_26.png)

```python
# 定义一个变量来存储股票排名
context.rank = None
```

![](img/b6c00e812ab7eb92154d69fc5966a26f_28.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_30.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_32.png)

### 设置定时调仓

![](img/b6c00e812ab7eb92154d69fc5966a26f_34.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_36.png)

然后，我们需要设置一个定时器，用于定期执行调仓操作。这里我们选择按月调仓。

![](img/b6c00e812ab7eb92154d69fc5966a26f_38.png)

```python
# 设置按月调仓的定时器
schedule_function(rebalance, date_rule=month_end(), time_rule=market_open())
```

![](img/b6c00e812ab7eb92154d69fc5966a26f_40.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_42.png)

在上面的代码中，`rebalance` 是我们即将定义的核心调仓函数。设置好定时器后，下一步就是实现这个函数。

![](img/b6c00e812ab7eb92154d69fc5966a26f_44.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_46.png)

## 数据读取与处理

![](img/b6c00e812ab7eb92154d69fc5966a26f_48.png)

在 `rebalance` 函数中，最核心的任务是根据财务指标对股票进行评分。为此，我们需要先获取数据。

![](img/b6c00e812ab7eb92154d69fc5966a26f_50.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_52.png)

### 创建数据获取函数

![](img/b6c00e812ab7eb92154d69fc5966a26f_54.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_56.png)

我们首先定义一个名为 `get_stocks` 的函数，专门用于获取并处理所需的财务数据。

![](img/b6c00e812ab7eb92154d69fc5966a26f_58.png)

```python
def get_stocks(context):
    # 此函数将执行数据查询和评分操作
```

![](img/b6c00e812ab7eb92154d69fc5966a26f_60.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_62.png)

### 构建数据查询

![](img/b6c00e812ab7eb92154d69fc5966a26f_64.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_66.png)

在 `get_stocks` 函数中，第一步是构建查询语句，以获取我们需要的财务指标。我们将使用六个指标作为例子，这些指标可以分为两类：**越高越好** 和 **越低越好**。

![](img/b6c00e812ab7eb92154d69fc5966a26f_68.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_70.png)

以下是构建查询语句的步骤：

![](img/b6c00e812ab7eb92154d69fc5966a26f_72.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_74.png)

1.  **查询“越高越好”的指标**：我们首先查询那些数值越大代表公司越优质的指标。
2.  **查询“越低越好”的指标**：接着查询那些数值越小越好的指标。
3.  **应用筛选器**：确保只查询我们股票池（沪深300）中的股票。

![](img/b6c00e812ab7eb92154d69fc5966a26f_76.png)

以下是具体的代码实现：

![](img/b6c00e812ab7eb92154d69fc5966a26f_78.png)

```python
def get_stocks(context):
    # 1. 构建查询：越高越好的指标
    query_up = query(
        fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)
        fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)
        fundamentals.financial_indicator.roa        # 总资产收益率 (ROA)
    ).filter(
        fundamentals.stockcode.in_(context.hs300) # 筛选在沪深300池中的股票
    )
    
    # 获取数据并转置，使股票代码成为行索引
    df_up = get_fundamentals(query_up).T
    
    # 2. 构建查询：越低越好的指标
    query_down = query(
        fundamentals.financial_indicator.debt_to_asset, # 资产负债率
        fundamentals.eod_derivative_indicator.pb_ratio, # 市净率 (PB)
        fundamentals.eod_derivative_indicator.market_cap # 总市值
    ).filter(
        fundamentals.stockcode.in_(context.hs300)
    )
    
    # 获取数据并转置
    df_down = get_fundamentals(query_down).T
    
    return df_up, df_down
```

![](img/b6c00e812ab7eb92154d69fc5966a26f_80.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_82.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_84.png)

**代码说明**：
*   `query()` 函数用于指定要查询的财务指标字段。
*   `.filter()` 方法用于限定查询范围，这里限定为 `context.hs300` 定义的股票池。
*   `get_fundamentals()` 函数执行查询并返回数据。
*   `.T` 是转置操作，它将数据框的行列互换，使得每一行代表一只股票，每一列代表一个财务指标，这更便于后续按股票进行评分计算。
*   函数最终返回两个 `DataFrame`：`df_up`（越高越好）和 `df_down`（越低越好）。

假设沪深300有300只股票，那么 `df_up` 和 `df_down` 的形态都将是 `(300, 3)`，即300行（股票）和3列（指标）。

![](img/b6c00e812ab7eb92154d69fc5966a26f_86.png)

## 本节总结

![](img/b6c00e812ab7eb92154d69fc5966a26f_88.png)

在本节课中，我们一起学习了量化交易策略的初始化与数据读取步骤：
1.  我们初始化了策略上下文，定义了**股票池（沪深300）** 和存储排名的变量。
2.  我们设置了**按月调仓**的定时器。
3.  我们创建了 `get_stocks` 函数，通过构建查询语句，分别获取了 **“越高越好”** 和 **“越低越好”** 两类财务指标数据，并确保数据来源于指定的股票池。

![](img/b6c00e812ab7eb92154d69fc5966a26f_90.png)

![](img/b6c00e812ab7eb92154d69fc5966a26f_92.png)

至此，我们已经成功获取了用于后续因子评分的基础数据。在下一节中，我们将学习如何对这些指标进行处理和标准化，并计算每只股票的综合得分。