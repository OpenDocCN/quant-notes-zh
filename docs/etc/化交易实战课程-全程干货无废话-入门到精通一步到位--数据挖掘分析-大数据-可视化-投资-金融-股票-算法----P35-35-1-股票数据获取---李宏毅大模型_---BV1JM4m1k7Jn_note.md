# Python金融分析与量化交易实战课程：P35：35.1-股票数据获取 📈

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_1.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_3.png)

在本节课中，我们将学习如何获取股票数据，这是构建量化交易策略的第一步。我们将介绍如何使用特定的工具包来读取和处理股票数据，为后续的因子选股策略打下基础。

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_5.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_7.png)

## 工具包导入 📦

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_9.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_11.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_13.png)

上一节我们介绍了课程的整体目标，本节中我们来看看实现策略所需的具体工具。以下是实现股票数据获取和因子策略需要导入的Python工具包。

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_15.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_17.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_19.png)

```python
import numpy as np
import pandas as pd
from statsmodels import regression
```

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_21.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_23.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_25.png)

*   **`numpy`**：用于高效的数值计算。
*   **`pandas`**：用于数据处理和分析，是处理表格数据的核心库。
*   **`statsmodels`**：用于统计分析，我们将使用其回归模块进行因子中性化操作。

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_27.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_29.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_31.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_33.png)

## 策略初始化与定时器设置 ⏰

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_35.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_37.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_39.png)

在开始获取数据之前，我们需要初始化策略框架，并设定策略的执行频率。以下是策略初始化模块中需要完成的关键设置。

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_41.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_43.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_45.png)

1.  **设定策略执行周期**：我们计划按月对投资组合进行调仓，而不是按天，以避免过于频繁的交易。
2.  **定义调仓函数**：创建一个名为 `rebalance` 的函数，该函数将在每个调仓日被调用，用于执行选股和买卖操作。
3.  **配置定时器**：在策略的构造函数中，使用框架提供的定时器功能，将其设置为按月运行，并指向我们定义的 `rebalance` 函数。

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_47.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_49.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_51.png)

## 获取股票数据 📊

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_53.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_55.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_57.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_59.png)

初始化完成后，下一步是获取可供筛选的股票池。在 `rebalance` 函数中，我们需要读取所有可用的股票数据。以下是获取股票数据的具体步骤。

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_61.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_63.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_65.png)

1.  **调用数据接口**：使用框架提供的API函数（例如 `get_all_securities`）来获取指定市场（如A股）的所有股票基础信息。
2.  **指定参数**：在调用函数时，需要传入参数 `type=‘cs’` 来指明获取的是普通股票数据。
3.  **数据预处理**：获取到的股票列表可能包含所有上市股票，后续我们需要根据策略规则（如剔除ST股、次新股等）对这个列表进行过滤，以构建最终的备选股票池。

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_67.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_69.png)

![](img/eb6eb94ed1f3c2f972b295d8b22d5563_71.png)

本节课中我们一起学习了量化策略初始化的步骤，包括导入必要的工具包、设置策略执行频率以及获取原始股票数据的方法。这些是构建任何因子选股策略的基础准备工作。