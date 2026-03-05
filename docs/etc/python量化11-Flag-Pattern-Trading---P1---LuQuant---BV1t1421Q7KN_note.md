# Python量化11：1：旗形模式交易 - 第一部分

![](img/806981b8ac7090d0856fba389fee94a8_0.png)

![](img/806981b8ac7090d0856fba389fee94a8_2.png)

## 概述
在本节课中，我们将学习如何使用Python自动检测金融市场图表中的旗形模式。我们将从理解旗形模式的基本概念开始，然后逐步构建一个算法来识别它，最后用代码实现这个检测过程。

## 旗形模式简介
在交易中，当价格经历一段强劲的上涨或下跌后，通常会进入一个短暂的盘整期。这个盘整期的形态如果其高点和低点能够分别沿着两条收敛的趋势线对齐，看起来就像一面旗帜，因此被称为“旗形模式”。

具体而言，如果高点大致沿递减的斜率对齐，而低点大致沿递增的斜率对齐，就形成了一个类似三角形的旗形区域。这种模式很重要，因为它通常预示着价格将延续之前的趋势。

价格可能有两种后续走势：
1.  价格继续在三角形旗形内部盘整。交易者可以利用这个狭窄的区间进行快速的区间交易。
2.  价格向上或向下突破旗形区域。突破后，价格可能会回踩支撑或阻力位，然后延续原趋势。这为趋势交易提供了机会。

![](img/806981b8ac7090d0856fba389fee94a8_4.png)

## 算法核心思路
我们将使用Python语言自动检测此模式。算法的核心步骤如下：
1.  **检测枢轴点**：识别图表中的局部高点和低点。
2.  **拟合趋势线**：尝试将识别出的高点拟合到一条下降的直线，将低点拟合到一条上升的直线。
3.  **评估拟合质量**：检查这两条线的拟合优度（R²值）是否足够高，以确认点确实沿趋势线对齐。
4.  **确认模式**：如果高点和低点交替出现，且两条趋势线的拟合效果都很好，同时高点趋势线斜率为负（下降），低点趋势线斜率为正（上升），则判定检测到旗形模式。

接下来，让我们进入编码部分，看看如何具体实现。

![](img/806981b8ac7090d0856fba389fee94a8_6.png)

## 数据准备与预处理
首先，我们需要加载并准备用于分析的价格数据。

```python
import pandas as pd

![](img/806981b8ac7090d0856fba389fee94a8_8.png)

# 从CSV文件加载数据，这里以欧元/美元1小时数据为例
df = pd.read_csv('eurusd_1h_bid_2020_2023.csv')
# 数据列通常包括：开盘时间、最高价、最低价、收盘价、成交量

# 清理数据：移除成交量为零的蜡烛图（例如周末休市时段）
df = df[df[‘Volume’] > 0]

# 重置索引
df = df.reset_index(drop=True)

# 检查是否存在缺失值
print(df.isnull().sum())

# 查看数据前10行
print(df.head(10))
```

![](img/806981b8ac7090d0856fba389fee94a8_10.png)

## 第一步：检测枢轴点
枢轴点是价格图表中的局部转折点。我们定义一个函数来判断某根K线是否为枢轴高点或低点。

![](img/806981b8ac7090d0856fba389fee94a8_12.png)

**核心逻辑**：如果一根K线的价格高于其前后N根邻居K线的价格，则它是一个枢轴高点；如果低于其前后N根邻居K线的价格，则是一个枢轴低点。

```python
def pivot_id(ohlc, n1, n2):
    """
    判断给定索引的K线是否为枢轴点。
    :param ohlc: 包含价格数据的DataFrame
    :param n1: 向前看的邻居数量
    :param n2: 向后看的邻居数量
    :return: 1表示枢轴低点，2表示枢轴高点，0表示都不是
    """
    # 获取当前索引
    i = ohlc.index[n2]
    
    # 检查是否为枢轴高点：当前最高价 > 前后邻居的最高价
    high_condition = (ohlc.loc[i, ‘High’] > ohlc.loc[i-n1:i-1, ‘High’].max()) and \
                     (ohlc.loc[i, ‘High’] > ohlc.loc[i+1:i+n2, ‘High’].max())
    
    # 检查是否为枢轴低点：当前最低价 < 前后邻居的最低价
    low_condition = (ohlc.loc[i, ‘Low’] < ohlc.loc[i-n1:i-1, ‘Low’].min()) and \
                    (ohlc.loc[i, ‘Low’] < ohlc.loc[i+1:i+n2, ‘Low’].min())
    
    if high_condition and low_condition:
        return 3 # 理论上不应同时成立
    elif high_condition:
        return 2 # 枢轴高点
    elif low_condition:
        return 1 # 枢轴低点
    else:
        return 0

![](img/806981b8ac7090d0856fba389fee94a8_14.png)

# 应用函数到整个DataFrame，假设我们使用前后各3根K线进行判断
n1 = 3
n2 = 3
df[‘Pivot’] = df.apply(lambda row: pivot_id(df, n1, n2) if row.name >= n1 and row.name < len(df)-n2 else 0, axis=1)
```

![](img/806981b8ac7090d0856fba389fee94a8_16.png)

现在，`df[‘Pivot’]`列存储了每根K线的枢轴点信息（1=低点，2=高点）。我们可以将这些点可视化在图表上以验证检测效果。

## 第二步：检测旗形模式
上一节我们介绍了如何识别单个的枢轴点。本节中，我们将利用这些枢轴点来检测由一系列点构成的旗形模式。

![](img/806981b8ac7090d0856fba389fee94a8_18.png)

![](img/806981b8ac7090d0856fba389fee94a8_19.png)

我们定义一个核心函数 `detect_flag`，它会在指定的历史窗口内寻找符合条件的枢轴点序列。

以下是该函数的关键步骤说明：

1.  **获取近期枢轴点**：从当前K线开始，向前回溯一定数量的K线（`lookback_candles`），获取这个窗口内所有标记为枢轴高点（值=2）和枢轴低点（值=1）的索引和价格。
2.  **检查点数**：确保我们至少找到了3个高点和3个低点。这是形成三角形收敛形态的最低要求。
3.  **检查顺序**：确保高点和低点是交替出现的（例如：高、低、高、低... 或 低、高、低、高...）。这符合价格波动的自然节奏。
4.  **线性拟合**：分别对高点的索引和价格值、低点的索引和价格值进行线性回归。
    *   拟合得到两条直线的斜率：`slope_max`（高点线斜率）和 `slope_min`（低点线斜率）。
    *   同时得到判定系数R²：`r_squared_max` 和 `r_squared_min`。R²越接近1，说明点越贴合这条直线。
5.  **模式判定**：当满足以下所有条件时，判定为检测到旗形模式：
    *   高点和低点交替出现。
    *   `r_squared_max > 0.9` 且 `r_squared_min > 0.9`（拟合效果很好）。
    *   `slope_max < 0`（高点连线向下倾斜）。
    *   `slope_min > 0`（低点连线向上倾斜）。

```python
from scipy import stats
import matplotlib.pyplot as plt

def detect_flag(candle_id, lookback_candles, window, plot_flag=False):
    """
    检测在指定蜡烛之前是否存在旗形模式。
    :param candle_id: 当前蜡烛的索引
    :param lookback_candles: 向前回溯的蜡烛数量
    :param window: 用于避免前瞻性偏差的窗口大小，应大于枢轴点检测的n1/n2
    :param plot_flag: 是否绘制检测到的模式
    :return: 如果检测到模式返回1，否则返回0
    """
    start_idx = max(0, candle_id - lookback_candles)
    df_slice = df.iloc[start_idx:candle_id]
    
    # 获取切片内的枢轴高点和低点
    high_pivots = df_slice[df_slice[‘Pivot’] == 2]
    low_pivots = df_slice[df_slice[‘Pivot’] == 1]
    
    # 条件1：至少有3个高点和3个低点
    if len(high_pivots) < 3 or len(low_pivots) < 3:
        return 0
    
    # 获取最近3个高点和低点（按索引排序）
    last_highs = high_pivots.tail(3)
    last_lows = low_pivots.tail(3)
    
    # 提取索引和价格用于回归分析
    high_idx = last_highs.index.values
    high_vals = last_highs[‘High’].values
    low_idx = last_lows.index.values
    low_vals = last_lows[‘Low’].values
    
    # 条件2：检查高点和低点是否大致交替出现（简化检查）
    all_pivots = pd.concat([last_highs[[‘High’]], last_lows[[‘Low’]]]).sort_index()
    # 这里可以添加更精确的顺序逻辑，例如检查序列是否为 [高，低，高，低...]
    
    # 对高点和低点进行线性回归
    slope_high, intercept_high, r_value_high, p_value_high, std_err_high = stats.linregress(high_idx, high_vals)
    slope_low, intercept_low, r_value_low, p_value_low, std_err_low = stats.linregress(low_idx, low_vals)
    
    r_sq_high = r_value_high**2
    r_sq_low = r_value_low**2
    
    # 条件3：拟合优度高，且斜率方向符合旗形（高点下降，低点上升）
    if r_sq_high > 0.9 and r_sq_low > 0.9 and slope_high < 0 and slope_low > 0:
        if plot_flag:
            # 绘制价格和检测到的旗形区域
            plt.figure(figsize=(12,6))
            plt.plot(df_slice[‘Close’], label=‘Close Price’)
            plt.scatter(high_idx, high_vals, color=‘red’, label=‘High Pivots’, zorder=5)
            plt.scatter(low_idx, low_vals, color=‘green’, label=‘Low Pivots’, zorder=5)
            # 绘制拟合的趋势线
            plt.plot(high_idx, intercept_high + slope_high * high_idx, ‘r--’, label=‘High Trendline’)
            plt.plot(low_idx, intercept_low + slope_low * low_idx, ‘g--’, label=‘Low Trendline’)
            plt.title(f‘Flag Pattern Detected before Candle {candle_id}’)
            plt.legend()
            plt.show()
        return 1 # 检测到旗形
    return 0 # 未检测到旗形

# 示例：检测特定蜡烛之前的模式
candle_to_test = 2000
lookback = 50
window_size = 5 # 应大于 n1 和 n2 (3)
detect_flag(candle_to_test, lookback, window_size, plot_flag=True)
```

运行上述代码，如果指定蜡烛之前存在旗形模式，函数将返回1并绘制出该模式的图表。

![](img/806981b8ac7090d0856fba389fee94a8_21.png)

## 在全数据集上应用检测
现在，我们可以将 `detect_flag` 函数应用到整个数据集，标记出所有出现在旗形模式之后的K线。

![](img/806981b8ac7090d0856fba389fee94a8_23.png)

```python
# 在全数据集上应用检测函数
lookback_period = 50
window = 5
df[‘Flag’] = 0 # 初始化一个新列

![](img/806981b8ac7090d0856fba389fee94a8_25.png)

# 遍历DataFrame的每一行（从足够靠后的位置开始，以保证有足够的历史数据）
for i in range(lookback_period, len(df)):
    df.loc[i, ‘Flag’] = detect_flag(i, lookback_period, window, plot_flag=False)

# 查看所有被标记为旗形模式出现后的K线
flag_candles = df[df[‘Flag’] == 1]
print(f“检测到 {len(flag_candles)} 根K线之前存在旗形模式。”)
print(flag_candles[[‘Open’, ‘High’, ‘Low’, ‘Close’, ‘Pivot’, ‘Flag’]].head())
```

![](img/806981b8ac7090d0856fba389fee94a8_27.png)

![](img/806981b8ac7090d0856fba389fee94a8_28.png)

您可能会注意到，连续的几根K线可能都被标记为`Flag=1`。这是因为同一个旗形模式会对其后方的多根K线产生影响，我们的算法在每一根K线处回溯检查时都会重复检测到同一个模式。这是正常现象。

## 参数优化与改进建议
代码算法中的几个关键参数可以根据您的需求进行调整，以优化检测效果：

以下是主要参数及其影响：
*   `n1`, `n2`（在`pivot_id`函数中）：定义判断枢轴点时向前/后看的邻居K线数量。增大这些值会使枢轴点的定义更严格（更少但更显著的转折点），减小则更敏感。
*   `lookback_candles`（在`detect_flag`函数中）：回溯检查的历史窗口长度。需要足够长以包含整个潜在的旗形形态。
*   `window`：用于避免使用未来数据的缓冲区大小，应始终大于`n1`和`n2`。
*   线性回归的R²阈值（代码中为0.9）：控制趋势线拟合的严格程度。降低阈值会检测到更多不完美的“近似”旗形，提高阈值则要求形态更规整。

![](img/806981b8ac7090d0856fba389fee94a8_30.png)

![](img/806981b8ac7090d0856fba389fee94a8_31.png)

![](img/806981b8ac7090d0856fba389fee94a8_32.png)

您可以通过回测不同参数组合在历史数据上的表现，来找到最适合您交易品种和时间框架的参数。

![](img/806981b8ac7090d0856fba389fee94a8_33.png)

## 总结
本节课中，我们一起学习了旗形模式交易策略的自动化检测方法。

![](img/806981b8ac7090d0856fba389fee94a8_35.png)

我们首先了解了旗形模式的市场含义和两种交易情景。接着，我们构建了一个分两步走的算法：第一步是检测价格图表中的局部转折点（枢轴点）；第二步是在一系列枢轴点中寻找符合特定几何和统计特征（高点下降、低点上升、线性拟合良好）的组合，从而识别出旗形形态。

![](img/806981b8ac7090d0856fba389fee94a8_37.png)

我们使用Python实现了这个算法，包括数据加载、枢轴点识别、线性回归分析和模式标记等完整流程。最后，我们还讨论了如何调整算法参数以优化检测效果。

![](img/806981b8ac7090d0856fba389fee94a8_39.png)

通过本教程，您已经掌握了使用Python自动识别技术分析模式的基础方法，可以在此基础上进一步开发交易策略或进行市场分析。