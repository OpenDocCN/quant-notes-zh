# Python金融量化实战：P31：1-股票数据获取 📈

在本节课中，我们将学习如何获取股票数据，这是构建量化交易策略的第一步。我们将从初始化环境开始，逐步介绍如何获取全市场股票列表，并理解后续进行数据筛选和策略构建的基础。

![](img/1f7e1ef39a493137d22575eafabb2ad7_1.png)

## 导入必要的工具包

![](img/1f7e1ef39a493137d22575eafabb2ad7_3.png)

在开始编写代码之前，我们需要导入一些必要的Python库。这些库将帮助我们进行数据处理、数学运算和统计分析。

以下是本教程将使用到的核心工具包：

*   **numpy**：用于高效的数值计算。
*   **pandas**：用于数据处理和分析，是金融数据分析的核心工具。
*   **statsmodels**：一个用于统计建模和计量经济学的库。我们将使用它来进行回归分析，这在后续的因子中性化处理中至关重要。

![](img/1f7e1ef39a493137d22575eafabb2ad7_5.png)

```python
import numpy as np
import pandas as pd
from statsmodels import regression
```

![](img/1f7e1ef39a493137d22575eafabb2ad7_7.png)

选择`statsmodels`而非`sklearn`进行回归，主要是因为其在统计分析场景下接口更为直观，通常只需几行代码即可完成回归任务。

![](img/1f7e1ef39a493137d22575eafabb2ad7_9.png)

## 初始化策略模块

![](img/1f7e1ef39a493137d22575eafabb2ad7_11.png)

上一节我们导入了工具包，本节中我们来看看如何初始化我们的量化策略模块。策略的核心之一是确定调仓频率，即每隔多久重新评估并调整一次投资组合。

![](img/1f7e1ef39a493137d22575eafabb2ad7_13.png)

首先，我们创建一个新的Python文件，例如命名为`factor_strategy.py`。在文件的初始化部分（通常是类的`__init__`方法中），我们需要设置一个定时器（Scheduler）来定义策略的执行周期。

![](img/1f7e1ef39a493137d22575eafabb2ad7_15.png)

![](img/1f7e1ef39a493137d22575eafabb2ad7_17.png)

```python
# 示例：在策略初始化函数中设置按月调仓
self.schedule_rebalance(event_rule='monthly')
```

这里，我们将调仓频率设置为“月度”（`monthly`）。这意味着策略将每个月运行一次`rebalance`函数来调整持仓。相比按日调仓，月度调仓更为常见，既能捕捉市场变化，又避免了过度交易。

![](img/1f7e1ef39a493137d22575eafabb2ad7_19.png)

## 定义调仓函数

![](img/1f7e1ef39a493137d22575eafabb2ad7_21.png)

设置好调仓频率后，我们需要定义具体的调仓逻辑。这个函数（通常命名为`rebalance`）将在每个调仓日被自动调用，其核心任务是筛选出符合我们因子标准的股票。

![](img/1f7e1ef39a493137d22575eafabb2ad7_23.png)

以下是`rebalance`函数中需要完成的关键步骤：

![](img/1f7e1ef39a493137d22575eafabb2ad7_25.png)

1.  **获取全市场股票数据**：首先，我们需要获取当前市场上所有股票的基本信息列表。这可以通过调用数据接口的`get_all_securities`函数实现，并指定类型为股票（`type=‘cs’`）。
2.  **构建股票池**：获取到的股票列表可能包含所有上市公司的证券代码。在实际策略中，我们通常需要根据市值、流动性、上市时间等条件进行初步筛选，过滤掉不符合条件的股票（如ST股、次新股等），形成一个基础的“股票池”。
3.  **应用因子选股**：在后续课程中，我们将在这个股票池的基础上，应用具体的因子（如估值因子、动量因子）进行打分和排序，最终选出要买入的股票组合。

![](img/1f7e1ef39a493137d22575eafabb2ad7_27.png)

```python
def rebalance(self):
    # 步骤1：获取全市场股票列表
    all_stocks = get_all_securities(types=[‘cs’])
    # 步骤2：对股票列表进行初步筛选（示例，后续完善）
    # filtered_stocks = self.filter_stocks(all_stocks)
    # 步骤3：应用因子模型进行选股（后续实现）
    # selected_stocks = self.factor_selection(filtered_stocks)
    pass
```

![](img/1f7e1ef39a493137d22575eafabb2ad7_29.png)

通过以上步骤，我们就搭建起了因子选股策略的基本框架。获取数据是这一切的起点。

![](img/1f7e1ef39a493137d22575eafabb2ad7_31.png)

![](img/1f7e1ef39a493137d22575eafabb2ad7_33.png)

## 总结

![](img/1f7e1ef39a493137d22575eafabb2ad7_35.png)

本节课中我们一起学习了量化策略初始化的关键步骤。我们首先导入了`numpy`、`pandas`和`statsmodels`等核心工具包。接着，在策略初始化模块中设置了按月的调仓频率。最后，我们定义了`rebalance`函数的框架，明确了其三步核心任务：**获取全市场数据、初步筛选股票池、应用因子模型选股**。在接下来的课程中，我们将深入每一步的具体实现，完成一个完整的因子选股策略。