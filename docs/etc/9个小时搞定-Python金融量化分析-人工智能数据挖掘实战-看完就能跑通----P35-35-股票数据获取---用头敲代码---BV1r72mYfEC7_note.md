# Python金融量化分析：P35：股票数据获取 📈

![](img/32b1318b2b22b276d97646974febbf1a_1.png)

在本节课中，我们将学习如何获取股票数据，这是构建量化交易策略的第一步。我们将使用特定的工具包来读取和处理股票数据，为后续的因子选股策略做准备。

![](img/32b1318b2b22b276d97646974febbf1a_3.png)

## 工具包导入

首先，我们需要导入一些必要的Python工具包。这些工具包将帮助我们进行数据处理和统计分析。

![](img/32b1318b2b22b276d97646974febbf1a_5.png)

![](img/32b1318b2b22b276d97646974febbf1a_7.png)

以下是需要导入的库：

![](img/32b1318b2b22b276d97646974febbf1a_9.png)

![](img/32b1318b2b22b276d97646974febbf1a_11.png)

![](img/32b1318b2b22b276d97646974febbf1a_13.png)

*   **numpy**：用于高效的数值计算。
*   **pandas**：用于数据处理和分析。
*   **statsmodels**：用于统计建模，特别是回归分析，这在后续因子中性化处理中会用到。

![](img/32b1318b2b22b276d97646974febbf1a_15.png)

```python
import numpy as np
import pandas as pd
from statsmodels import regression
```

![](img/32b1318b2b22b276d97646974febbf1a_17.png)

![](img/32b1318b2b22b276d97646974febbf1a_19.png)

## 初始化与定时器设置

![](img/32b1318b2b22b276d97646974febbf1a_21.png)

![](img/32b1318b2b22b276d97646974febbf1a_23.png)

上一节我们介绍了所需的工具包，本节中我们来看看如何初始化策略并设置调仓周期。

![](img/32b1318b2b22b276d97646974febbf1a_25.png)

![](img/32b1318b2b22b276d97646974febbf1a_27.png)

在量化策略中，我们需要定期（例如每月）检查并调整持仓的股票组合。这通过设置一个定时器来实现。

![](img/32b1318b2b22b276d97646974febbf1a_29.png)

![](img/32b1318b2b22b276d97646974febbf1a_31.png)

```python
def initialize(context):
    # 设置按月调仓的定时器
    schedule_function(rebalance, date_rules.month_start())
```

![](img/32b1318b2b22b276d97646974febbf1a_33.png)

![](img/32b1318b2b22b276d97646974febbf1a_35.png)

![](img/32b1318b2b22b276d97646974febbf1a_37.png)

这里，`rebalance` 是我们即将定义的调仓函数，`date_rules.month_start()` 指定了在每个月的第一个交易日执行该函数。

![](img/32b1318b2b22b276d97646974febbf1a_39.png)

## 获取股票数据

![](img/32b1318b2b22b276d97646974febbf1a_41.png)

![](img/32b1318b2b22b276d97646974febbf1a_43.png)

设置好调仓周期后，我们需要在每次调仓时获取股票数据。这是从整个市场股票池中筛选目标股票的基础。

![](img/32b1318b2b22b276d97646974febbf1a_45.png)

在 `rebalance` 函数中，第一步是获取当前市场上所有的股票信息。

![](img/32b1318b2b22b276d97646974febbf1a_47.png)

![](img/32b1318b2b22b276d97646974febbf1a_49.png)

![](img/32b1318b2b22b276d97646974febbf1a_51.png)

```python
def rebalance(context, data):
    # 获取所有股票代码
    all_stocks = get_all_securities(types=[‘stock‘])
```

![](img/32b1318b2b22b276d97646974febbf1a_53.png)

`get_all_securities` 函数用于获取指定类型（此处为股票）的所有证券信息。返回的结果通常包含股票代码等基本信息。

![](img/32b1318b2b22b276d97646974febbf1a_55.png)

![](img/32b1318b2b22b276d97646974febbf1a_57.png)

![](img/32b1318b2b22b276d97646974febbf1a_59.png)

## 股票数据筛选

![](img/32b1318b2b22b276d97646974febbf1a_61.png)

![](img/32b1318b2b22b276d97646974febbf1a_63.png)

获取到所有股票后，我们通常不会交易全部股票，而是需要根据一定的规则（如市值、流动性）进行初步筛选，形成一个备选股票池。

![](img/32b1318b2b22b276d97646974febbf1a_65.png)

![](img/32b1318b2b22b276d97646974febbf1a_67.png)

![](img/32b1318b2b22b276d97646974febbf1a_69.png)

因此，在获取全部股票ID之后，我们需要编写过滤逻辑，剔除不符合基本交易条件的股票（例如ST股、停牌股、上市时间过短的股票等），为后续的因子选股做准备。

![](img/32b1318b2b22b276d97646974febbf1a_71.png)

本节课中我们一起学习了量化策略中获取和初步处理股票数据的基本流程。我们导入了必要的工具包，设置了按月调仓的定时器，并学会了如何获取全市场股票数据以及理解后续进行数据筛选的必要性。这是构建任何量化交易策略的起点。