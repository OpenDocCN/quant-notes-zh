# Python金融分析与量化交易实战教程：P35：股票数据获取 📈

在本节课中，我们将学习如何获取股票数据，这是构建量化交易策略的第一步。我们将从数据源读取数据，并进行初步的筛选，为后续的因子计算和策略构建打下基础。

![](img/5bb0b4bc2503c17229e439d04efe10cf_1.png)

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何获取和处理股票数据。

![](img/5bb0b4bc2503c17229e439d04efe10cf_3.png)

## 导入必要的工具包

在开始编写代码之前，我们需要导入一些必要的Python库。这些库将帮助我们进行数据处理、数值计算和统计分析。

![](img/5bb0b4bc2503c17229e439d04efe10cf_5.png)

以下是需要导入的核心工具包：

![](img/5bb0b4bc2503c17229e439d04efe10cf_7.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_9.png)

```python
import numpy as np
import pandas as pd
from statsmodels.api import OLS
```

![](img/5bb0b4bc2503c17229e439d04efe10cf_11.png)

*   **numpy**：用于高效的数值计算。
*   **pandas**：用于数据处理和分析，是金融数据分析的核心工具。
*   **statsmodels**：用于统计分析，我们将使用其`OLS`模块进行回归分析，这在后续的因子中性化处理中会用到。

![](img/5bb0b4bc2503c17229e439d04efe10cf_13.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_15.png)

## 初始化策略与设置定时器

![](img/5bb0b4bc2503c17229e439d04efe10cf_17.png)

在量化策略中，我们需要定义策略的执行频率，例如是按月调仓还是按日调仓。这通常在策略的初始化函数中完成。

![](img/5bb0b4bc2503c17229e439d04efe10cf_19.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_21.png)

以下是策略初始化的关键步骤：

![](img/5bb0b4bc2503c17229e439d04efe10cf_23.png)

```python
def initialize(context):
    # 设置按月调仓的定时器
    run_monthly(rebalance, monthday=1)
```

![](img/5bb0b4bc2503c17229e439d04efe10cf_25.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_27.png)

*   `initialize(context)`：这是策略的初始化函数，`context`对象用于存储策略的全局信息。
*   `run_monthly(func, monthday)`：这是一个定时器函数，它指定在每月的第`monthday`天执行名为`func`的函数。这里设置为每月1号执行`rebalance`函数。

![](img/5bb0b4bc2503c17229e439d04efe10cf_29.png)

## 定义调仓函数

![](img/5bb0b4bc2503c17229e439d04efe10cf_31.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_33.png)

`rebalance`函数是策略的核心，它定义了每次调仓时需要执行的操作，包括获取股票池、筛选股票以及买卖决策。

![](img/5bb0b4bc2503c17229e439d04efe10cf_35.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_37.png)

以下是`rebalance`函数的基本框架：

```python
def rebalance(context):
    # 1. 获取所有股票数据
    all_stocks = get_all_securities(types=[‘stock‘])
    # 2. 对股票进行筛选（后续步骤）
    # ...
```

![](img/5bb0b4bc2503c17229e439d04efe10cf_39.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_41.png)

## 获取股票数据

![](img/5bb0b4bc2503c17229e439d04efe10cf_43.png)

获取数据是量化分析的基础。我们将使用平台提供的API来获取当前市场上所有的股票信息。

![](img/5bb0b4bc2503c17229e439d04efe10cf_45.png)

获取所有股票数据的代码如下：

![](img/5bb0b4bc2503c17229e439d04efe10cf_47.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_49.png)

```python
all_stocks = get_all_securities(types=[‘stock‘])
```

![](img/5bb0b4bc2503c17229e439d04efe10cf_51.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_53.png)

*   `get_all_securities(types=[‘stock‘])`：这个API函数用于获取指定类型（这里是`‘stock‘`，即股票）的所有证券的基本信息。返回的结果通常是一个`pandas DataFrame`，包含股票的代码、名称、上市日期等信息。

![](img/5bb0b4bc2503c17229e439d04efe10cf_55.png)

## 数据筛选与预处理

![](img/5bb0b4bc2503c17229e439d04efe10cf_57.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_59.png)

获取到所有股票后，我们通常不会直接使用全部数据。为了构建有效的策略，我们需要对股票池进行筛选，例如剔除ST股票、次新股或流动性不足的股票。

![](img/5bb0b4bc2503c17229e439d04efe10cf_61.png)

对初始股票池进行筛选的思路如下：

![](img/5bb0b4bc2503c17229e439d04efe10cf_63.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_65.png)

```python
# 假设我们有一个筛选条件列表
filtered_stocks = []
for stock in all_stocks.index:
    # 应用各种筛选条件，例如：
    # - 非ST股票
    # - 上市超过一定天数
    # - 日均成交额大于某个阈值
    if meets_criteria(stock):
        filtered_stocks.append(stock)
# 更新context中的股票池
context.stock_pool = filtered_stocks
```

![](img/5bb0b4bc2503c17229e439d04efe10cf_67.png)

![](img/5bb0b4bc2503c17229e439d04efe10cf_69.png)

*   在实际操作中，我们需要定义具体的`meets_criteria`函数来实现筛选逻辑。
*   筛选后的股票列表`filtered_stocks`将作为我们后续因子计算和选股的基础股票池。

![](img/5bb0b4bc2503c17229e439d04efe10cf_71.png)

本节课中我们一起学习了量化交易策略中获取和初步处理股票数据的方法。我们首先导入了必要的工具包，然后在策略初始化函数中设置了按月调仓的定时器。接着，我们定义了`rebalance`调仓函数，并使用`get_all_securities` API获取了全市场的股票数据。最后，我们讨论了如何对初始股票池进行筛选，以得到更适合构建策略的股票集合。这些步骤为后续的因子分析、策略构建和回测做好了数据准备。