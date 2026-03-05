# 量化交易与Python金融分析实战：P7：连续指标变化情况分析 📈

![](img/8f8afd592bafc9f0987713920bbce34b_0.png)

![](img/8f8afd592bafc9f0987713920bbce34b_2.png)

在本节课中，我们将要学习如何分析金融时间序列的连续变化情况。我们将探讨为什么简单的增长率累加会失效，并引入对数收益率这一核心概念，来计算和可视化“初始投资”随时间变化的真实价值。

## 概述

上一节我们介绍了如何计算时间序列的简单差异和增长率。本节中我们来看看，当我们需要分析一个连续时间段内的整体增长情况时，应该如何处理。特别是，我们将解决“增长率不能简单累加”的问题。

## 为什么增长率不能直接累加？ 🤔

![](img/8f8afd592bafc9f0987713920bbce34b_4.png)

首先，我们通过一个例子来理解问题所在。假设我们观察一只股票的价格变化：

*   第一天价格：100元
*   第二天价格：50元
*   第三天价格：75元

如果分别计算每日的增长率：
*   第二天增长率 = (50 - 100) / 100 = **-0.5** (下跌50%)
*   第三天增长率 = (75 - 50) / 50 = **0.5** (上涨50%)

若将这两个增长率简单累加：-0.5 + 0.5 = 0。结果为零，似乎意味着股票从第一天到第三天“既没涨也没跌”。但观察实际价格：从100元变为75元，实际上是下跌了。这表明，**增长率不能直接进行加法运算**，因为它无法正确反映连续时间内的整体复合变化。

![](img/8f8afd592bafc9f0987713920bbce34b_6.png)

![](img/8f8afd592bafc9f0987713920bbce34b_8.png)

## 解决方案：引入对数收益率

为了能够对连续变化进行累加，我们需要借助数学中的对数。核心思想是计算**对数收益率**，其加法等价于原始价格比值的乘法。

![](img/8f8afd592bafc9f0987713920bbce34b_10.png)

**公式**：
`log(今日价格 / 昨日价格)`

对于上面的例子：
*   第一到二天的对数收益率 = log(50 / 100)
*   第二到三天的对数收益率 = log(75 / 50)

![](img/8f8afd592bafc9f0987713920bbce34b_12.png)

将对数收益率累加：`log(50/100) + log(75/50) = log( (50/100) * (75/50) ) = log(75/100)`

![](img/8f8afd592bafc9f0987713920bbce34b_14.png)

![](img/8f8afd592bafc9f0987713920bbce34b_16.png)

![](img/8f8afd592bafc9f0987713920bbce34b_18.png)

最终结果 `log(75/100)` 直接反映了**第三天价格与第一天价格的整体比率关系**。这解决了累加问题，其经济含义是：如果初始投资为1个单位（例如1元），经过一系列涨跌后，最终价值相当于多少。

![](img/8f8afd592bafc9f0987713920bbce34b_20.png)

![](img/8f8afd592bafc9f0987713920bbce34b_22.png)

## 在Python中计算连续变化

![](img/8f8afd592bafc9f0987713920bbce34b_24.png)

![](img/8f8afd592bafc9f0987713920bbce34b_26.png)

以下是实现连续时间序列分析的步骤和代码。

![](img/8f8afd592bafc9f0987713920bbce34b_28.png)

![](img/8f8afd592bafc9f0987713920bbce34b_30.png)

### 1. 计算每日对数收益率

![](img/8f8afd592bafc9f0987713920bbce34b_32.png)

![](img/8f8afd592bafc9f0987713920bbce34b_34.png)

我们需要计算每个交易日相对于前一个交易日的对数收益率。这可以通过 `pandas` 的 `.shift()` 方法方便地实现。

**代码**：
```python
import numpy as np
import pandas as pd

![](img/8f8afd592bafc9f0987713920bbce34b_36.png)

![](img/8f8afd592bafc9f0987713920bbce34b_38.png)

![](img/8f8afd592bafc9f0987713920bbce34b_40.png)

# 假设 `data` 是一个包含股价的Pandas Series或DataFrame
# 计算对数收益率
log_returns = np.log(data / data.shift(1))
```

*   `data.shift(1)` 将整个序列向下移动一行，使得每一行都对应其“前一天”的数据。
*   `data / data.shift(1)` 计算每日价格与前一日价格的比值。
*   `np.log()` 计算该比值的自然对数，即得到对数收益率序列 `log_returns`。

![](img/8f8afd592bafc9f0987713920bbce34b_42.png)

![](img/8f8afd592bafc9f0987713920bbce34b_44.png)

### 2. 计算累积收益率（初始价值的演变）

![](img/8f8afd592bafc9f0987713920bbce34b_46.png)

得到每日对数收益率后，我们可以通过累加来还原“初始1单位投资”随时间变化的价值。

![](img/8f8afd592bafc9f0987713920bbce34b_48.png)

**代码**：
```python
# 计算累积对数收益率
cumulative_log_returns = log_returns.cumsum()

![](img/8f8afd592bafc9f0987713920bbce34b_50.png)

![](img/8f8afd592bafc9f0987713920bbce34b_52.png)

# 将对数收益率还原为实际价值（即：1元初始投资的价值演变）
cumulative_value = np.exp(cumulative_log_returns)
```

*   `.cumsum()` 是Pandas的累积求和函数。`log_returns.cumsum()` 计算了从起始日到当前日的**累积对数收益率**。
*   `np.exp()` 是自然指数函数，是 `np.log()` 的逆运算。`np.exp(cumulative_log_returns)` 将累积对数收益率还原为实际的资产价值倍数。例如，结果为2.5表示初始的1元现在值2.5元。

![](img/8f8afd592bafc9f0987713920bbce34b_54.png)

![](img/8f8afd592bafc9f0987713920bbce34b_56.png)

### 3. 可视化结果

![](img/8f8afd592bafc9f0987713920bbce34b_58.png)

我们可以将 `cumulative_value` 绘制成图表，直观地展示各只股票从起始日（假设初始价值为1）到结束日的价值增长轨迹。

![](img/8f8afd592bafc9f0987713920bbce34b_60.png)

![](img/8f8afd592bafc9f0987713920bbce34b_62.png)

**代码**：
```python
import matplotlib.pyplot as plt

![](img/8f8afd592bafc9f0987713920bbce34b_64.png)

cumulative_value.plot(figsize=(12, 6))
plt.title(‘初始1单位投资的价值演变 (2010年基准)’)
plt.ylabel(‘价值 (倍数)’)
plt.xlabel(‘日期’)
plt.legend(loc=‘best’)
plt.grid(True)
plt.show()
```

![](img/8f8afd592bafc9f0987713920bbce34b_66.png)

![](img/8f8afd592bafc9f0987713920bbce34b_68.png)

通过这张图，可以清晰地比较不同股票在相同时间段内的整体表现。例如，可能发现亚马逊(AMZN)的1元投资增长到了10元，而苹果(AAPL)增长到了4元多。

![](img/8f8afd592bafc9f0987713920bbce34b_70.png)

## 本节核心操作总结

![](img/8f8afd592bafc9f0987713920bbce34b_72.png)

![](img/8f8afd592bafc9f0987713920bbce34b_74.png)

![](img/8f8afd592bafc9f0987713920bbce34b_76.png)

本节课中我们一起学习了金融时间序列的连续变化分析方法。以下是关键操作的回顾：

![](img/8f8afd592bafc9f0987713920bbce34b_78.png)

![](img/8f8afd592bafc9f0987713920bbce34b_80.png)

*   **计算差异值**：使用 `.diff()` 方法，非常简单。
*   **计算简单增长率**：(后值 - 前值) / 前值。
*   **计算对数收益率（核心）**：`np.log( data / data.shift(1) )`。这是分析连续复利增长的基础。
*   **计算累积价值演变**：先对对数收益率进行累积求和 (`.cumsum()`)，再通过指数函数 (`np.exp()`) 还原为实际价值倍数。
*   **可视化**：将累积价值绘制成曲线图，用于直观比较。

![](img/8f8afd592bafc9f0987713920bbce34b_82.png)

![](img/8f8afd592bafc9f0987713920bbce34b_84.png)

通过引入对数收益率，我们成功地解决了增长率无法累加的问题，并能够准确计算出初始投资在一段连续时间内经历所有涨跌后的最终价值。这是金融数据分析中一个非常实用且重要的工具。