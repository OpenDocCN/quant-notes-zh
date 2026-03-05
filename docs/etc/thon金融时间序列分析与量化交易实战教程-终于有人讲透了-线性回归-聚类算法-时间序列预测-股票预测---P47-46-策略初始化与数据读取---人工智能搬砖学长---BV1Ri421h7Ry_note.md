# Python金融时间序列分析与量化交易实战教程：P47：46.策略初始化与数据读取 📊

![](img/5145c863077ed5e9d2016b50a8a4dd30_0.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_2.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_4.png)

在本节课中，我们将学习如何为一个量化交易策略进行初始化，并读取所需的股票基本面数据。我们将构建一个因子打分选股策略的框架，从指定股票池到获取关键财务指标，为后续的评分与选股打下基础。

![](img/5145c863077ed5e9d2016b50a8a4dd30_6.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_8.png)

## 策略初始化 🚀

![](img/5145c863077ed5e9d2016b50a8a4dd30_10.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_12.png)

上一节我们介绍了策略的整体框架，本节中我们来看看如何具体实现初始化步骤。首先，我们需要在策略的 `context` 对象中设置一些基础参数。

![](img/5145c863077ed5e9d2016b50a8a4dd30_14.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_16.png)

以下是初始化步骤：

![](img/5145c863077ed5e9d2016b50a8a4dd30_18.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_20.png)

1.  **指定股票池**：为了获得更好的策略表现，我们选择在沪深300指数的成分股中进行选股。这通过设置 `context.hs300` 变量来实现。
    ```python
    context.hs300 = ‘沪深300’
    ```

![](img/5145c863077ed5e9d2016b50a8a4dd30_22.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_24.png)

2.  **预留排名变量**：我们预先定义一个变量 `context.ranks`，用于存储后续计算出的股票排名结果。
    ```python
    context.ranks = None
    ```

![](img/5145c863077ed5e9d2016b50a8a4dd30_26.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_28.png)

3.  **设置调仓定时器**：我们设定策略按月进行调仓。使用 `run_monthly` 函数，并指定调仓执行的函数名 `rebalance` 和日期（每月第一个交易日）。
    ```python
    run_monthly(rebalance, 1)
    ```

![](img/5145c863077ed5e9d2016b50a8a4dd30_30.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_32.png)

## 定义调仓函数与数据获取 📈

![](img/5145c863077ed5e9d2016b50a8a4dd30_34.png)

在设置了定时器后，我们需要定义具体的调仓函数 `rebalance`。这个函数的核心任务是获取股票评分并执行调仓。首先，我们来实现其中最关键的一步：获取股票数据。

![](img/5145c863077ed5e9d2016b50a8a4dd30_36.png)

以下是 `rebalance` 函数及其数据获取子函数 `get_factors` 的框架：

![](img/5145c863077ed5e9d2016b50a8a4dd30_38.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_40.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_42.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_44.png)

```python
def rebalance(context):
    # 调用函数获取股票因子数据及排名
    context.ranks = get_factors(context)

![](img/5145c863077ed5e9d2016b50a8a4dd30_46.png)

def get_factors(context):
    # 此函数用于获取并处理股票因子数据
    pass
```

![](img/5145c863077ed5e9d2016b50a8a4dd30_48.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_50.png)

现在，我们来详细实现 `get_factors` 函数。该函数需要从沪深300池子中查询多组基本面指标。

![](img/5145c863077ed5e9d2016b50a8a4dd30_52.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_54.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_56.png)

## 查询基本面数据 🔍

![](img/5145c863077ed5e9d2016b50a8a4dd30_58.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_60.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_62.png)

我们计划使用六个财务指标对股票进行打分，这些指标分为两类：“越高越好”型和“越低越好”型。

![](img/5145c863077ed5e9d2016b50a8a4dd30_64.png)

以下是数据查询的具体步骤：

![](img/5145c863077ed5e9d2016b50a8a4dd30_66.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_68.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_70.png)

1.  **构建查询语句 (Query)**：我们需要分别查询两类指标。
    *   **“越高越好”型指标**：包括每股收益 (EPS)、净资产收益率 (ROE)、投入资本回报率 (ROIC)。
        ```python
        q_up = query(
            fundamentals.eod_derivative_indicator.eps, # 每股收益，越高越好
            fundamentals.financial_indicator.roe, # 净资产收益率，越高越好
            fundamentals.financial_indicator.roic # 投入资本回报率，越高越好
        )
        ```
    *   **“越低越好”型指标**：包括资产负债率、市净率 (PB)、总市值。
        ```python
        q_down = query(
            fundamentals.financial_indicator.debt_to_asset_ratio, # 资产负债率，越低越好
            fundamentals.eod_derivative_indicator.pb_ratio, # 市净率，越低越好
            fundamentals.eod_derivative_indicator.market_cap # 总市值，越低越好
        )
        ```

![](img/5145c863077ed5e9d2016b50a8a4dd30_72.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_74.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_76.png)

2.  **应用股票池过滤**：在查询中，我们需要限定股票范围在之前设定的沪深300成分股内。
    ```python
    # 在 q_up 和 q_down 查询对象后添加 filter
    q_up = q_up.filter(fundamentals.stockcode.in_(context.hs300))
    q_down = q_down.filter(fundamentals.stockcode.in_(context.hs300))
    ```

![](img/5145c863077ed5e9d2016b50a8a4dd30_78.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_80.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_82.png)

3.  **执行查询并转换数据**：使用 `get_fundamentals` 函数执行查询。获取到的数据通常需要进行转置（`.T`），以便行是股票、列是指标，方便后续处理。
    ```python
    df_up = get_fundamentals(q_up).T # “越高越好”型指标数据框
    df_down = get_fundamentals(q_down).T # “越低越好”型指标数据框
    ```

![](img/5145c863077ed5e9d2016b50a8a4dd30_84.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_86.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_88.png)

## 数据形态确认 ✅

![](img/5145c863077ed5e9d2016b50a8a4dd30_90.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_92.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_94.png)

完成查询后，我们得到了两个 `DataFrame`：`df_up` 和 `df_down`。它们的形状预期为 `(300, 3)`，即300支股票，每支股票对应3个财务指标。这些数据是后续进行因子标准化、加权和打分的基石。

![](img/5145c863077ed5e9d2016b50a8a4dd30_96.png)

![](img/5145c863077ed5e9d2016b50a8a4dd30_98.png)

本节课中我们一起学习了量化策略的初始化流程，包括设置股票池、调仓频率，并实现了从数据库获取多维度基本面数据的功能。我们已经成功将沪深300成分股的六项关键财务指标提取出来，并分成了“正向”和“负向”两类，为下一节课构建打分模型做好了充分的数据准备。