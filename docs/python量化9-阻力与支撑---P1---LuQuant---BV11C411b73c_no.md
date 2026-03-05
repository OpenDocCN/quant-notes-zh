# Python量化交易教程9：1：阻力与支撑检测

![](img/62a2d919cbe1e6231008830d18e11203_1.png)

在本节课中，我们将学习一种基于历史价格数据检测关键支撑位和阻力位的简单方法。我们将通过识别“分型”或“枢轴点”蜡烛，并统计价格在这些水平附近反弹的次数，来量化每个支撑或阻力区域的强度。

![](img/62a2d919cbe1e6231008830d18e11203_3.png)

## 概述

支撑位和阻力位是技术分析中的核心概念。支撑位是价格下跌时可能遇到买盘从而止跌回升的水平；阻力位则是价格上涨时可能遇到卖盘从而止涨回落的水平。本节课的目标是编写一个程序，自动从历史K线数据中找出这些关键水平，并评估其强度，以便在未来的交易决策中作为参考。

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何具体实现一个支撑阻力检测工具。

## 数据准备与枢轴点识别

![](img/62a2d919cbe1e6231008830d18e11203_5.png)

首先，我们需要加载历史K线数据。这里我们使用欧元/美元（EUR/USD）货币对在2020年至2023年间的1小时K线数据。

```python
import pandas as pd

# 加载数据
df = pd.read_csv(‘eurusd_hourly.csv‘)
# 清理数据，移除无交易量的行（如周末）
df = df[df[‘Volume‘] > 0]
df.reset_index(drop=True, inplace=True)
# 检查缺失值
print(df.isnull().sum())
print(df.head(10))
```

接下来，我们定义函数来识别“枢轴点”蜡烛。枢轴高点是指一根蜡烛的最高价同时高于其左侧N根和右侧N根邻居蜡烛的最高价。枢轴低点则相反，其最低价同时低于其左右邻居的最低价。

以下是识别枢轴点的函数：

```python
def pivot_id(df, candle_index, left_neighbors, right_neighbors):
    """
    判断指定索引的蜡烛是否为枢轴点。
    参数:
        df: 包含‘High‘, ‘Low‘列的DataFrame
        candle_index: 要判断的蜡烛索引
        left_neighbors: 左侧考虑的邻居数量
        right_neighbors: 右侧考虑的邻居数量
    返回:
        2: 枢轴高点
        1: 枢轴低点
        0: 非枢轴点
    """
    if candle_index < left_neighbors or candle_index + right_neighbors >= len(df):
        return 0

    pivot_high = True
    pivot_low = True
    current_high = df.loc[candle_index, ‘High‘]
    current_low = df.loc[candle_index, ‘Low‘]

    # 检查是否为枢轴高点
    for i in range(candle_index - left_neighbors, candle_index + right_neighbors + 1):
        if i == candle_index:
            continue
        if df.loc[i, ‘High‘] > current_high:
            pivot_high = False
            break

    # 检查是否为枢轴低点
    for i in range(candle_index - left_neighbors, candle_index + right_neighbors + 1):
        if i == candle_index:
            continue
        if df.loc[i, ‘Low‘] < current_low:
            pivot_low = False
            break

    if pivot_high and pivot_low:
        return 3 # 高低点重合，本教程不关注此情况
    elif pivot_high:
        return 2
    elif pivot_low:
        return 1
    else:
        return 0

![](img/62a2d919cbe1e6231008830d18e11203_7.png)

# 应用函数到每一行
df[‘Pivot‘] = df.apply(lambda row: pivot_id(df, row.name, left_neighbors=5, right_neighbors=5), axis=1)
```

![](img/62a2d919cbe1e6231008830d18e11203_9.png)

为了可视化这些枢轴点，我们可以在图表上标记它们：

![](img/62a2d919cbe1e6231008830d18e11203_10.png)

![](img/62a2d919cbe1e6231008830d18e11203_11.png)

```python
def point_pos(df):
    if df[‘Pivot‘] == 2:
        return df[‘High‘] + 0.0003 # 在蜡烛高点上方标记点
    elif df[‘Pivot‘] == 1:
        return df[‘Low‘] - 0.0003 # 在蜡烛低点下方标记点
    else:
        return None

df[‘PointPos‘] = df.apply(point_pos, axis=1)
# 之后可以使用绘图库（如mplfinance）将PointPos列的点绘制在K线图上
```

![](img/62a2d919cbe1e6231008830d18e11203_13.png)

![](img/62a2d919cbe1e6231008830d18e11203_15.png)

## 构建支撑阻力直方图

![](img/62a2d919cbe1e6231008830d18e11203_16.png)

识别出枢轴点后，我们分别收集所有枢轴高点的价格（潜在阻力位）和所有枢轴低点的价格（潜在支撑位）。通过绘制这些价格的分布直方图，我们可以直观地看到价格在哪些水平附近反复出现反弹。

![](img/62a2d919cbe1e6231008830d18e11203_18.png)

以下是构建直方图的关键步骤：

![](img/62a2d919cbe1e6231008830d18e11203_20.png)

```python
import numpy as np
import matplotlib.pyplot as plt

![](img/62a2d919cbe1e6231008830d18e11203_21.png)

![](img/62a2d919cbe1e6231008830d18e11203_22.png)

# 1. 提取枢轴点的价格
pivot_high_prices = df[df[‘Pivot‘] == 2][‘High‘].values
pivot_low_prices = df[df[‘Pivot‘] == 1][‘Low‘].values

![](img/62a2d919cbe1e6231008830d18e11203_24.png)

# 2. 定义区域宽度 (Bin Width)
bin_width = 0.002  # 这是一个关键参数，定义了支撑/阻力“区域”的宽度

# 3. 计算直方图
# 确定价格范围并计算需要的箱子数量
price_min = min(pivot_low_prices.min(), pivot_high_prices.min())
price_max = max(pivot_low_prices.max(), pivot_high_prices.max())
num_bins = int((price_max - price_min) / bin_width)

# 绘制直方图
plt.figure(figsize=(12, 6))
plt.hist(pivot_high_prices, bins=num_bins, alpha=0.5, label=‘Resistance (High Pivots)‘, color=‘red‘, edgecolor=‘black‘)
plt.hist(pivot_low_prices, bins=num_bins, alpha=0.5, label=‘Support (Low Pivots)‘, color=‘blue‘, edgecolor=‘black‘)
plt.xlabel(‘Price‘)
plt.ylabel(‘Frequency (Number of Touches)‘)
plt.title(‘Support and Resistance Levels Histogram‘)
plt.legend()
plt.show()
```

![](img/62a2d919cbe1e6231008830d18e11203_26.png)

![](img/62a2d919cbe1e6231008830d18e11203_27.png)

**核心概念解释**：
*   **区域宽度 (Bin Width)**：这定义了我们将多大范围内的价格视为同一个支撑或阻力“区域”。例如，`bin_width = 0.002` 意味着我们将价格相差在0.002以内的所有枢轴点视为在测试同一个水平。这个参数至关重要：
    *   值太大：区域过宽，会合并过多不同水平，失去精确性。
    *   值太小：区域过窄，每个区域内的样本（反弹次数）可能太少，导致图形噪声过大，无法识别出有意义的水平。

在直方图中，柱子越高，代表价格在该价格区域（柱子对应的价格区间）反弹的次数越多，该区域的支撑或阻力强度也就越强。

![](img/62a2d919cbe1e6231008830d18e11203_29.png)

![](img/62a2d919cbe1e6231008830d18e11203_30.png)

## 参数调整与结果分析

![](img/62a2d919cbe1e6231008830d18e11203_32.png)

调整 `bin_width` 参数会显著影响分析结果。让我们看看不同设置下的效果：

![](img/62a2d919cbe1e6231008830d18e11203_33.png)

![](img/62a2d919cbe1e6231008830d18e11203_34.png)

1.  **较宽的区间 (例如 0.005)**：直方图柱子更少、更宽。这有助于过滤噪音，突出显示最重要、最宽泛的支撑阻力区域，但会损失一些精确度。
2.  **较窄的区间 (例如 0.001)**：直方图柱子更多、更细。这提高了价格水平的精确度，但可能会因为样本分散而产生许多“矮柱子”，使得真正的关键水平被噪音淹没。

![](img/62a2d919cbe1e6231008830d18e11203_36.png)

通常需要根据交易品种的波动性和分析的时间框架来反复测试，选择一个能清晰展示少数几个突出峰值的 `bin_width`。

以下是分析结果的解读方法：

![](img/62a2d919cbe1e6231008830d18e11203_38.png)

*   **独立的高峰**：一个只在支撑（蓝色）或阻力（红色）直方图中出现的突出高峰，代表一个纯粹的关键支撑位或阻力位。
*   **重叠的高峰**：如果一个价格区域在支撑和阻力直方图中都出现了高峰，这意味着该区域在历史上既充当过支撑也充当过阻力。这是一个非常强大的**交易区间**信号，价格很可能继续在该区域内震荡。
*   **通过观察整个历史数据的直方图**，我们可以识别出长期有效的关键水平。这些水平在未来价格再次接近时，值得交易者密切关注。

![](img/62a2d919cbe1e6231008830d18e11203_39.png)

## 总结

本节课中我们一起学习了如何用Python实现一个简单的支撑阻力位检测工具。我们首先通过识别“枢轴点”蜡烛来定位潜在的反转点，然后通过统计这些点的价格分布，利用直方图来可视化并量化关键价格区域的强度。我们重点探讨了**区域宽度**这个核心参数的影响，并学会了如何解读直方图来找到强有力的支撑位、阻力位以及重要的交易区间。

![](img/62a2d919cbe1e6231008830d18e11203_41.png)

![](img/62a2d919cbe1e6231008830d18e11203_42.png)

这种方法基于一个核心假设：历史价格行为会重复。虽然简单，但它为量化市场结构提供了一个清晰的起点。你可以将此工具应用于不同的交易品种和时间框架，并将其作为更复杂交易策略的组成部分。