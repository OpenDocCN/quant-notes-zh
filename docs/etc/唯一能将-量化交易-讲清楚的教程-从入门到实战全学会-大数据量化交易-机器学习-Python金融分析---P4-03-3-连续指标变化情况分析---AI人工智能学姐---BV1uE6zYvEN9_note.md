# Python金融量化+股票交易：P4：03-3-连续指标变化情况分析 📈

![](img/6a3fffaea251852ceb0df650ba08e154_0.png)

![](img/6a3fffaea251852ceb0df650ba08e154_2.png)

在本节课中，我们将要学习如何分析金融时间序列的连续变化情况。我们将探讨为什么简单的增长率累加会失效，并引入对数变换来解决这个问题，最终计算出“初始投资的一块钱相当于未来某一天价值多少”这一核心指标。

## 概述

上一节我们介绍了如何计算简单的差异值和增长率。本节中我们来看看，当我们面对一个连续的时间序列，并希望计算其累积变化时，会遇到什么问题以及如何解决。

## 简单增长率累加的问题

![](img/6a3fffaea251852ceb0df650ba08e154_4.png)

首先，我们思考一个问题：能否将每天的增长率简单相加，来代表一段时间的总增长？

答案是否定的。让我们通过一个例子来说明。

假设有一只股票：
*   第一天价格：`100`
*   第二天价格：`50`
*   第三天价格：`75`

计算每日增长率：
*   第二天增长率 = `(50 - 100) / 100 = -0.5`
*   第三天增长率 = `(75 - 50) / 50 = 0.5`

如果将这两个增长率相加：`-0.5 + 0.5 = 0`。结果为零，似乎意味着股票从第一天到第三天既没涨也没跌。

![](img/6a3fffaea251852ceb0df650ba08e154_6.png)

![](img/6a3fffaea251852ceb0df650ba08e154_8.png)

但观察原始数据：第一天`100`，第三天`75`，股价实际上是下跌的。这说明**简单的增长率加法无法正确反映连续时间内的整体变化**。

## 解决方案：引入对数收益率

![](img/6a3fffaea251852ceb0df650ba08e154_10.png)

对于连续时间的累积计算，我们需要借助数学中的对数。

核心思想是：计算**对数收益率**并进行累加。对数收益率的加法，等价于原始价格比值的乘法。

公式如下：
`总对数收益率 = log(第N天价格 / 第1天价格) = log(价格2/价格1) + log(价格3/价格2) + ... + log(价格N/价格(N-1))`

![](img/6a3fffaea251852ceb0df650ba08e154_12.png)

![](img/6a3fffaea251852ceb0df650ba08e154_14.png)

其中，`log`通常指以自然常数`e`为底的自然对数。

![](img/6a3fffaea251852ceb0df650ba08e154_16.png)

![](img/6a3fffaea251852ceb0df650ba08e154_18.png)

这解决了累加问题，因为对数的加法等于内部的乘法：
`log(a) + log(b) = log(a * b)`

![](img/6a3fffaea251852ceb0df650ba08e154_20.png)

![](img/6a3fffaea251852ceb0df650ba08e154_22.png)

最终，`log(价格N/价格1)` 恰恰反映了第一天与最后一天价格的整体差异。将其还原（取指数），就能得到“第一天的一单位价值相当于最后一天是多少”。

![](img/6a3fffaea251852ceb0df650ba08e154_24.png)

![](img/6a3fffaea251852ceb0df650ba08e154_26.png)

## 在Python中实现连续变化分析

![](img/6a3fffaea251852ceb0df650ba08e154_28.png)

![](img/6a3fffaea251852ceb0df650ba08e154_30.png)

以下是实现步骤：

### 1. 计算每日的对数收益率

![](img/6a3fffaea251852ceb0df650ba08e154_32.png)

我们需要计算 `log(当天价格 / 前一天价格)`。这里使用 `shift()` 函数来获取前一天的数据。

![](img/6a3fffaea251852ceb0df650ba08e154_34.png)

![](img/6a3fffaea251852ceb0df650ba08e154_36.png)

```python
import numpy as np
import pandas as pd

# 假设 data 是一个包含股价的Pandas Series
# 计算对数收益率
log_returns = np.log(data / data.shift(1))
```

![](img/6a3fffaea251852ceb0df650ba08e154_38.png)

### 2. 计算累积对数收益率

![](img/6a3fffaea251852ceb0df650ba08e154_40.png)

使用 `cumsum()` 函数对对数收益率进行累加，得到从起始日到每一天的累积变化。

![](img/6a3fffaea251852ceb0df650ba08e154_42.png)

```python
# 计算累积对数收益率
cumulative_log_returns = log_returns.cumsum()
```

![](img/6a3fffaea251852ceb0df650ba08e154_44.png)

![](img/6a3fffaea251852ceb0df650ba08e154_46.png)

### 3. 还原为实际价值倍数

将对数收益率还原为实际的价值倍数。即计算 `e^(累积对数收益率)`。

![](img/6a3fffaea251852ceb0df650ba08e154_48.png)

![](img/6a3fffaea251852ceb0df650ba08e154_50.png)

```python
# 还原为实际价值倍数 (以第一天的1个单位为基础)
normalized_price = np.exp(cumulative_log_returns)
```

### 4. 可视化结果

![](img/6a3fffaea251852ceb0df650ba08e154_52.png)

![](img/6a3fffaea251852ceb0df650ba08e154_54.png)

将 `normalized_price` 绘制成图表，可以直观看到初始投资（假设为1元）随时间推移的价值变化。

```python
import matplotlib.pyplot as plt

![](img/6a3fffaea251852ceb0df650ba08e154_56.png)

![](img/6a3fffaea251852ceb0df650ba08e154_58.png)

normalized_price.plot(figsize=(16, 8))
plt.title('初始1单位投资的价值变化 (基于对数收益率)')
plt.ylabel('价值倍数')
plt.xlabel('日期')
plt.show()
```

## 核心操作总结

![](img/6a3fffaea251852ceb0df650ba08e154_60.png)

![](img/6a3fffaea251852ceb0df650ba08e154_62.png)

本节课中我们一起学习了金融时间序列的连续变化分析方法。以下是关键操作点：

![](img/6a3fffaea251852ceb0df650ba08e154_64.png)

![](img/6a3fffaea251852ceb0df650ba08e154_66.png)

*   **计算差异值**：使用 `.diff()` 方法。
*   **计算简单增长率**：使用 `.pct_change()` 方法或手动计算 `(当期-上期)/上期`。
*   **计算对数收益率（解决累加问题）**：使用公式 `np.log(当期 / 上期)`，并结合 `.shift(1)` 获取上期值。
*   **计算累积变化**：对对数收益率使用 `.cumsum()` 进行累加。
*   **还原为实际价值**：对累积对数收益率使用 `np.exp()` 取指数。

![](img/6a3fffaea251852ceb0df650ba08e154_68.png)

![](img/6a3fffaea251852ceb0df650ba08e154_70.png)

通过这种方法，我们可以准确评估一项资产在连续时间内为初始投资带来的真实累积回报，例如“2010年投资1元，到2018年相当于多少元”，这是量化分析中一个非常基础和重要的指标。