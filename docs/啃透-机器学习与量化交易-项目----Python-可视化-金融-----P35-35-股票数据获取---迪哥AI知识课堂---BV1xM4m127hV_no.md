# 机器学习与量化交易：P35：股票数据获取 📈

在本节课中，我们将学习如何获取股票数据，这是构建因子选股策略的第一步。我们将从初始化环境开始，逐步完成数据读取和初步筛选，为后续的因子计算和策略回测打下基础。

![](img/d1e300bf1f02ee255310b1db7069b5f3_1.png)

## 工具包导入 🧰

![](img/d1e300bf1f02ee255310b1db7069b5f3_3.png)

首先，我们需要导入后续分析中将要用到的Python工具包。这些包提供了数据处理、数学运算和统计分析的核心功能。

以下是需要导入的库：

![](img/d1e300bf1f02ee255310b1db7069b5f3_5.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_7.png)

*   **numpy**：用于高效的数值计算和数组操作。
*   **pandas**：用于数据处理和分析，是处理表格数据的核心工具。
*   **statsmodels**：一个用于统计建模和计量经济学的库，我们将使用其回归分析模块来执行因子中性化操作。

![](img/d1e300bf1f02ee255310b1db7069b5f3_9.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_11.png)

```python
import numpy as np
import pandas as pd
from statsmodels import regression
```

![](img/d1e300bf1f02ee255310b1db7069b5f3_13.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_15.png)

## 初始化与定时器设置 ⏰

![](img/d1e300bf1f02ee255310b1db7069b5f3_17.png)

上一节我们导入了必要的工具包，本节中我们来看看如何初始化策略框架并设置调仓周期。

![](img/d1e300bf1f02ee255310b1db7069b5f3_19.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_21.png)

在量化策略中，我们需要定期（例如每月）检查并调整持仓组合，这个过程称为“调仓”。为了实现这一点，我们首先在策略的初始化模块中定义一个定时器。

![](img/d1e300bf1f02ee255310b1db7069b5f3_23.png)

```python
def initialize(context):
    # 设置按月调仓的定时器
    # run_monthly 函数指定在每月某个交易日执行 rebalance 函数
    run_monthly(rebalance, monthday=1)
```

![](img/d1e300bf1f02ee255310b1db7069b5f3_25.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_27.png)

这里，`run_monthly` 是一个调度函数，它确保在每个月的第一个交易日（`monthday=1`）自动调用我们即将定义的 `rebalance` 函数来执行调仓逻辑。

![](img/d1e300bf1f02ee255310b1db7069b5f3_29.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_31.png)

## 数据读取与股票池构建 📊

![](img/d1e300bf1f02ee255310b1db7069b5f3_33.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_35.png)

设置好调仓周期后，下一步是获取可交易的股票数据，并构建我们的初始股票池。

![](img/d1e300bf1f02ee255310b1db7069b5f3_37.png)

在 `rebalance` 函数中，我们首先要获取市场上所有的股票信息。这可以通过平台提供的API函数来完成。

![](img/d1e300bf1f02ee255310b1db7069b5f3_39.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_41.png)

```python
def rebalance(context):
    # 获取所有A股股票列表
    all_stocks = get_all_securities(types=[‘stock‘])
```

![](img/d1e300bf1f02ee255310b1db7069b5f3_43.png)

`get_all_securities` 函数用于获取指定市场（此处为A股股票 `‘stock‘`）的所有证券基本信息。执行后，`all_stocks` 变量将包含一个股票代码和基本信息的列表。

![](img/d1e300bf1f02ee255310b1db7069b5f3_45.png)

## 股票池初步筛选 🔍

![](img/d1e300bf1f02ee255310b1db7069b5f3_47.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_49.png)

获取全部股票列表只是第一步。在实际策略中，我们通常不会交易所有股票，而是需要根据一定规则（如剔除ST股、次新股、停牌股等）进行初步筛选，形成一个更干净、可操作的“股票池”。

![](img/d1e300bf1f02ee255310b1db7069b5f3_51.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_53.png)

以下是常见的初步筛选步骤：

![](img/d1e300bf1f02ee255310b1db7069b5f3_55.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_57.png)

*   **剔除ST/*ST股票**：这些是存在退市风险或其他异常状况的股票。
*   **剔除上市时间过短的股票**：例如上市不足60天，以避免次新股波动的影响。
*   **剔除当日停牌的股票**：确保在调仓日可以正常买卖。

![](img/d1e300bf1f02ee255310b1db7069b5f3_59.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_61.png)

```python
    # 示例：进行初步筛选（此处为逻辑示意，具体函数名可能因平台而异）
    # 1. 获取非ST股票
    normal_stocks = filter_st_stocks(all_stocks)
    # 2. 获取上市超过60天的股票
    mature_stocks = filter_new_stocks(normal_stocks, 60)
    # 3. 获取当前未停牌的股票
    tradable_stocks = filter_paused_stocks(mature_stocks)
```

![](img/d1e300bf1f02ee255310b1db7069b5f3_63.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_65.png)

经过这些步骤，我们得到了一个经过初步清洗的股票池 `tradable_stocks`。这个池子里的股票将成为我们后续因子计算和选股策略的基础。

![](img/d1e300bf1f02ee255310b1db7069b5f3_67.png)

![](img/d1e300bf1f02ee255310b1db7069b5f3_69.png)

---

![](img/d1e300bf1f02ee255310b1db7069b5f3_71.png)

本节课中我们一起学习了量化策略初始化的关键步骤：导入工具包、设置调仓定时器、读取全市场股票数据以及进行股票池的初步筛选。这些工作是构建任何因子选股模型前必须完成的准备工作，确保了后续分析的数据质量和策略的可执行性。在下一节中，我们将基于这个股票池，开始进行因子的计算与预处理。