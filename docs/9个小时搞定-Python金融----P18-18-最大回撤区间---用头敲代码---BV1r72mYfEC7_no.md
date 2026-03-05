# Python金融量化分析：P18：最大回撤区间 📉

![](img/f4bce5140e38e9b7149578abcff91a13_1.png)

在本节课中，我们将学习金融量化分析中的一个重要风险指标——**最大回撤**。我们将理解其概念、计算方式，并通过Python代码演示如何计算它。

![](img/f4bce5140e38e9b7149578abcff91a13_3.png)

## 概述

在策略分析中，我们不仅关注收益，也关注风险。最大回撤就是衡量投资策略风险的关键指标之一。它描述的是在特定时期内，资产净值从最高点到之后最低点的最大跌幅，直观反映了投资者可能经历的最大“惨劲儿”。

上一节我们介绍了策略分析的基本框架，本节中我们来看看如何量化评估策略的风险。

## 什么是最大回撤？

最大回撤不能仅从字面理解。它描述的是在投资过程中，资产价值下跌最严重、持续时间最长的那段区间。例如，在炒股时，价格会有涨跌，而最大回撤区间就是价格下跌最惨烈的那段时期。

其核心是衡量资产缩水的程度，公式如下：

**最大回撤 = max( (P_i - P_j) / P_i )**

其中：
*   `P_i` 代表第 `i` 天的资产价格（或净值）。
*   `P_j` 代表第 `i` 天之后某一天 `j` 的资产价格。
*   我们需要遍历所有可能的 `i` 和 `j`（`j > i`），找出使 `(P_i - P_j) / P_i` 这个比值最大的组合。这个比值代表了从高点 `i` 到低点 `j` 的**相对跌幅**。

![](img/f4bce5140e38e9b7149578abcff91a13_5.png)

这个指标评估的不是策略的绝对好坏，而是其风险。即使一个策略最终盈利，但如果在过程中出现了巨大的回撤，也可能因为投资者无法承受心理压力而在低点止损退出。例如，2008年股市从高点大幅下跌，就让许多投资者承受了巨大损失。

![](img/f4bce5140e38e9b7149578abcff91a13_7.png)

![](img/f4bce5140e38e9b7149578abcff91a13_9.png)

## 如何计算最大回撤？

![](img/f4bce5140e38e9b7149578abcff91a13_11.png)

在Python中，我们可以借助Pandas库高效地计算最大回撤。核心思路是：对于每一天，计算当日价格与**该日之前历史最高价**的差值，然后找出所有差值中相对跌幅最大的那个。

以下是计算步骤的分解：

![](img/f4bce5140e38e9b7149578abcff91a13_13.png)

![](img/f4bce5140e38e9b7149578abcff91a13_15.png)

首先，我们需要一个价格序列。这里使用Pandas的`Series`作为示例。

![](img/f4bce5140e38e9b7149578abcff91a13_17.png)

```python
import pandas as pd

![](img/f4bce5140e38e9b7149578abcff91a13_19.png)

# 假设这是我们的资产净值序列 (例如收盘价)
price_series = pd.Series([2913, 2925, 2938, 2934, 2950, 2940])
```

![](img/f4bce5140e38e9b7149578abcff91a13_21.png)

第一步，计算**历史累计最大值**。即对于序列中的每一个位置，计算从开始到该位置的最大值。

![](img/f4bce5140e38e9b7149578abcff91a13_23.png)

```python
# 计算累积最大值
cumulative_max = price_series.cummax()
print(cumulative_max)
```
输出会是：`2913, 2925, 2938, 2938, 2950, 2950`。可以看到，第四天的值依然是2938，因为前三天的最大值是2938。

![](img/f4bce5140e38e9b7149578abcff91a13_25.png)

![](img/f4bce5140e38e9b7149578abcff91a13_27.png)

第二步，用每一天的**历史累计最大值**减去**当天的实际价格**。这得到了每一天相对于其之前高点的“潜在下跌额”。

![](img/f4bce5140e38e9b7149578abcff91a13_29.png)

![](img/f4bce5140e38e9b7149578abcff91a13_31.png)

```python
drawdown_value = cumulative_max - price_series
print(drawdown_value)
```

![](img/f4bce5140e38e9b7149578abcff91a13_33.png)

第三步，计算**回撤率**。用上一步的差值除以对应的历史累计最大值，得到相对跌幅。

![](img/f4bce5140e38e9b7149578abcff91a13_35.png)

```python
drawdown_ratio = (cumulative_max - price_series) / cumulative_max
print(drawdown_ratio)
```

![](img/f4bce5140e38e9b7149578abcff91a13_37.png)

第四步，从所有回撤率中找出最大值，这就是**最大回撤**。

![](img/f4bce5140e38e9b7149578abcff91a13_39.png)

![](img/f4bce5140e38e9b7149578abcff91a13_41.png)

```python
max_drawdown = drawdown_ratio.max()
print(f"最大回撤为：{max_drawdown:.2%}")
```

将以上步骤整合，计算最大回撤的函数可以简洁地写为：

![](img/f4bce5140e38e9b7149578abcff91a13_43.png)

```python
def calculate_max_drawdown(price_series):
    """
    计算价格序列的最大回撤。
    参数:
        price_series: pandas Series，资产价格或净值序列。
    返回:
        最大回撤值 (小数形式，例如0.05表示5%)
    """
    cumulative_max = price_series.cummax()
    drawdown_ratio = (cumulative_max - price_series) / cumulative_max
    return drawdown_ratio.max()

![](img/f4bce5140e38e9b7149578abcff91a13_45.png)

# 使用函数
max_dd = calculate_max_drawdown(price_series)
print(f"最大回撤为：{max_dd:.2%}")
```

在实际的策略回测中（例如使用`backtrader`等框架），最大回撤区间会被自动统计并可视化。图表中通常会用一个阴影区域标出资产曲线中最大回撤发生的起止时间和幅度。

![](img/f4bce5140e38e9b7149578abcff91a13_47.png)

## 总结

![](img/f4bce5140e38e9b7149578abcff91a13_49.png)

![](img/f4bce5140e38e9b7149578abcff91a13_51.png)

本节课中我们一起学习了最大回撤这个重要的风险指标。
*   **概念**：最大回撤衡量了投资过程中资产价值从高峰到低谷的最大相对跌幅，反映了策略可能面临的最大风险。
*   **计算**：其核心公式是 **`max( (P_i - P_j) / P_i )`**，我们利用Pandas的`.cummax()`方法可以高效地计算出来。
*   **意义**：它帮助投资者了解策略的波动性和潜在的最大亏损，对于风险管理和投资决策至关重要。

![](img/f4bce5140e38e9b7149578abcff91a13_53.png)

![](img/f4bce5140e38e9b7149578abcff91a13_55.png)

理解并监控最大回撤，能让你更全面地评估一个策略，不仅知道它“能赚多少”，也清楚它“最多会亏多少”。