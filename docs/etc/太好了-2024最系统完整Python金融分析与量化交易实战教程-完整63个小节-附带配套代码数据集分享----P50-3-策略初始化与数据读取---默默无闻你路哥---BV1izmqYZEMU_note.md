# Python金融分析与量化交易实战：P50：3-策略初始化与数据读取 📊

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_0.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_2.png)

在本节课中，我们将学习如何初始化一个量化交易策略，并读取用于因子打分选股所需的财务数据。我们将从指定股票池开始，逐步完成策略的框架搭建和数据获取。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_4.png)

## 策略初始化与股票池设定 🎯

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_6.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_8.png)

上一节我们介绍了策略的整体框架，本节中我们来看看如何具体初始化策略参数。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_10.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_12.png)

首先，我们需要在策略的 `context` 对象中指定选股的范围，即“股票池”。为了获得更好的演示效果，我们选择在沪深300指数的成分股中进行选股。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_14.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_16.png)

```python
# 在context中指定选股池为沪深300指数成分股
context.hs300 = ‘沪深300’
```

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_18.png)

接下来，我们需要定义一个变量来存储股票的排名结果，这个排名将在后续的因子打分过程中产生。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_20.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_22.png)

```python
# 定义一个变量，用于存储股票的排名情况
context.rank = None
```

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_24.png)

## 设置定时调仓函数 ⏰

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_26.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_28.png)

在量化策略中，通常需要定期（例如每月）执行调仓操作。我们需要设置一个定时器来触发这个调仓函数。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_30.png)

以下是设置每月调仓定时器的步骤：

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_32.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_34.png)

1.  使用 `run_monthly` 方法设置定时器。
2.  指定调仓时执行的函数，这里我们命名为 `rebalance`。
3.  参数 `1` 表示在每月第一个交易日执行。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_36.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_38.png)

```python
# 设置每月调仓的定时器
run_monthly(rebalance, 1)
```

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_40.png)

设置好定时器后，我们需要定义具体的 `rebalance` 调仓函数。这个函数的核心任务是获取评分排名靠前的股票。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_42.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_44.png)

```python
def rebalance(context):
    # 调仓函数的核心：获取评分后的股票列表
    pass
```

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_46.png)

## 构建数据获取函数 📥

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_48.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_50.png)

在 `rebalance` 函数中，我们需要一个专门的函数来获取股票数据并进行评分。我们将其命名为 `get_stocks`。

```python
def get_stocks(context):
    # 此函数负责获取数据并计算因子得分
    pass
```

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_52.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_54.png)

在 `get_stocks` 函数中，第一步是查询所需的财务指标数据。我们将使用六个关键指标作为例子，它们可以分为两类：“越高越好”的指标和“越低越好”的指标。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_56.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_58.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_60.png)

以下是构建查询语句的步骤：

1.  使用 `query` 函数指定要查询的指标。
2.  从 `Fundamentals` 模块中选取具体的财务指标。
3.  使用 `filter` 条件将查询范围限定在之前设定的沪深300股票池内。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_62.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_64.png)

首先，我们查询“越高越好”的指标，包括每股收益、净资产收益率和投入资本回报率。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_66.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_68.png)

```python
# 构建查询语句，获取“越高越好”的指标
query_up = query(
    fundamentals.eod_derivative_indicator.eps, # 每股收益 (EPS)，越高越好
    fundamentals.financial_indicator.roe,       # 净资产收益率 (ROE)，越高越好
    fundamentals.financial_indicator.roic       # 投入资本回报率 (ROIC)，越高越好
).filter(
    fundamentals.stockcode.in_(context.hs300)   # 过滤条件：仅限沪深300成分股
)
```

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_70.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_72.png)

然后，我们查询“越低越好”的指标，包括资产负债率、市净率（PB）和总市值。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_74.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_76.png)

```python
# 构建查询语句，获取“越低越好”的指标
query_down = query(
    fundamentals.financial_indicator.debt_to_asset, # 资产负债率，越低越好
    fundamentals.eod_derivative_indicator.pb_ratio, # 市净率 (PB)，越低越好
    fundamentals.eod_derivative_indicator.market_cap # 总市值，越低越好
).filter(
    fundamentals.stockcode.in_(context.hs300)        # 过滤条件：仅限沪深300成分股
)
```

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_78.png)

## 执行查询与数据转换 🔄

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_80.png)

构建好查询语句后，我们使用 `get_fundamentals` 函数来执行查询，获取实际数据。获取到的数据通常需要进行转置（`.T`），以便让股票代码作为行索引，指标作为列，方便后续处理。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_82.png)

以下是获取并处理两类指标数据的代码：

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_84.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_86.png)

```python
# 执行查询，获取“越高越好”的指标数据，并进行转置
df_up = get_fundamentals(query_up).T
# 执行查询，获取“越低越好”的指标数据，并进行转置
df_down = get_fundamentals(query_down).T
```

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_88.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_90.png)

执行上述代码后，我们将得到两个 `DataFrame`：
*   `df_up`：形状为 `(300, 3)`，包含300支股票的三个“利好”指标。
*   `df_down`：形状同样为 `(300, 3)`，包含300支股票的三个“利空”指标。

这些数据构成了我们进行多因子打分选股的基础。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_92.png)

## 总结 📝

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_94.png)

本节课中我们一起学习了量化策略初始化的关键步骤：
1.  **设定股票池**：明确了在沪深300成分股范围内进行选股。
2.  **搭建策略框架**：定义了存储排名的变量和定时的调仓函数 `rebalance`。
3.  **获取因子数据**：构建了 `get_stocks` 函数，通过 `query` 和 `get_fundamentals` 成功获取了用于打分的六类财务指标数据，并将其分为“越高越好”和“越低越好”两组。

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_96.png)

![](img/e4893a7bcfed5b1a48d16dc74e929c1b_98.png)

我们已经准备好了策略运行所需的基础数据和框架，下一步将学习如何对这些因子进行标准化处理和综合打分。