# 量化投资：P35：1-股票数据获取 📈

在本节课中，我们将学习如何获取股票数据，这是构建因子选股策略的第一步。我们将介绍如何初始化策略、导入必要的工具包，以及如何从数据源中获取并筛选股票列表。

![](img/28fe9666bb3c9d090b6eec43de344116_1.png)

---

![](img/28fe9666bb3c9d090b6eec43de344116_3.png)

## 策略初始化与工具包导入

上一节我们介绍了因子选股策略的基本流程，本节中我们来看看如何开始编写代码。首先，我们需要创建一个新的策略文件，并导入必要的工具包。

以下是需要导入的核心工具包：

*   **numpy**：用于高效的数值计算。
*   **pandas**：用于数据处理和分析。
*   **statsmodels**：用于统计分析，特别是回归分析，这在后续因子中性化操作中会用到。

![](img/28fe9666bb3c9d090b6eec43de344116_5.png)

![](img/28fe9666bb3c9d090b6eec43de344116_7.png)

```python
import numpy as np
import pandas as pd
from statsmodels import regression
```

---

## 设置策略运行周期

初始化策略后，我们需要定义策略的运行周期。因子选股策略通常需要定期调仓，即每隔一段时间重新筛选并调整持仓的股票。

![](img/28fe9666bb3c9d090b6eec43de344116_9.png)

在策略的构造函数中，我们使用定时器来设定调仓频率。考虑到交易成本和市场稳定性，按月调仓是一个常见且合理的选择。

![](img/28fe9666bb3c9d090b6eec43de344116_11.png)

![](img/28fe9666bb3c9d090b6eec43de344116_13.png)

```python
def initialize(context):
    # 设置按月调仓
    run_monthly(rebalance, monthday=1, time='open')
```

这里的 `rebalance` 函数是我们即将定义的核心函数，它将在每个月的第一个交易日开盘时执行，负责完成选股和调仓逻辑。

![](img/28fe9666bb3c9d090b6eec43de344116_15.png)

---

![](img/28fe9666bb3c9d090b6eec43de344116_17.png)

## 获取与筛选股票池

在 `rebalance` 函数中，第一步是获取一个初始的股票池。我们通常从一个广泛的市场股票列表开始，然后根据特定规则进行筛选，以缩小我们的选股范围。

![](img/28fe9666bb3c9d090b6eec43de344116_19.png)

以下是获取和初步处理股票池的步骤：

![](img/28fe9666bb3c9d090b6eec43de344116_21.png)

1.  **获取全市场股票**：使用API函数获取指定市场（如A股）的所有股票合约信息。
2.  **筛选股票**：对获取到的股票列表进行初步过滤，例如剔除ST股票、上市时间过短的股票等，以得到一个更干净、可投资的股票池。

![](img/28fe9666bb3c9d090b6eec43de344116_23.png)

```python
def rebalance(context):
    # 1. 获取全市场股票
    all_stocks = get_all_securities(types=[‘stock‘], date=None)

    # 2. 对股票进行初步筛选 (此处为示例，筛选逻辑需自定义)
    # 例如，过滤掉ST股票和上市不足60天的股票
    filtered_stocks = [stock for stock in all_stocks.index
                       if not is_st_stock(stock) and days_since_listed(stock) > 60]
```

![](img/28fe9666bb3c9d090b6eec43de344116_25.png)

通过以上步骤，我们就完成了策略的初始化、数据获取和初步的股票池构建工作，为后续的因子计算和选股策略实施打下了基础。

---

![](img/28fe9666bb3c9d090b6eec43de344116_27.png)

本节课中我们一起学习了量化策略的起步步骤：创建策略文件、导入工具包、设置策略运行周期以及获取并筛选初始股票池。在接下来的课程中，我们将在此基础上，进行因子的预处理和具体的选股逻辑实现。