# Python金融分析量化交易：P18：3-最大回撤区间 📉

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_1.png)

在本节课中，我们将要学习一个重要的风险指标——最大回撤。最大回撤用于衡量投资策略在特定时间段内可能面临的最糟糕情况，即资产净值从峰值到谷底的最大跌幅。理解并计算最大回撤对于评估投资策略的风险至关重要。

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_3.png)

## 什么是最大回撤？

上一节我们介绍了策略回测的基本概念，本节中我们来看看如何量化策略的风险。最大回撤描述的是在投资过程中，资产价值从历史最高点下跌到最低点的最大幅度。它反映了投资者可能经历的最大账面亏损。

最大回撤的计算公式如下：

**最大回撤 = max( (Pi - Pj) / Pi )**

其中：
*   **Pi** 代表第 i 天的资产价格（或净值）。
*   **Pj** 代表第 i 天之后的某一天（第 j 天）的资产价格。
*   我们需要遍历所有可能的 i 和 j（j > i），找出使 `(Pi - Pj) / Pi` 这个比值最大的组合。这个比值代表了从高点 Pi 到低点 Pj 的缩水比例。

这个指标的核心是衡量“惨劲儿”，即投资过程中最痛苦的持续下跌阶段。即使一个策略最终盈利，但过程中如果存在巨大的回撤，也可能因为投资者无法承受心理压力而提前止损，导致实际亏损。例如，2008年全球金融危机期间，股市从高点大幅下挫，许多投资者经历了巨大的回撤。

## 如何计算最大回撤？

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_5.png)

理解了最大回撤的含义后，我们来看看如何用Python进行计算。我们将使用Pandas库来高效地处理时间序列数据。

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_7.png)

计算最大回撤的关键在于，对于每一天，我们都需要知道在此之前（包括当天）的历史最高价，然后计算当前价格与这个历史最高价的差值比例。

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_9.png)

以下是计算最大回撤的核心步骤：

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_11.png)

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_13.png)

1.  **计算历史最高点序列**：使用 `cummax()` 函数。这个函数返回截至当前行的最大值。
    ```python
    # 假设 ‘close’ 是每日收盘价序列
    historical_max = df[‘close’].cummax()
    ```
    这行代码会生成一个新序列，其中每个值都是到该日期为止的收盘价最大值。

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_15.png)

2.  **计算每日回撤**：用历史最高点减去当日收盘价，再除以历史最高点。
    ```python
    daily_drawdown = (historical_max - df[‘close’]) / historical_max
    ```
    这样就得到了每日相对于历史最高点的回撤比例。

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_17.png)

3.  **找出最大回撤**：从每日回撤序列中找出最大值。
    ```python
    max_drawdown = daily_drawdown.max()
    ```
    这个值就是整个回测区间内的最大回撤。

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_19.png)

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_21.png)

让我们通过一个简化的代码示例来演示这个过程：

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_23.png)

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_25.png)

```python
import pandas as pd
import numpy as np

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_27.png)

# 创建一个示例的股价序列
data = {‘date’: pd.date_range(start=‘2023-01-01’, periods=10, freq=‘D’),
        ‘close’: [100, 102, 98, 105, 103, 101, 96, 94, 97, 99]}
df = pd.DataFrame(data).set_index(‘date’)

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_29.png)

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_31.png)

# 计算历史最高点序列
df[‘historical_max’] = df[‘close’].cummax()

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_33.png)

# 计算每日回撤
df[‘daily_drawdown’] = (df[‘historical_max’] - df[‘close’]) / df[‘historical_max’]

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_35.png)

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_37.png)

# 计算最大回撤
max_drawdown = df[‘daily_drawdown’].max()
print(f“最大回撤为：{max_drawdown:.2%}”)

# 找到最大回撤发生的区间（起始点和结束点）
max_dd_index = df[‘daily_drawdown’].idxmax() # 回撤最大的那一天（谷底日）
# 找到在谷底日之前，股价等于历史最高点的日期（峰值日）
peak_index = df.loc[:max_dd_index, ‘close’].idxmax()
print(f“最大回撤区间：从 {peak_index.date()} 到 {max_dd_index.date()}”)
```

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_39.png)

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_41.png)

## 最大回撤区间的可视化

计算出的最大回撤值对应着图表中的一个特定区间。在回测结果图中，这个区间通常会被高亮显示（例如用阴影标出），直观地展示资产净值从峰值跌至谷底的过程。

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_43.png)

这个区间告诉你，在你的策略执行过程中，最糟糕的连续下跌发生在什么时候，持续了多久，以及跌幅有多深。分析这个区间对应的市场环境或策略信号，有助于你优化策略以控制风险。

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_45.png)

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_47.png)

## 总结

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_49.png)

![](img/86b2d2f1a7b669f4a8ff268e4343c0cd_51.png)

本节课中我们一起学习了最大回撤这一核心风险指标。我们首先明确了最大回撤的定义，它衡量的是投资过程中可能出现的最大账面亏损幅度。接着，我们分解了其计算公式，并重点学习了使用Pandas的 `cummax()` 函数来计算历史最高序列，进而推导出最大回撤的Python实现方法。最后，我们了解到最大回撤在回测图表中对应着一个具体的下跌区间。掌握最大回撤的计算与分析，是评估和优化量化交易策略风险的重要一环。