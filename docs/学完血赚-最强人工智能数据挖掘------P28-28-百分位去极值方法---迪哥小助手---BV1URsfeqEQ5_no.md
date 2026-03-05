# 金融量化分析：P28：百分位去极值方法 📊

在本节课中，我们将要学习如何对因子数据进行预处理操作。因子可以理解为影响最终投资决策的指标，例如市净率、营收增长率等。处理这些数据是构建有效量化策略的关键步骤。我们将按照“三步走”策略，依次讲解去极值、标准化和中性化这三个核心预处理方法。

## 因子数据预处理概述

上一节我们介绍了因子数据的基本概念。本节中，我们来看看如何处理这些数据。当我们拿到因子数据后，不能直接用于建模，通常需要进行预处理。这类似于数据挖掘任务，我们需要分析每个因子（X1, X2, X3...）对最终收益（Y）的影响，并从中做出选择。本节课将重点介绍预处理的第一步：去极值。

## 第一步：去极值处理

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_1.png)

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_2.png)

去极值的目标是处理数据中的离群点或异常值。一种常见思路不是直接删除这些点，而是将它们“拉回”到我们设定的合理边界内。例如，如果下界是-5，而某个值是-10，我们将其调整为-5。以下是几种去极值的方法，首先介绍分位数法。

### 分位数法

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_4.png)

分位数是理解数据分布的关键工具。与均值相比，中位数等分位数对极端值不敏感，能更好地代表数据的中心趋势。

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_6.png)

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_7.png)

*   **中位数 (Q2)**：将数据从小到大排列后，处于正中间位置的值。对于奇数个数据，取中间值；对于偶数个数据，取中间两个数的平均值。
*   **第一四分位数 (Q1)**：将所有数据分成四等份，处于第一份末尾（即25%位置）的值。
*   **第三四分位数 (Q3)**：将所有数据分成四等份，处于第三份末尾（即75%位置）的值。

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_9.png)

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_10.png)

利用这些分位数，我们可以定义数据的正常范围。一种常用的方法是计算**四分位距 (IQR)**，其公式为：
`IQR = Q3 - Q1`
然后，设定正常值的上下界：
`上界 = Q3 + 1.5 * IQR`
`下界 = Q1 - 1.5 * IQR`
任何大于上界或小于下界的值，都将被调整为相应的边界值。

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_11.png)

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_12.png)

## 代码实践：分位数去极值

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_14.png)

接下来，我们通过代码演示如何使用分位数法进行去极值处理。虽然这里以股价数据为例，但方法是通用的，稍后可以在量化平台中应用于实际的因子数据。

```python
import pandas as pd
import numpy as np

# 示例：读取苹果公司股价数据
data = pd.read_csv(‘apple_stock.csv’)
price_data = data[‘Close’]  # 以收盘价为例

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_16.png)

def winsorize_by_quantile(series, n=5):
    """
    使用百分位法去极值
    :param series: 待处理的数据序列
    :param n: 百分位阈值，例如5表示使用5%和95%分位点作为边界
    :return: 处理后的数据序列
    """
    # 计算上下分位点
    low_bound = series.quantile(n / 100.0)
    high_bound = series.quantile(1 - n / 100.0)
    # 将超出边界的值拉回边界
    winsorized_series = series.clip(lower=low_bound, upper=high_bound)
    return winsorized_series

# 应用去极值函数，使用5%分位点
processed_data = winsorize_by_quantile(price_data, n=5)
print(“原始数据描述：\n”, price_data.describe())
print(“\n去极值后数据描述：\n”, processed_data.describe())
```

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_18.png)

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_19.png)

在这段代码中，`clip` 函数是关键，它能将序列中所有小于下界`low_bound`的值设置为`low_bound`，将所有大于上界`high_bound`的值设置为`high_bound`，从而实现“拉回”操作。

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_21.png)

## 总结

![](img/60c8d442fcbba6bbfc29a3ab14f06c36_23.png)

本节课中我们一起学习了因子数据预处理的第一步：去极值。我们重点介绍了**分位数法**，该方法通过计算数据的特定百分位点（如5%和95%）来确定正常值的边界，并将超出边界的极端值调整到边界上。这种方法比直接删除数据更为稳健，保留了数据样本量。理解并掌握去极值是保证后续建模分析结果可靠性的重要基础。在接下来的课程中，我们将继续学习标准化和中性化这两个预处理步骤。