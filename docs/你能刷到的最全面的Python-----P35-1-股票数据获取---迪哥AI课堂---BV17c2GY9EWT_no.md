# Python金融分析量化交易：P35：1-股票数据获取 📈

在本节课中，我们将学习如何获取股票数据，这是构建量化交易策略的第一步。我们将从初始化环境开始，逐步讲解如何获取全市场股票列表，并对其进行初步筛选，为后续的因子计算和策略构建打下基础。

![](img/5b122207ab1d978a9f1faf926188dfc5_1.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_3.png)

## 导入必要的工具包 🧰

首先，我们需要导入一些在数据处理和分析中必不可少的Python库。以下是我们要用到的工具包：

![](img/5b122207ab1d978a9f1faf926188dfc5_5.png)

```python
import numpy as np
import pandas as pd
from statsmodels import regression
```

![](img/5b122207ab1d978a9f1faf926188dfc5_7.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_9.png)

*   **`numpy`**：用于高效的数值计算。
*   **`pandas`**：用于数据处理和分析，是处理表格数据的核心工具。
*   **`statsmodels`**：用于统计分析，我们主要使用其回归模块，为后续的因子中性化操作做准备。

![](img/5b122207ab1d978a9f1faf926188dfc5_11.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_13.png)

## 初始化策略与设置定时器 ⏰

![](img/5b122207ab1d978a9f1faf926188dfc5_15.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_17.png)

上一节我们介绍了所需的工具包，本节中我们来看看如何初始化我们的策略框架。在量化策略中，我们需要设定一个调仓周期，例如每月或每周执行一次选股和调仓操作。

![](img/5b122207ab1d978a9f1faf926188dfc5_19.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_21.png)

```python
def initialize(context):
    # 设置按月调仓的定时器
    run_monthly(rebalance, monthday=1, time='open')
```

![](img/5b122207ab1d978a9f1faf926188dfc5_23.png)

*   **`initialize(context)`**：这是策略的初始化函数。
*   **`run_monthly(func, monthday, time)`**：这是一个定时器函数，它会在每个月的指定日期（`monthday`）和市场开盘时间（`time`）自动调用指定的函数（`func`）。
*   **`rebalance`**：这是我们接下来要定义的、执行核心选股和调仓逻辑的函数。

![](img/5b122207ab1d978a9f1faf926188dfc5_25.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_27.png)

## 定义调仓函数与获取股票池 📊

![](img/5b122207ab1d978a9f1faf926188dfc5_29.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_31.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_33.png)

现在，我们来定义核心的`rebalance`函数。这个函数将在每个调仓日被自动调用，其首要任务就是获取当前市场上的所有股票，形成一个“股票池”。

![](img/5b122207ab1d978a9f1faf926188dfc5_35.png)

以下是获取股票池的步骤：

![](img/5b122207ab1d978a9f1faf926188dfc5_37.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_39.png)

1.  使用API函数`get_all_securities`获取指定类型的所有证券信息。
2.  通过参数`type=‘CS’`来指定我们只关心普通股票。
3.  该函数会返回一个包含所有股票代码（ID）和其他信息的`DataFrame`。

![](img/5b122207ab1d978a9f1faf926188dfc5_41.png)

```python
def rebalance(context):
    # 获取全市场股票列表
    all_stocks = get_all_securities(types=[‘CS’])
    print(“获取到的股票数量：”, len(all_stocks))
```

![](img/5b122207ab1d978a9f1faf926188dfc5_43.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_45.png)

*   **`get_all_securities(types=[‘CS’])`**：这个API调用会返回一个`pandas DataFrame`，其中包含了所有A股普通股票的基本信息列表。
*   **`types=[‘CS’]`**：参数`types`用于指定证券类型，`‘CS’`代表普通股。

![](img/5b122207ab1d978a9f1faf926188dfc5_47.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_49.png)

## 对股票池进行初步筛选 🔍

![](img/5b122207ab1d978a9f1faf926188dfc5_51.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_53.png)

获取到全市场股票列表后，我们通常不会直接使用所有股票。在实际策略中，我们需要根据一些基本规则（如剔除ST股、次新股、停牌股等）对股票池进行初步筛选，以得到一个更干净、可交易的股票集合。

![](img/5b122207ab1d978a9f1faf926188dfc5_55.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_57.png)

```python
def rebalance(context):
    # 获取全市场股票列表
    all_stocks = get_all_securities(types=[‘CS’])
    
    # 对股票ID列表进行初步筛选（此处为示例，筛选逻辑需根据实际情况编写）
    # 例如：过滤掉上市时间过短的股票
    filtered_stocks = [stock for stock in all_stocks.index if some_filter_condition(stock)]
    
    print(“筛选后的股票数量：”, len(filtered_stocks))
```

![](img/5b122207ab1d978a9f1faf926188dfc5_59.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_61.png)

![](img/5b122207ab1d978a9f1faf926188dfc5_63.png)

*   在实际操作中，`some_filter_condition`需要替换成具体的筛选条件函数，例如检查股票是否被ST、上市天数是否大于一定阈值等。
*   经过筛选的`filtered_stocks`列表，将成为我们后续进行因子计算和选股策略的基础股票池。

![](img/5b122207ab1d978a9f1faf926188dfc5_65.png)

本节课中我们一起学习了量化策略初始化的基本步骤：导入工具包、设置定时调仓函数、获取全市场股票数据以及对其进行初步筛选。这是构建任何量化交易模型的起点，一个干净、准确的股票池是后续所有分析和决策的基石。在接下来的课程中，我们将在这个股票池的基础上，进行因子的计算、处理和选股策略的构建。