# Python量化22：通道突破指标 - P1 - LuQuant

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_1.png)

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_3.png)

## 概述
在本节课中，我们将学习如何使用Python实现价格通道突破指标。这是一种技术分析工具，通过绘制价格通道来帮助交易者识别潜在的趋势变化信号。我们将从核心概念讲起，逐步实现检测枢轴点、拟合价格通道以及识别突破信号的完整算法。

---

## 1️⃣ 核心概念：价格通道突破

价格通道突破是一种技术分析工具。它通过围绕价格行为绘制一条通道来帮助交易者识别潜在的价格突破。该通道代表了一段时间内价格的高点和低点。当价格突破通道时，可以发出潜在趋势变化或市场机会的信号。

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_5.png)

在Python中，我们可以自动化识别通道和潜在突破发生的过程。

**核心逻辑公式化表示：**
*   **上轨突破（看涨信号）**：`前一蜡烛低点 > 通道下轨` 且 `前一蜡烛收盘价 > 通道上轨` 且 `当前蜡烛开盘价与收盘价 > 通道上轨`
*   **下轨突破（看跌信号）**：`前一蜡烛高点 < 通道上轨` 且 `前一蜡烛收盘价 < 通道下轨` 且 `当前蜡烛开盘价与收盘价 < 通道下轨`

---

## 2️⃣ 算法步骤总览

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_7.png)

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_8.png)

整个算法分为三个主要阶段。

上一节我们介绍了价格通道突破的核心概念，本节中我们来看看实现它的具体步骤。

以下是实现通道突破检测的三个关键步骤：

1.  **检测枢轴点**：识别相对于邻近蜡烛表现出极高或极低值的蜡烛。
2.  **检测通道**：使用检测到的枢轴点（高点和低点）拟合出通道的上轨和下轨。
3.  **检测突破**：根据定义的规则，判断价格是否突破了通道，并预测未来趋势方向。

---

## 3️⃣ 第一步：检测枢轴点

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_10.png)

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_11.png)

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_12.png)

枢轴点是价格图表上的局部高点或低点。我们将编写一个函数来检测它们。

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_14.png)

以下是检测枢轴点的Python函数 `is_pivot` 的核心逻辑：

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_16.png)

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_18.png)

```python
def is_pivot(candle, window, df):
    """
    判断给定蜡烛是否是枢轴点。
    :param candle: 要测试的蜡烛索引
    :param window: 在两侧考虑的相邻蜡烛数量
    :param df: 包含OHLC数据的数据框
    :return: 2 (枢轴低点), 1 (枢轴高点), 3 (同时为高点和低点), 0 (非枢轴点)
    """
    # 处理数据边界情况
    if candle < window or candle + window >= len(df):
        return 0

    # 获取当前蜡烛的高低价
    current_high = df.loc[candle, 'High']
    current_low = df.loc[candle, 'Low']

    # 初始化标签
    pivot_high = 1
    pivot_low = 2

    # 检查是否为枢轴高点：当前高点必须高于所有相邻窗口内蜡烛的高点
    for i in range(candle - window, candle + window + 1):
        if i == candle:
            continue
        if df.loc[i, 'High'] > current_high:
            pivot_high = 0 # 取消高点标签
            break

    # 检查是否为枢轴低点：当前低点必须低于所有相邻窗口内蜡烛的低点
    for i in range(candle - window, candle + window + 1):
        if i == candle:
            continue
        if df.loc[i, 'Low'] < current_low:
            pivot_low = 0 # 取消低点标签
            break

    # 返回结果
    if pivot_high and pivot_low:
        return 3
    elif pivot_high:
        return 1
    elif pivot_low:
        return 2
    else:
        return 0
```

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_19.png)

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_21.png)

将此函数应用于数据框，并为绘图添加辅助函数 `point_position`，我们就能在图表上可视化这些枢轴点。

---

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_23.png)

## 4️⃣ 第二步：拟合价格通道

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_25.png)

在检测到枢轴点后，我们需要用它们来拟合价格通道的上轨和下轨。

上一节我们成功检测出了图表上的枢轴点，本节中我们将利用这些点来构建价格通道。

以下是拟合通道的函数 `collect_channel` 的关键步骤：

1.  **选取局部数据**：为了避免前瞻性偏差，我们只使用当前测试蜡烛**之前**的数据。例如，测试索引为 `candle` 的蜡烛时，我们使用 `df[candle - window - lookback : candle]` 的数据。
2.  **识别高低枢轴**：在局部数据中，分别找出所有标记为枢轴高点（`pivot=1`）和枢轴低点（`pivot=2`）的点。
3.  **线性拟合**：如果高低枢轴点都至少有2个，则分别对它们进行线性回归，拟合出两条直线。这两条线就构成了通道。
    *   拟合低点得到通道下轨：`y_low = slope_low * x + intercept_low`
    *   拟合高点得到通道上轨：`y_high = slope_high * x + intercept_high`
4.  **返回参数**：函数返回两条拟合线的斜率、截距以及可选的R平方值（用于衡量拟合优度）。

```python
from scipy import stats

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_27.png)

def collect_channel(candle, lookback, window, df):
    """
    为指定蜡烛收集并拟合价格通道。
    :param candle: 当前蜡烛索引
    :param lookback: 向后查看的蜡烛数量
    :param window: 枢轴点检测窗口
    :param df: 数据框
    :return: 通道参数 (slope_low, intercept_low, slope_high, intercept_high, r2_low, r2_high)
    """
    # 1. 获取局部数据，避免前瞻偏差
    start_idx = max(0, candle - lookback - window)
    local_df = df.iloc[start_idx:candle].copy() # 注意：不包含candle本身

    # 2. 在局部数据上检测枢轴点
    # ... (应用 is_pivot 函数) ...

    # 3. 分离高低枢轴点的索引和价格
    high_pivots_idx = local_df[local_df['pivot'] == 1].index
    high_pivots_val = local_df.loc[high_pivots_idx, 'High'].values
    low_pivots_idx = local_df[local_df['pivot'] == 2].index
    low_pivots_val = local_df.loc[low_pivots_idx, 'Low'].values

    # 4. 检查点数并拟合
    if len(high_pivots_idx) >= 2 and len(low_pivots_idx) >= 2:
        # 拟合高点（上轨）
        slope_high, intercept_high, r_value_high, _, _ = stats.linregress(high_pivots_idx, high_pivots_val)
        r2_high = r_value_high**2
        # 拟合低点（下轨）
        slope_low, intercept_low, r_value_low, _, _ = stats.linregress(low_pivots_idx, low_pivots_val)
        r2_low = r_value_low**2

        return slope_low, intercept_low, slope_high, intercept_high, r2_low, r2_high
    else:
        return 0, 0, 0, 0, 0, 0 # 无法拟合通道
```

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_29.png)

---

## 5️⃣ 第三步：检测通道突破

最后一步是定义规则，判断价格是否发生了有意义的通道突破。

上一节我们构建了价格通道，本节中我们来制定规则，识别价格何时突破了通道。

以下是突破检测函数 `check_breakout` 的逻辑：

```python
def check_breakout(candle, lookback, window, df):
    """
    检查指定蜡烛是否发生通道突破。
    :param candle: 当前蜡烛索引
    :param lookback: 收集通道时向后看的蜡烛数
    :param window: 枢轴点窗口
    :param df: 数据框
    :return: 1 (上破，看涨), -1 (下破，看跌), 0 (无突破)
    """
    # 边界检查
    if candle < 1 or candle - lookback - window < 0:
        return 0

    # 1. 获取当前蜡烛的通道参数
    slope_low, intercept_low, slope_high, intercept_high, _, _ = collect_channel(candle, lookback, window, df)
    if slope_low == 0: # 通道无效
        return 0

    # 2. 获取关键价格
    prev_idx = candle - 1
    curr_idx = candle

    prev_high = df.loc[prev_idx, 'High']
    prev_low = df.loc[prev_idx, 'Low']
    prev_close = df.loc[prev_idx, 'Close']

    curr_open = df.loc[curr_idx, 'Open']
    curr_close = df.loc[curr_idx, 'Close']
    curr_low = df.loc[curr_idx, 'Low']

    # 3. 计算当前蜡烛处通道的上下轨值
    channel_high_curr = slope_high * curr_idx + intercept_high
    channel_low_curr = slope_low * curr_idx + intercept_low
    # 计算前一蜡烛处通道的上下轨值（用于判断前一蜡烛是否在通道内）
    channel_high_prev = slope_high * prev_idx + intercept_high
    channel_low_prev = slope_low * prev_idx + intercept_low

    # 4. 下破规则（看跌）：前一蜡烛高点在通道内，收盘价跌破下轨，且当前蜡烛开收盘均在下轨之下
    if (prev_high < channel_high_prev and
        prev_close < channel_low_prev and
        curr_open < channel_low_curr and
        curr_close < channel_low_curr):
        return -1 # 下降趋势突破

    # 5. 上破规则（看涨）：前一蜡烛低点在通道内，收盘价升破上轨，且当前蜡烛开收盘均在上轨之上
    if (prev_low > channel_low_prev and
        prev_close > channel_high_prev and
        curr_open > channel_high_curr and
        curr_close > channel_high_curr):
        return 1 # 上升趋势突破

    return 0
```

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_31.png)

我们可以遍历数据框，应用此函数，并将检测到的突破点（例如用黑色星形标记）绘制在图表上，进行可视化验证。

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_33.png)

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_35.png)

---

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_36.png)

## 总结

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_38.png)

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_39.png)

本节课中我们一起学习了如何用Python实现一个完整的通道突破指标。

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_41.png)

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_42.png)

1.  **理解原理**：价格通道突破是通过识别价格对动态通道边界的突破来预测趋势。
2.  **实现检测**：我们分三步实现了算法：检测局部高低点（枢轴点）、用线性回归拟合通道、根据特定规则判断突破信号。
3.  **避免偏差**：在编码中特别注意了避免使用未来数据，确保策略在实盘中的可行性。
4.  **可视化**：通过绘图将枢轴点、拟合通道和突破信号直观地展示出来，便于分析和调试。

![](img/1acd09ad44069c2bd9e7f3f94ced7fe8_44.png)

您可以根据自己的交易偏好，调整枢轴点窗口大小、通道拟合的最小点数、突破确认规则或加入R平方过滤条件，来优化这个基础策略。