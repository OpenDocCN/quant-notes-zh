# Python金融分析与量化交易实战教程：P33：【因子选股策略实战】01-1-股票数据获取 📈

在本节课中，我们将学习如何获取股票数据，这是构建因子选股策略的第一步。我们将从初始化环境开始，逐步完成数据读取和初步筛选，为后续的因子预处理和策略构建打下基础。

![](img/513f111215b7a77c7b9d769f0762cdea_1.png)

## 初始化环境与导入工具包 🛠️

![](img/513f111215b7a77c7b9d769f0762cdea_3.png)

首先，我们需要创建一个新的Python文件，并导入必要的工具包。这些工具包将帮助我们进行数据处理、数学运算和统计分析。

以下是构建策略所需的核心工具包：

![](img/513f111215b7a77c7b9d769f0762cdea_5.png)

![](img/513f111215b7a77c7b9d769f0762cdea_7.png)

*   **numpy**：用于高效的数值计算和数组操作。
*   **pandas**：用于数据处理和分析，是处理表格数据的利器。
*   **statsmodels**：用于统计建模和计量经济学分析，我们将用它来求解回归方程，以完成因子的中性化操作。

```python
import numpy as np
import pandas as pd
from statsmodels import regression
```

![](img/513f111215b7a77c7b9d769f0762cdea_9.png)

![](img/513f111215b7a77c7b9d769f0762cdea_11.png)

## 设计策略初始化模块 ⚙️

![](img/513f111215b7a77c7b9d769f0762cdea_13.png)

上一节我们导入了必要的工具包，本节中我们来看看如何设计策略的初始化模块。这个模块的核心是设置策略的运行周期，例如按月调仓。

![](img/513f111215b7a77c7b9d769f0762cdea_15.png)

![](img/513f111215b7a77c7b9d769f0762cdea_17.png)

我们需要在初始化函数中设置一个定时器，指定策略按月度执行调仓操作。调仓意味着在每个月的特定时间点，我们会根据最新的因子数据，重新评估并调整投资组合中的股票。

![](img/513f111215b7a77c7b9d769f0762cdea_19.png)

```python
def initialize(context):
    # 设置按月调仓
    run_monthly(rebalance, monthday=1)
```

![](img/513f111215b7a77c7b9d769f0762cdea_21.png)

## 获取与筛选股票数据 📊

![](img/513f111215b7a77c7b9d769f0762cdea_23.png)

设置好调仓周期后，下一步是获取股票数据。我们将获取市场上所有的股票信息，但通常不会全部使用，需要进行初步筛选以构建一个可管理的股票池。

![](img/513f111215b7a77c7b9d769f0762cdea_25.png)

![](img/513f111215b7a77c7b9d769f0762cdea_27.png)

以下是获取和初步处理股票数据的步骤：

![](img/513f111215b7a77c7b9d769f0762cdea_29.png)

1.  **获取全市场股票**：使用API函数获取指定市场（如A股）的所有股票代码和基本信息。
2.  **构建股票池**：对获取到的所有股票进行筛选，剔除不符合基本条件的股票（例如ST股、上市时间过短的股票等），形成初始的股票池。

![](img/513f111215b7a77c7b9d769f0762cdea_31.png)

![](img/513f111215b7a77c7b9d769f0762cdea_33.png)

```python
def rebalance(context):
    # 1. 获取全市场股票
    all_stocks = get_all_securities(types=[‘stock‘], date=None)

    # 2. 对股票进行初步筛选，构建股票池
    # 例如，过滤掉ST股票、上市不足60天的股票等
    stock_list = [stock for stock in all_stocks.index if not is_st_stock(stock) and not is_new_stock(stock)]
```

![](img/513f111215b7a77c7b9d769f0762cdea_35.png)

本节课中我们一起学习了量化交易策略中获取股票数据的关键步骤。我们从导入必要的Python库开始，设计了策略的初始化模块以设定按月调仓的周期，最后通过API获取全市场股票并进行了初步筛选，为后续的因子计算和选股策略做好了数据准备。