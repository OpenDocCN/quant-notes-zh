# Python金融量化：P35：股票数据获取

![](img/cf73b518d1cdc81d375ca413931c96af_0.png)

![](img/cf73b518d1cdc81d375ca413931c96af_2.png)

在本节课中，我们将学习如何获取股票数据，这是构建量化交易策略的第一步。我们将从导入必要的工具包开始，逐步讲解如何获取并初步处理股票数据，为后续的因子选股策略打下基础。

![](img/cf73b518d1cdc81d375ca413931c96af_4.png)

## 工具包导入

![](img/cf73b518d1cdc81d375ca413931c96af_6.png)

首先，我们需要导入一些在数据处理和分析中必不可少的Python库。

![](img/cf73b518d1cdc81d375ca413931c96af_8.png)

![](img/cf73b518d1cdc81d375ca413931c96af_10.png)

![](img/cf73b518d1cdc81d375ca413931c96af_12.png)

以下是本节课将要用到的核心工具包：

![](img/cf73b518d1cdc81d375ca413931c96af_14.png)

![](img/cf73b518d1cdc81d375ca413931c96af_16.png)

*   **numpy**：用于高效的数值计算。
*   **pandas**：用于数据处理和分析，是金融数据分析的核心工具。
*   **statsmodels**：一个用于统计建模和计量经济学的库，我们将使用其回归模块进行因子中性化操作。

![](img/cf73b518d1cdc81d375ca413931c96af_18.png)

![](img/cf73b518d1cdc81d375ca413931c96af_20.png)

![](img/cf73b518d1cdc81d375ca413931c96af_22.png)

```python
import numpy as np
import pandas as pd
from statsmodels import regression
```

![](img/cf73b518d1cdc81d375ca413931c96af_24.png)

![](img/cf73b518d1cdc81d375ca413931c96af_26.png)

## 初始化与数据获取

![](img/cf73b518d1cdc81d375ca413931c96af_28.png)

![](img/cf73b518d1cdc81d375ca413931c96af_30.png)

![](img/cf73b518d1cdc81d375ca413931c96af_32.png)

上一节我们介绍了所需的工具包，本节中我们来看看如何初始化策略并获取股票数据。在量化策略中，我们通常需要设定一个调仓周期，例如每月调仓一次，并获取一个初始的股票池。

![](img/cf73b518d1cdc81d375ca413931c96af_34.png)

![](img/cf73b518d1cdc81d375ca413931c96af_36.png)

![](img/cf73b518d1cdc81d375ca413931c96af_38.png)

以下是初始化策略和获取股票数据的关键步骤：

![](img/cf73b518d1cdc81d375ca413931c96af_40.png)

![](img/cf73b518d1cdc81d375ca413931c96af_42.png)

1.  **设定调仓周期**：我们使用一个定时器来设定策略的执行频率。在本例中，我们设定为每月执行一次调仓操作。这通常在策略的初始化函数中完成。
2.  **定义调仓函数**：我们定义一个名为 `rebalance` 的函数。这个函数将在每个调仓周期被调用，负责执行选股和交易逻辑。
3.  **获取股票池**：在 `rebalance` 函数中，第一步是获取所有可交易的股票。我们可以使用量化平台提供的API（例如 `get_all_securities`）来获取市场上所有股票的基本信息列表。

![](img/cf73b518d1cdc81d375ca413931c96af_44.png)

![](img/cf73b518d1cdc81d375ca413931c96af_46.png)

```python
# 示例：获取所有股票代码
# 假设 `get_all_securities` 是一个返回股票DataFrame的API函数
stock_pool = get_all_securities(types=[‘stock’])
```

![](img/cf73b518d1cdc81d375ca413931c96af_48.png)

![](img/cf73b518d1cdc81d375ca413931c96af_50.png)

## 数据预处理与筛选

![](img/cf73b518d1cdc81d375ca413931c96af_52.png)

![](img/cf73b518d1cdc81d375ca413931c96af_54.png)

获取到初始股票池后，我们通常不会使用所有股票。接下来，我们需要对这些股票进行初步筛选，以缩小我们的选股范围。

![](img/cf73b518d1cdc81d375ca413931c96af_56.png)

![](img/cf73b518d1cdc81d375ca413931c96af_58.png)

![](img/cf73b518d1cdc81d375ca413931c96af_60.png)

以下是数据预处理与筛选的初步思路：

![](img/cf73b518d1cdc81d375ca413931c96af_62.png)

![](img/cf73b518d1cdc81d375ca413931c96af_64.png)

![](img/cf73b518d1cdc81d375ca413931c96af_66.png)

*   **数据读取**：通过API获取的股票列表通常包含股票代码、名称等信息。
*   **初步筛选**：我们可能根据上市时间、市值、流动性（如日均成交量）等基本条件对股票进行过滤，剔除不符合条件的股票（例如ST股、次新股或交易不活跃的股票）。
*   **保留股票ID**：筛选后，我们将得到一个精简的股票代码列表，作为后续因子计算和选股的基础池。

![](img/cf73b518d1cdc81d375ca413931c96af_68.png)

![](img/cf73b518d1cdc81d375ca413931c96af_70.png)

本节课中我们一起学习了量化策略中获取和初步处理股票数据的基本流程。我们首先导入了 `numpy`、`pandas` 和 `statsmodels` 等核心工具包。然后，我们讲解了如何设定策略的调仓周期，并定义了调仓函数。最后，我们介绍了通过API获取全市场股票数据，并进行初步筛选以构建基础股票池的方法。这是构建任何量化交易策略的起点，为后续深入进行因子分析和策略回测做好了准备。