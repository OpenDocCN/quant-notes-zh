# Python金融分析量化交易：P35：1-股票数据获取 📈

![](img/fd85c816541ffc7e0ace3ad95509f88d_0.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_2.png)

在本节课中，我们将学习如何获取股票数据，这是构建因子选股策略的第一步。我们将从初始化环境开始，逐步完成数据读取和初步筛选，为后续的因子计算和策略回测打下基础。

![](img/fd85c816541ffc7e0ace3ad95509f88d_4.png)

## 初始化环境与导入工具包 🛠️

![](img/fd85c816541ffc7e0ace3ad95509f88d_6.png)

首先，我们需要创建一个新的Python文件，并导入后续分析中必需的工具包。以下是核心步骤：

![](img/fd85c816541ffc7e0ace3ad95509f88d_8.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_10.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_12.png)

*   **导入NumPy和Pandas**：用于数据处理和数值计算。
*   **导入回归分析工具**：为了进行因子中性化处理，我们需要建立回归方程。这里选择使用`statsmodels`库，因为它使用起来相对简单直接。

![](img/fd85c816541ffc7e0ace3ad95509f88d_14.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_16.png)

以下是具体的导入代码：

![](img/fd85c816541ffc7e0ace3ad95509f88d_18.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_20.png)

```python
import numpy as np
import pandas as pd
from statsmodels import regression
```

![](img/fd85c816541ffc7e0ace3ad95509f88d_22.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_24.png)

## 定义策略初始化模块 ⚙️

![](img/fd85c816541ffc7e0ace3ad95509f88d_26.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_28.png)

上一节我们导入了必要的工具包，本节中我们来看看如何初始化我们的量化策略。初始化模块的核心是设置策略的运行周期和调仓逻辑。

![](img/fd85c816541ffc7e0ace3ad95509f88d_30.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_32.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_34.png)

我们需要在策略的构造函数中定义一个定时器。这个定时器决定了策略调仓的频率。对于股票策略而言，按日调仓过于频繁，通常按月或按周执行。这里我们选择按月调仓。

![](img/fd85c816541ffc7e0ace3ad95509f88d_36.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_38.png)

在定时器中，我们需要指定一个调仓函数（例如 `rebalance`）。这个函数将在每个调仓日被调用，执行具体的选股和交易逻辑。

![](img/fd85c816541ffc7e0ace3ad95509f88d_40.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_42.png)

```python
# 示例：在策略初始化中设置按月调仓
self.schedule.rebalance(self.rebalance, monthday=1)  # 假设每月1号调仓
```

![](img/fd85c816541ffc7e0ace3ad95509f88d_44.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_46.png)

## 获取与筛选股票数据 📊

![](img/fd85c816541ffc7e0ace3ad95509f88d_48.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_50.png)

初始化模块设置好后，接下来我们进入核心环节：获取并处理股票数据。在`rebalance`函数中，第一步就是获取当前市场上所有的股票。

![](img/fd85c816541ffc7e0ace3ad95509f88d_52.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_54.png)

我们可以使用平台提供的API（例如 `get_all_securities`）来获取全市场股票的基本信息列表。这个列表包含了所有可交易股票的代码等标识信息。

![](img/fd85c816541ffc7e0ace3ad95509f88d_56.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_58.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_60.png)

然而，我们通常不会交易所有股票，需要对股票池进行初步筛选。例如，我们可能只关注主板股票，或者排除ST股、次新股等。因此，在获取全量股票ID后，需要根据一定的规则进行过滤，得到一个更精简、更符合策略逻辑的初始股票池。

![](img/fd85c816541ffc7e0ace3ad95509f88d_62.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_64.png)

```python
# 示例：获取全市场股票并初步筛选
all_stocks = get_all_securities(types=[‘stock’], date=None)  # 获取所有股票
# 假设进行简单筛选，例如只保留上市超过一年的股票
filtered_stocks = filter_stocks(all_stocks)
```

![](img/fd85c816541ffc7e0ace3ad95509f88d_66.png)

![](img/fd85c816541ffc7e0ace3ad95509f88d_68.png)

本节课中我们一起学习了量化策略初始化的关键步骤，包括导入必要的Python库、设置策略的调仓周期以及获取并初步筛选股票数据。这些是构建任何因子选股策略的基础准备工作。在接下来的课程中，我们将基于这个股票池，进行因子的计算、去极值、标准化和中性化等处理。