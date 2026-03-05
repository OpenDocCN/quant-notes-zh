# 技术分析：P1：Python自动检测支撑与阻力水平 📈

![](img/dc08c9c7ac2a86b89062a07036e015d6_1.png)

在本教程中，我们将学习如何使用Python自动检测金融图表中的支撑位和阻力位。这是一个由社区评论启发的实用想法。我们将从理解核心概念开始，逐步构建一个算法，并用代码实现它。本教程附带一个Jupyter Notebook文件，您可以通过描述中的链接下载并进行实验。

![](img/dc08c9c7ac2a86b89062a07036e015d6_3.png)

## 概述

支撑位和阻力位是技术分析中的基本概念。支撑位是价格下跌时可能遇到买盘从而止跌回升的水平；阻力位则是价格上涨时可能遇到卖盘从而止涨回落的水平。虽然人眼观察图表时很容易识别这些水平，但用程序自动化检测则需要一个明确的算法。本节我们将介绍一种相对简单且易于实现的检测方法。

![](img/dc08c9c7ac2a86b89062a07036e015d6_5.png)

## 算法原理

上一节我们概述了目标，本节中我们来看看具体的算法逻辑。我们的核心思路是：通过分析特定蜡烛（K线）前后价格高点和低点的排列模式来识别支撑和阻力。

![](img/dc08c9c7ac2a86b89062a07036e015d6_7.png)

*   **对于支撑位**：我们需要在目标蜡烛`L`之前，找到`N1`个**连续下降**的低点；在目标蜡烛`L`之后，找到`N2`个**连续上升**的低点。当这种模式成立时，蜡烛`L`的最低点就被定义为一个支撑位。
    *   公式化描述：`Low[L-N1] > Low[L-N1+1] > ... > Low[L]` 且 `Low[L] < Low[L+1] < ... < Low[L+N2]`
*   **对于阻力位**：我们需要在目标蜡烛`L`之前，找到`N1`个**连续上升**的高点；在目标蜡烛`L`之后，找到`N2`个**连续下降**的高点。当这种模式成立时，蜡烛`L`的最高点就被定义为一个阻力位。
    *   公式化描述：`High[L-N1] < High[L-N1+1] < ... < High[L]` 且 `High[L] > High[L+1] > ... > High[L+N2]`

参数`N1`和`N2`（即观察窗口大小）可以由用户调整。较小的值会使算法更敏感，检测到更多水平；较大的值则更严格，检测到的水平更少但可能更关键。

## 代码实现

理解了算法原理后，我们现在进入代码实现部分。我们将使用Python的Pandas库进行数据处理。

首先，导入必要的库并加载数据。这里我们使用EUR/USD的日线数据作为示例。

```python
import pandas as pd

# 加载数据，假设数据包含‘Open‘, ‘High‘, ‘Low‘, ‘Close‘, ‘Volume‘列
df = pd.read_csv(‘eurusd_daily.csv‘)
# 清理数据：过滤掉成交量为零（市场关闭）的日子
df_clean = df[df[‘Volume‘] != 0].copy()
print(f“原始数据行数: {len(df)}， 清理后行数: {len(df_clean)}“)
```

接下来，我们定义检测支撑位和阻力位的核心函数。

```python
def is_support(df, i, n1, n2):
    """
    判断第i根蜡烛是否构成支撑位。
    :param df: 价格DataFrame
    :param i: 目标蜡烛的索引
    :param n1: 向前看的蜡烛数
    :param n2: 向后看的蜡烛数
    :return: 1 如果是支撑位，否则 0
    """
    # 检查前n1根蜡烛的低点是否依次下降
    for j in range(i - n1 + 1, i + 1):
        if df[‘Low‘].iloc[j] >= df[‘Low‘].iloc[j - 1]:
            return 0
    # 检查后n2根蜡烛的低点是否依次上升
    for j in range(i + 1, i + n2 + 1):
        if df[‘Low‘].iloc[j] <= df[‘Low‘].iloc[j - 1]:
            return 0
    return 1

def is_resistance(df, i, n1, n2):
    """
    判断第i根蜡烛是否构成阻力位。
    :param df: 价格DataFrame
    :param i: 目标蜡烛的索引
    :param n1: 向前看的蜡烛数
    :param n2: 向后看的蜡烛数
    :return: 1 如果是阻力位，否则 0
    """
    # 检查前n1根蜡烛的高点是否依次上升
    for j in range(i - n1 + 1, i + 1):
        if df[‘High‘].iloc[j] <= df[‘High‘].iloc[j - 1]:
            return 0
    # 检查后n2根蜡烛的高点是否依次下降
    for j in range(i + 1, i + n2 + 1):
        if df[‘High‘].iloc[j] >= df[‘High‘].iloc[j - 1]:
            return 0
    return 1
```

现在，我们需要遍历一段历史数据，应用这两个函数来找出所有的支撑位和阻力位。

```python
def detect_levels(df, lookback_period=200, n1=3, n2=3):
    """
    在指定的回顾期内检测支撑位和阻力位。
    :param df: 价格DataFrame
    :param lookback_period: 回顾的蜡烛数量
    :param n1: 算法参数N1
    :param n2: 算法参数N2
    :return: 支撑位列表，阻力位列表 (每个元素为(蜡烛索引, 价格))
    """
    support_levels = []
    resistance_levels = []
    # 确定检测的起始索引，确保有足够的前置数据
    start_idx = max(n1, lookback_period)
    
    for i in range(start_idx, len(df)):
        # 只检测最近lookback_period根蜡烛
        if i < len(df) - n2: # 确保有足够的后置数据
            if is_support(df, i, n1, n2):
                support_levels.append((i, df[‘Low‘].iloc[i]))
            if is_resistance(df, i, n1, n2):
                resistance_levels.append((i, df[‘High‘].iloc[i]))
    return support_levels, resistance_levels

# 使用函数进行检测
support_levels, resistance_levels = detect_levels(df_clean, lookback_period=200, n1=3, n2=3)
print(f“检测到 {len(support_levels)} 个支撑位， {len(resistance_levels)} 个阻力位。“)
```

## 结果可视化与优化

检测到水平后，我们需要将其可视化。同时，由于算法可能检测到非常接近的多个水平，我们需要进行合并以使图表更清晰。

以下是绘图和合并相近水平的步骤：

1.  **合并相近水平**：为了避免在几乎同一价格画出多条线，我们将距离过近的水平合并。
    ```python
    def merge_nearby_levels(levels, min_distance=0.005):
        """
        合并价格过于接近的水平。
        :param levels: 水平列表，格式为[(index, price), ...]
        :param min_distance: 最小距离阈值（价格比例）
        :return: 合并后的水平列表
        """
        if not levels:
            return []
        # 按价格排序
        sorted_levels = sorted(levels, key=lambda x: x[1])
        merged = [sorted_levels[0]]
        for current in sorted_levels[1:]:
            last = merged[-1]
            # 如果当前价格与最后一个合并价格的距离小于阈值，则合并（取均值）
            if abs(current[1] - last[1]) / last[1] < min_distance:
                # 合并策略：取价格平均值，索引取第一个（或可自定义）
                avg_price = (last[1] + current[1]) / 2
                merged[-1] = (last[0], avg_price)
            else:
                merged.append(current)
        return merged
    
    merged_support = merge_nearby_levels(support_levels)
    merged_resistance = merge_nearby_levels(resistance_levels)
    ```
2.  **绘制图表**：使用`plotly`或`matplotlib`绘制K线图并叠加支撑阻力线。
    ```python
    import plotly.graph_objects as go
    
    # 假设我们绘制最后300根蜡烛
    plot_df = df_clean.iloc[-300:].copy()
    fig = go.Figure(data=[go.Candlestick(x=plot_df.index,
                                         open=plot_df[‘Open‘],
                                         high=plot_df[‘High‘],
                                         low=plot_df[‘Low‘],
                                         close=plot_df[‘Close‘])])
    
    # 添加支撑线（紫色）
    for idx, price in merged_support:
        if idx in plot_df.index: # 确保该水平在绘图区间内
            fig.add_shape(type=“line“,
                          x0=idx-10, x1=idx+10, # 让线在检测点附近显示，而非贯穿全图
                          y0=price, y1=price,
                          line=dict(color=“Purple“, width=2, dash=“dash“))
    
    # 添加阻力线（蓝色）
    for idx, price in merged_resistance:
        if idx in plot_df.index:
            fig.add_shape(type=“line“,
                          x0=idx-10, x1=idx+10,
                          y0=price, y1=price,
                          line=dict(color=“Blue“, width=2, dash=“dash“))
    
    fig.update_layout(title=“支撑与阻力水平检测“,
                      xaxis_title=“Candle Index“,
                      yaxis_title=“Price“,
                      showlegend=False)
    fig.show()
    ```

## 参数调整与注意事项

我们的算法效果很大程度上依赖于参数`N1`、`N2`以及合并阈值`min_distance`。

以下是关于参数调整的一些指导：
*   **`N1`和`N2`**：减小这些值会使算法对短期波动更敏感，检测出更多水平，但可能包含一些不重要的噪音。增大这些值则使条件更严格，只识别出更显著、更持久的水平。建议从`(3,3)`开始尝试，并根据交易品种和时间框架进行调整。
*   **`min_distance`**：这个参数决定了多近的水平会被合并。增大该值会合并更多水平，让图表更简洁；减小该值则会保留更多细节。您可以根据价格波动的平均幅度来设置。
*   **算法局限性**：本算法是一种基于局部价格序列模式的检测方法。它可能会错过一些通过其他方式（如历史价格反复测试）形成的重要水平。它最适合作为辅助工具，而非唯一的决策依据。

## 总结

![](img/dc08c9c7ac2a86b89062a07036e015d6_9.png)

本节课中我们一起学习了如何使用Python自动检测图表中的支撑位和阻力位。我们从定义核心的检测算法开始，该算法通过寻找特定价格排列模式（连续的高点或低点）来识别关键水平。随后，我们实现了检测函数，遍历历史数据找出这些水平，并学习了如何合并过于接近的水平以使可视化结果更清晰。最后，我们讨论了关键参数的影响及算法的局限性。您可以通过调整`N1`、`N2`和合并阈值来使算法适应不同的市场环境与交易策略。请记住，这是一个基础工具，在实际应用中应结合其他分析方法共同使用。