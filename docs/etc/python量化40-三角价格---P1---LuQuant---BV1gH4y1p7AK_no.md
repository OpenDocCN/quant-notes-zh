# Python量化40：三角价格指标检测 - P1 - LuQuant

![](img/1de26219a83cae429820d1988ed4af4c_1.png)

## 📋 概述
在本节课中，我们将学习如何使用Python自动化检测价格图表中的三角形形态。我们将从识别关键的价格转折点（枢轴点）开始，然后通过线性回归拟合这些点来识别收敛三角形、上升三角形和下降三角形等形态。最后，我们将编写代码并在真实的外汇数据上进行测试和可视化。

## 📊 1：理解三角形形态与枢轴点
三角形形态是技术分析中常见的图表模式，通常预示着价格趋势的延续或反转。检测这些形态的第一步是识别构成三角形边界的价格高点（阻力）和低点（支撑），这些点被称为枢轴点。

![](img/1de26219a83cae429820d1988ed4af4c_3.png)

枢轴点是价格图表中高于或低于其相邻一定数量K线（例如，前后各3根K线）的价格极值点。高点枢轴点（Pivot High）是指其最高价高于前后邻居K线的最高价；低点枢轴点（Pivot Low）则是指其最低价低于前后邻居K线的最低价。

![](img/1de26219a83cae429820d1988ed4af4c_5.png)

以下是识别枢轴点的核心逻辑描述：
```python
# 伪代码逻辑
if 当前K线最低价 < 左侧N根K线最低价 and 当前K线最低价 < 右侧N根K线最低价:
    标记为低点枢轴点
if 当前K线最高价 > 左侧N根K线最高价 and 当前K线最高价 > 右侧N根K线最高价:
    标记为高点枢轴点
```

## 🔍 2：算法步骤与三角形定义
上一节我们介绍了如何识别构成三角形边界的枢轴点。本节中，我们来看看如何利用这些点来定义和检测特定的三角形形态。

整个检测过程分为两个主要步骤：
1.  计算并标记图表中的所有枢轴点。
2.  在局部区域内，将高点和低点分别拟合成两条直线，并根据这两条直线的斜率关系来判断三角形形态。

以下是几种常见的三角形形态及其斜率特征：
*   **收敛三角形**：高点连线的斜率为负（下降），低点连线的斜率为正（上升），两条线逐渐收敛。
    *   **公式**：`斜率_高点 < 0` 且 `斜率_低点 > 0`
*   **上升三角形**：高点连线斜率接近零（水平阻力），低点连线斜率为正（上升支撑）。
    *   **公式**：`abs(斜率_高点) ≈ 0` 且 `斜率_低点 > 0`
*   **下降三角形**：低点连线斜率接近零（水平支撑），高点连线斜率为负（下降阻力）。
    *   **公式**：`abs(斜率_低点) ≈ 0` 且 `斜率_高点 < 0`

为了确保拟合的质量，我们还会使用线性回归的**决定系数（R²）** 来评估点与拟合直线的接近程度。R²越接近1，说明拟合越好。

## 💻 3：代码实现 - 数据准备与枢轴点识别
现在，我们开始用Python代码实现上述思路。首先进行数据准备和枢轴点识别。

我们使用EUR/USD货币对的历史数据，并清理掉成交量为0的无效数据（如节假日）。
```python
import pandas as pd
import numpy as np

# 假设df是包含‘Open‘, ‘High‘, ‘Low‘, ‘Close‘, ‘Volume‘的数据框
# 清理无效数据
df = df[df[‘Volume‘] != 0]
```
接下来，我们定义一个函数来识别枢轴点。该函数检查每根K线是否是其前后`n`根K线中的价格极值。
```python
def pivot_id(df, n_before, n_after):
    """
    识别枢轴点。
    返回: 1表示低点枢轴，2表示高点枢轴，0表示非枢轴点。
    """
    low_pivot = (df[‘Low‘].shift(n_before) > df[‘Low‘]) & (df[‘Low‘].shift(-n_after) > df[‘Low‘])
    high_pivot = (df[‘High‘].shift(n_before) < df[‘High‘]) & (df[‘High‘].shift(-n_after) < df[‘High‘])
    return np.where(low_pivot, 1, np.where(high_pivot, 2, 0))

# 应用函数，例如检查前后各3根K线
n_before = 3
n_after = 3
df[‘Pivot‘] = pivot_id(df, n_before, n_after)
```
为了可视化枢轴点，我们创建一个新列来存储绘图坐标。
```python
def point_position(row):
    if row[‘Pivot‘] == 1: # 低点枢轴
        return row[‘Low‘] - 10e-3 # 在低点下方标记
    elif row[‘Pivot‘] == 2: # 高点枢轴
        return row[‘High‘] + 10e-3 # 在高点上方标记
    else:
        return np.nan

df[‘PointPos‘] = df.apply(point_position, axis=1)
```
使用`plotly`库可以绘制K线图并叠加枢轴点，方便我们检查识别是否正确。

## 📈 4：代码实现 - 三角形检测与可视化
识别出枢轴点后，我们就可以在图表中搜索三角形形态了。本节将编写一个函数，在指定K线附近寻找符合条件的三角形。

核心思路是：对于给定的K线，回顾其之前一定数量（如20根）的K线，收集其中的高点和低点枢轴点。如果收集到的点足够多（例如至少各3个），则分别对高点和低点进行线性回归拟合。最后，根据拟合出的两条直线的斜率以及拟合优度（R²）来判断是否形成了特定类型的三角形。

![](img/1de26219a83cae429820d1988ed4af4c_7.png)

以下是检测函数的核心逻辑框架：
```python
from scipy import stats

![](img/1de26219a83cae429820d1988ed4af4c_9.png)

def detect_triangle(df, candle_id, lookback=20, min_slope=0.001, r_squared_threshold=0.7):
    """
    在指定candle_id附近检测三角形。
    """
    start_idx = max(0, candle_id - lookback)
    window_df = df.iloc[start_idx:candle_id+1]

    # 收集枢轴点坐标
    high_points = window_df[window_df[‘Pivot‘] == 2][[‘High‘]].values
    low_points = window_df[window_df[‘Pivot‘] == 1][[‘Low‘]].values

    # 检查是否有足够多的点
    if len(high_points) < 3 or len(low_points) < 3:
        return None

    # 为高点拟合直线 (x为索引，y为价格)
    x_high = np.arange(len(high_points)).reshape(-1, 1)
    slope_high, intercept_high, r_value_high, _, _ = stats.linregress(x_high.flatten(), high_points.flatten())
    r_sq_high = r_value_high**2

    # 为低点拟合直线
    x_low = np.arange(len(low_points)).reshape(-1, 1)
    slope_low, intercept_low, r_value_low, _, _ = stats.linregress(x_low.flatten(), low_points.flatten())
    r_sq_low = r_value_low**2

    # 判断三角形类型
    triangle_type = None
    if abs(slope_high) < min_slope and slope_low > min_slope and r_sq_high > r_squared_threshold and r_sq_low > r_squared_threshold:
        triangle_type = ‘Ascending‘ # 上升三角形
    elif abs(slope_low) < min_slope and slope_high < -min_slope and r_sq_high > r_squared_threshold and r_sq_low > r_squared_threshold:
        triangle_type = ‘Descending‘ # 下降三角形
    elif slope_high < -min_slope and slope_low > min_slope and r_sq_high > r_squared_threshold and r_sq_low > r_squared_threshold:
        triangle_type = ‘Symmetrical‘ # 收敛三角形

    if triangle_type:
        return {
            ‘candle_id‘: candle_id,
            ‘type‘: triangle_type,
            ‘slope_high‘: slope_high,
            ‘slope_low‘: slope_low,
            ‘r_sq_high‘: r_sq_high,
            ‘r_sq_low‘: r_sq_low,
            ‘high_points‘: high_points,
            ‘low_points‘: low_points,
            ‘window_df‘: window_df
        }
    return None
```
我们可以遍历数据框，调用此函数来寻找所有潜在的三角形形态。找到后，使用绘图函数将三角形区域、拟合的边界线以及突破点可视化出来，以验证检测效果并辅助交易决策。

## 🎯 5：参数调整与策略应用
检测算法的效果很大程度上依赖于参数的设置。通过调整参数，我们可以在信号数量和质量之间取得平衡。

以下是主要的可调参数及其影响：
*   **`n_before` / `n_after`**：识别枢轴点时，向前/后查看的K线数量。增大此值会使枢轴点更显著但数量更少。
*   **`lookback`**：检测三角形时回顾的K线数量。它定义了寻找三角形的局部窗口大小。
*   **`min_slope`**：判断斜率是否“接近水平”的阈值。值越小，对水平线的要求越严格。
*   **`r_squared_threshold`**：拟合优度（R²）的最小要求。值越高（越接近1），对点排列成直线的要求越严格，检测到的三角形形态越“标准”，但数量也会减少。

在实际应用中，例如想寻找更多“上升三角形”，可以设置 `slope_high ≈ 0` 且 `slope_low > 0`。若想过滤掉质量不高的信号，则提高 `r_squared_threshold`。

将三角形检测纳入交易策略的一个常见思路是**突破交易**。当价格收盘价有效突破三角形的某一边界（阻力或支撑）时，视为趋势可能沿突破方向延续的信号，从而入场交易。

## 📝 总结
本节课中，我们一起学习了如何用Python自动化检测价格图表中的三角形形态。

我们从理解枢轴点的概念和识别方法开始，然后阐述了通过线性回归拟合高低点来定义三角形形态（收敛、上升、下降）的算法原理。接着，我们逐步实现了数据清洗、枢轴点识别、三角形检测函数以及结果可视化的完整代码流程。最后，我们探讨了关键参数对检测结果的影响以及如何将这种检测方法初步应用于交易策略中。

![](img/1de26219a83cae429820d1988ed4af4c_11.png)

![](img/1de26219a83cae429820d1988ed4af4c_12.png)

这个算法提供了一个基础框架，你可以通过调整参数、增加过滤条件（如成交量、形态大小）或结合其他指标来优化它，从而构建更稳健的交易系统。