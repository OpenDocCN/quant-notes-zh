# Python金融时间序列分析与量化交易实战教程：P15：14.最大回撤区间 📉

![](img/db15ece49069abbe4abf353e731a6f1b_0.png)

![](img/db15ece49069abbe4abf353e731a6f1b_2.png)

![](img/db15ece49069abbe4abf353e731a6f1b_4.png)

在本节课中，我们将要学习一个在量化交易中至关重要的风险指标——**最大回撤**。它用于衡量投资策略在最糟糕时期可能遭受的损失程度。

## 什么是最大回撤？

上一节我们介绍了策略的收益表现，本节中我们来看看如何评估策略的风险。最大回撤描述的是在投资过程中，资产净值从某个峰值到之后最低点的最大跌幅。它衡量的是投资者可能经历的“最惨”时期。

具体来说，它寻找的是在整个投资周期内，任意一个高点（`Pi`）到其后的任意一个低点（`Pj`）之间，净值缩水的最大比例。其计算公式如下：

![](img/db15ece49069abbe4abf353e731a6f1b_6.png)

![](img/db15ece49069abbe4abf353e731a6f1b_8.png)

**最大回撤 = max( (Pi - Pj) / Pi )**，其中 `i < j`

![](img/db15ece49069abbe4abf353e731a6f1b_10.png)

这个指标不直接评价策略的好坏，而是评估策略的风险。一个策略可能最终盈利，但如果在过程中经历了巨大的回撤，投资者可能因无法承受心理压力而过早退出，导致实际亏损。

![](img/db15ece49069abbe4abf353e731a6f1b_12.png)

## 如何计算最大回撤？

![](img/db15ece49069abbe4abf353e731a6f1b_14.png)

![](img/db15ece49069abbe4abf353e731a6f1b_16.png)

理解了概念后，我们来看看如何用Python代码实现最大回撤的计算。核心思路是遍历所有可能的“高点-低点”组合，并找出其中跌幅最大的一个。

![](img/db15ece49069abbe4abf353e731a6f1b_18.png)

![](img/db15ece49069abbe4abf353e731a6f1b_20.png)

![](img/db15ece49069abbe4abf353e731a6f1b_22.png)

以下是计算最大回撤的步骤：

![](img/db15ece49069abbe4abf353e731a6f1b_24.png)

![](img/db15ece49069abbe4abf353e731a6f1b_26.png)

![](img/db15ece49069abbe4abf353e731a6f1b_28.png)

1.  **计算历史最高点序列**：对于时间序列中的每一个点，计算到该点为止的历史最高净值。
2.  **计算回撤序列**：用每个点的历史最高值减去该点的实际净值，得到每个时刻的“潜在”回撤值。
3.  **计算回撤率序列**：将每个时刻的“潜在”回撤值除以其对应的历史最高值，得到回撤率。
4.  **找出最大值**：在所有回撤率中找出最大值，即为最大回撤。

![](img/db15ece49069abbe4abf353e731a6f1b_30.png)

![](img/db15ece49069abbe4abf353e731a6f1b_32.png)

![](img/db15ece49069abbe4abf353e731a6f1b_34.png)

我们可以使用Pandas库的 `cummax()` 函数来高效地完成第一步。`cummax()` 函数会返回序列的累积最大值。

![](img/db15ece49069abbe4abf353e731a6f1b_36.png)

![](img/db15ece49069abbe4abf353e731a6f1b_38.png)

![](img/db15ece49069abbe4abf353e731a6f1b_40.png)

```python
import pandas as pd

![](img/db15ece49069abbe4abf353e731a6f1b_42.png)

![](img/db15ece49069abbe4abf353e731a6f1b_44.png)

![](img/db15ece49069abbe4abf353e731a6f1b_46.png)

![](img/db15ece49069abbe4abf353e731a6f1b_48.png)

# 假设 df[‘net_value’] 是我们的资产净值时间序列
# 1. 计算历史最高点序列
historical_max = df[‘net_value’].cummax()

![](img/db15ece49069abbe4abf353e731a6f1b_50.png)

![](img/db15ece49069abbe4abf353e731a6f1b_52.png)

![](img/db15ece49069abbe4abf353e731a6f1b_54.png)

# 2. 计算每日的回撤（历史最高 - 当前净值）
drawdown = historical_max - df[‘net_value’]

![](img/db15ece49069abbe4abf353e731a6f1b_56.png)

![](img/db15ece49069abbe4abf353e731a6f1b_58.png)

![](img/db15ece49069abbe4abf353e731a6f1b_60.png)

# 3. 计算每日的回撤率
drawdown_pct = drawdown / historical_max

![](img/db15ece49069abbe4abf353e731a6f1b_62.png)

![](img/db15ece49069abbe4abf353e731a6f1b_64.png)

![](img/db15ece49069abbe4abf353e731a6f1b_66.png)

# 4. 找出最大回撤率
max_drawdown = drawdown_pct.max()
print(f“最大回撤为：{max_drawdown:.2%}”)
```

![](img/db15ece49069abbe4abf353e731a6f1b_68.png)

![](img/db15ece49069abbe4abf353e731a6f1b_70.png)

![](img/db15ece49069abbe4abf353e731a6f1b_72.png)

## 最大回撤区间的意义

![](img/db15ece49069abbe4abf353e731a6f1b_74.png)

![](img/db15ece49069abbe4abf353e731a6f1b_76.png)

![](img/db15ece49069abbe4abf353e731a6f1b_78.png)

计算出的最大回撤率是一个重要的数字，但它背后对应的时间区间同样关键。这个区间就是“最大回撤区间”，它告诉我们在哪一段时间内，策略经历了最严峻的考验。

![](img/db15ece49069abbe4abf353e731a6f1b_80.png)

![](img/db15ece49069abbe4abf353e731a6f1b_82.png)

![](img/db15ece49069abbe4abf353e731a6f1b_84.png)

![](img/db15ece49069abbe4abf353e731a6f1b_86.png)

在策略回测报告中，我们不仅能看到最大回撤的数值（例如3.07%），还能看到这个回撤发生的起止日期。分析这个区间内的市场环境、策略信号和行为，有助于我们理解策略的弱点，并进行针对性的优化。

![](img/db15ece49069abbe4abf353e731a6f1b_88.png)

## 总结

![](img/db15ece49069abbe4abf353e731a6f1b_90.png)

![](img/db15ece49069abbe4abf353e731a6f1b_92.png)

![](img/db15ece49069abbe4abf353e731a6f1b_94.png)

本节课中我们一起学习了**最大回撤**这一核心风险指标。我们了解到：
*   最大回撤衡量的是投资过程中可能出现的最大亏损幅度，反映了策略的“惨劲儿”。
*   它是一个关键的风险评估工具，帮助投资者了解策略的潜在压力。
*   我们掌握了使用Pandas的 `cummax()` 函数计算最大回撤的代码方法。
*   最大回撤对应的**时间区间**对于策略分析和优化具有重要参考价值。

![](img/db15ece49069abbe4abf353e731a6f1b_96.png)

![](img/db15ece49069abbe4abf353e731a6f1b_98.png)

理解并监控最大回撤，对于管理投资风险、保持投资纪律至关重要。在后续的策略评估中，我们需要在收益和回撤之间寻找平衡。