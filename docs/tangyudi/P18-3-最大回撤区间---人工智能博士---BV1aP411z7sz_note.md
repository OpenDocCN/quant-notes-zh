# 量化投资基础：P18：最大回撤区间 📉

![](img/4b6df91fc644cf80df4ebfe825be728d_1.png)

在本节课中，我们将要学习一个重要的风险指标——最大回撤。它描述的是在投资过程中，资产净值从最高点到之后最低点的最大跌幅，直观地反映了投资者可能经历的最糟糕时期。

![](img/4b6df91fc644cf80df4ebfe825be728d_3.png)

## 什么是最大回撤？📊

上一节我们介绍了策略分析的基本概念，本节中我们来看看如何衡量投资风险。最大回撤不能仅从字面理解，它描述的是投资过程中“最惨”的阶段。

具体来说，在炒股过程中，价格必然有涨有跌。最大回撤指的就是从某个高点开始，到后续某个低点为止，资产价值下跌最严重、持续时间最长的那段区间。它衡量的是你资产“缩水”的程度。

![](img/4b6df91fc644cf80df4ebfe825be728d_1.png)

这个指标的核心在于评估风险，而非单纯判断策略好坏。一个策略可能最终盈利，但中间如果存在巨大的回撤，意味着投资者需要承受极大的心理压力和资金风险。例如，2008年股市从高点暴跌，就体现了巨大的回撤风险。

## 最大回撤的计算公式 🧮

理解了概念后，我们来看看如何用数学语言描述它。最大回撤的计算涉及寻找历史数据中跌幅最大的区间。

其核心公式可以表示为：

**最大回撤 = max\[ (Pi - Pj) / Pi ]**，其中 **i < j**

这里：
*   **Pi** 代表时间点 i 的资产价格（或净值）。
*   **Pj** 代表时间点 i 之后某个时间点 j 的资产价格。
*   公式的含义是：遍历所有可能的 i 和 j (i < j)，计算从 i 点到 j 点的相对跌幅 **(Pi - Pj) / Pi**，然后取其中的最大值。

![](img/4b6df91fc644cf80df4ebfe825be728d_5.png)

![](img/4b6df91fc644cf80df4ebfe825be728d_3.png)

![](img/4b6df91fc644cf80df4ebfe825be728d_7.png)

这个公式计算的就是那个“最惨”阶段的资产缩水比例。

## 使用Python计算最大回撤 💻

![](img/4b6df91fc644cf80df4ebfe825be728d_9.png)

理论需要实践来巩固。接下来，我们使用Python的Pandas库来演示如何计算最大回撤。我们将通过一个简单的序列操作来理解这个过程。

![](img/4b6df91fc644cf80df4ebfe825be728d_11.png)

![](img/4b6df91fc644cf80df4ebfe825be728d_13.png)

首先，我们需要计算到每个时间点为止的历史最高值。这可以通过 `cummax()` 函数实现。

```python
import pandas as pd

![](img/4b6df91fc644cf80df4ebfe825be728d_15.png)

# 假设 df[‘close’] 是每日收盘价序列
historical_max = df[‘close’].cummax()
```

![](img/4b6df91fc644cf80df4ebfe825be728d_17.png)

![](img/4b6df91fc644cf80df4ebfe825be728d_19.png)

`historical_max` 序列的每个值，都代表从起始日到当前日的最高收盘价。

![](img/4b6df91fc644cf80df4ebfe825be728d_21.png)

接着，我们计算每日的“潜在回撤”，即当日历史最高价与当日实际收盘价的差值。

![](img/4b6df91fc644cf80df4ebfe825be728d_23.png)

![](img/4b6df91fc644cf80df4ebfe825be728d_25.png)

```python
drawdown = historical_max - df[‘close’]
```

![](img/4b6df91fc644cf80df4ebfe825be728d_27.png)

然后，我们计算每日的回撤率，即回撤值除以对应的历史最高价。

![](img/4b6df91fc644cf80df4ebfe825be728d_29.png)

```python
drawdown_pct = drawdown / historical_max
```

![](img/4b6df91fc644cf80df4ebfe825be728d_31.png)

最后，最大回撤就是这个回撤率序列中的最大值。

![](img/4b6df91fc644cf80df4ebfe825be728d_33.png)

```python
max_drawdown = drawdown_pct.max()
```

![](img/4b6df91fc644cf80df4ebfe825be728d_35.png)

![](img/4b6df91fc644cf80df4ebfe825be728d_47.png)

![](img/4b6df91fc644cf80df4ebfe825be728d_37.png)

上述步骤可以合并为一行的Pandas链式操作：

```python
max_drawdown = (df[‘close’].cummax() - df[‘close’]).div(df[‘close’].cummax()).max()
```

![](img/4b6df91fc644cf80df4ebfe825be728d_39.png)

![](img/4b6df91fc644cf80df4ebfe825be728d_41.png)

执行这段代码，我们就能得到该资产在这段时间内的最大回撤值，例如 `0.0307` 表示最大回撤为 **3.07%**。

## 总结 📝

本节课中我们一起学习了最大回撤这个关键的风险评估指标。

![](img/4b6df91fc644cf80df4ebfe825be728d_43.png)

我们首先明确了最大回撤描述的是投资过程中资产净值从峰值到谷底的最大跌幅，反映了投资者可能面临的最大阶段性损失。然后，我们学习了其计算公式 **max\[ (Pi - Pj) / Pi ]**。最后，我们使用Python的Pandas库，通过计算历史最高序列 (`cummax()`)、回撤序列和回撤率序列，最终求得最大回撤值。

![](img/4b6df91fc644cf80df4ebfe825be728d_45.png)

![](img/4b6df91fc644cf80df4ebfe825be728d_47.png)

理解并监控最大回撤，对于管理投资风险、评估策略稳健性至关重要。它提醒我们，不仅要关注最终收益，更要关注通往收益道路上的波折与风险。

![](img/4b6df91fc644cf80df4ebfe825be728d_49.png)

![](img/4b6df91fc644cf80df4ebfe825be728d_49.png)