# 金融数据分析：P26：3-最大回撤区间 📉

![](img/01dbb13fe523592fe83664088e0fbfee_0.png)

![](img/01dbb13fe523592fe83664088e0fbfee_2.png)

在本节课中，我们将要学习金融数据分析中的一个重要风险指标——**最大回撤**。我们将理解它的概念、计算方式，并通过Python代码演示如何计算它。

## 概述

在策略分析中，我们不仅需要评估策略的盈利能力，还需要评估其风险。最大回撤就是衡量投资策略风险的关键指标之一。它描述的是在投资过程中，资产净值从最高点到之后最低点的最大跌幅，反映了投资者可能经历的最糟糕时期。

## 什么是最大回撤？

最大回撤描述的是投资过程中“最惨”的阶段。它衡量的是从任意一个历史高点开始，到之后任意一个低点为止，资产价值下跌的最大幅度。

具体来说，它关注的是两点：**跌幅有多大**以及**这个糟糕的时期持续了多久**。这个指标不能仅从字面理解，它核心描述的是投资组合价值缩水的严重程度。

我们可以用以下公式来定义最大回撤：

**最大回撤 = max( (Pi - Pj) / Pi )**

其中：
*   `Pi` 代表第 `i` 天的资产价格（或净值）。
*   `Pj` 代表第 `i` 天之后（`j > i`）的某一天资产价格。
*   我们需要遍历所有可能的 `i` 和 `j`（`j > i`）组合，找出 `(Pi - Pj) / Pi` 这个比值最大的情况。

这个比值计算的是从高点 `Pi` 到低点 `Pj` 的**相对跌幅**，即资产缩水的比例。例如，从15元跌到1元，最大回撤就是 `(15-1)/15 ≈ 93.3%`。

## 为什么最大回撤很重要？

最大回撤是一个**风险指标**。一个策略可能最终是盈利的，但如果在过程中经历了巨大的回撤，投资者可能会因为无法承受心理压力或资金链断裂而在低点被迫退出，从而无法享受到后期的反弹收益。

历史案例，如2008年全球金融危机，股市从高点大幅下挫，许多投资者承受了巨大损失，这正是最大回撤所试图量化的风险。了解策略的最大回撤，有助于投资者评估自己能否承受相应的波动和风险。

![](img/01dbb13fe523592fe83664088e0fbfee_4.png)

上一节我们介绍了最大回撤的概念和重要性，本节中我们来看看如何用Python进行计算。

## 如何用Python计算最大回撤？

计算最大回撤的核心思路是：找到历史上每一个“高点”，并计算从该高点之后出现的所有“低点”中，相对跌幅最大的那一个。

![](img/01dbb13fe523592fe83664088e0fbfee_6.png)

我们将使用Pandas库来高效地完成这个计算。关键步骤是使用 `.cummax()` 函数。

![](img/01dbb13fe523592fe83664088e0fbfee_8.png)

### 关键函数：`.cummax()`

![](img/01dbb13fe523592fe83664088e0fbfee_10.png)

`.cummax()` 函数返回序列截至当前行的历史最大值。这与我们之前学过的 `.cumsum()`（累计和）函数类似，只不过这里计算的是累计最大值。

![](img/01dbb13fe523592fe83664088e0fbfee_12.png)

![](img/01dbb13fe523592fe83664088e0fbfee_14.png)

以下是 `.cummax()` 的一个简单示例：

![](img/01dbb13fe523592fe83664088e0fbfee_16.png)

```python
import pandas as pd

# 创建一个示例序列
s = pd.Series([3, 1, 4, 1, 5])
print(s.cummax())
```
输出结果将是：`0 3, 1 3, 2 4, 3 4, 4 5`。这表示：
*   第0位（3）：历史最大值是3。
*   第1位（1）：历史最大值（3,1）是3。
*   第2位（4）：历史最大值（3,1,4）是4。
*   以此类推。

![](img/01dbb13fe523592fe83664088e0fbfee_18.png)

![](img/01dbb13fe523592fe83664088e0fbfee_20.png)

### 计算步骤分解

![](img/01dbb13fe523592fe83664088e0fbfee_22.png)

![](img/01dbb13fe523592fe83664088e0fbfee_24.png)

假设我们有一个代表每日资产净值的Pandas Series，名为 `net_value`。

![](img/01dbb13fe523592fe83664088e0fbfee_26.png)

以下是计算最大回撤的步骤：

![](img/01dbb13fe523592fe83664088e0fbfee_28.png)

![](img/01dbb13fe523592fe83664088e0fbfee_30.png)

1.  **计算历史最高值序列**：对净值序列使用 `.cummax()`，得到每个交易日对应的历史最高净值。
    ```python
    historical_max = net_value.cummax()
    ```

![](img/01dbb13fe523592fe83664088e0fbfee_32.png)

2.  **计算每日的回撤值**：用当天的历史最高净值减去当天的实际净值，得到每日的“潜在”回撤幅度（如果当天是低点）。
    ```python
    drawdown = historical_max - net_value
    ```

3.  **计算每日的回撤率**：将每日回撤幅度除以对应的历史最高净值，得到回撤比例。
    ```python
    drawdown_ratio = drawdown / historical_max
    ```

![](img/01dbb13fe523592fe83664088e0fbfee_34.png)

![](img/01dbb13fe523592fe83664088e0fbfee_36.png)

4.  **找出最大回撤率**：在所有每日回撤率中，最大值就是整个考察期内的**最大回撤**。
    ```python
    max_drawdown = drawdown_ratio.max()
    ```

![](img/01dbb13fe523592fe83664088e0fbfee_38.png)

### 完整代码示例

![](img/01dbb13fe523592fe83664088e0fbfee_40.png)

让我们将上述步骤整合，并应用于一段示例数据：

![](img/01dbb13fe523592fe83664088e0fbfee_42.png)

```python
import pandas as pd
import numpy as np

![](img/01dbb13fe523592fe83664088e0fbfee_44.png)

# 1. 创建示例数据：模拟一段有涨有跌的净值曲线
np.random.seed(42)
dates = pd.date_range(start=‘2023-01-01’, periods=100, freq=‘D’)
# 生成随机波动并带有轻微向上趋势的净值
net_value = 100 + np.cumsum(np.random.randn(100) * 0.5)
net_value_series = pd.Series(net_value, index=dates)

![](img/01dbb13fe523592fe83664088e0fbfee_46.png)

# 2. 计算历史最高值序列
historical_max = net_value_series.cummax()

![](img/01dbb13fe523592fe83664088e0fbfee_48.png)

# 3. 计算每日回撤幅度
drawdown = historical_max - net_value_series

![](img/01dbb13fe523592fe83664088e0fbfee_50.png)

# 4. 计算每日回撤率
drawdown_ratio = drawdown / historical_max

![](img/01dbb13fe523592fe83664088e0fbfee_52.png)

![](img/01dbb13fe523592fe83664088e0fbfee_54.png)

# 5. 找出最大回撤率及其发生日期
max_drawdown_ratio = drawdown_ratio.max()
max_drawdown_date = drawdown_ratio.idxmax()

# 6. 找出最大回撤开始日期（回撤开始时，即历史最高净值出现的日期）
# 我们需要找到在最大回撤发生日之前，净值等于历史最高值的那个点
peak_before_drawdown = historical_max.loc[max_drawdown_date]
start_date = net_value_series[net_value_series == peak_before_drawdown].index[0]

print(f“最大回撤率：{max_drawdown_ratio:.2%}”)
print(f“最大回撤期：从 {start_date.date()} 到 {max_drawdown_date.date()}”)
print(f“期初净值（高点）：{historical_max.loc[max_drawdown_date]:.2f}”)
print(f“期末净值（低点）：{net_value_series.loc[max_drawdown_date]:.2f}”)
```

![](img/01dbb13fe523592fe83664088e0fbfee_56.png)

运行以上代码，你将得到类似以下的输出，它清晰地指出了最大回撤发生的时段和幅度：
```
最大回撤率：12.34%
最大回撤期：从 2023-02-15 到 2023-03-10
期初净值（高点）：105.67
期末净值（低点）：92.64
```

![](img/01dbb13fe523592fe83664088e0fbfee_58.png)

## 总结

![](img/01dbb13fe523592fe83664088e0fbfee_60.png)

![](img/01dbb13fe523592fe83664088e0fbfee_62.png)

本节课中我们一起学习了**最大回撤**这个核心风险指标。
*   **它是什么**：衡量资产净值从历史高点下跌到之后低点的最大幅度，反映了投资可能面临的最糟糕情况。
*   **为什么重要**：它帮助投资者理解策略的波动风险和潜在的心理、资金压力，是风控管理的重要依据。
*   **如何计算**：我们利用Pandas的 `.cummax()` 函数高效地计算了历史最高序列，并通过公式 `(历史最高值 - 当前值) / 历史最高值` 求出了最大回撤率。

理解并计算最大回撤，是进行科学的量化策略评估和风险管理的第一步。在后续的课程中，我们将结合更多指标对策略进行综合评估。