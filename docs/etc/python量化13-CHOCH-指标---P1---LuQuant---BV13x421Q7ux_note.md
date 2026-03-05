# Python量化13：CHOCH指标 - P1 - LuQuant

在本教程中，我们将学习如何用Python实现CHOCH指标，即“模式变化”或“特征变化”指标。该指标用于识别价格趋势中“更高的高点和更高的低点”模式的突破，从而发现潜在的趋势反转信号。

![](img/db73f448519efc462fbd9b60676ceb3e_1.png)

我们将从加载数据开始，然后逐步实现趋势检测、枢轴点识别，并最终编写核心函数来检测CHOCH模式。教程将包含清晰的代码示例和逻辑解释，确保初学者能够理解并应用。

---

## 1️⃣ 理解CHOCH模式

![](img/db73f448519efc462fbd9b60676ceb3e_3.png)

CHOCH模式用于识别趋势的潜在变化。它主要关注两种形态：

1.  **上升趋势中的突破**：价格原本呈现更高的高点和更高的低点，但随后出现一个更低的低点，跌破了前一个低点，这预示着上升趋势可能被打破。
2.  **买家动能衰竭**：在上升趋势中，出现一个比前一个高点更低的高点，表明买家力量减弱，随后价格可能下跌并跌破前一个低点。

![](img/db73f448519efc462fbd9b60676ceb3e_5.png)

这两种情况都提供了潜在的空头入场机会。反之，在下降趋势中，模式是对称的，可用于寻找多头机会。

![](img/db73f448519efc462fbd9b60676ceb3e_7.png)

---

## 2️⃣ 数据准备与趋势检测

首先，我们需要加载数据并定义一个基本的趋势方向。我们将使用移动平均线来辅助判断。

以下是加载数据和计算移动平均线的步骤：

```python
import pandas as pd
import ta  # 技术分析库

# 加载数据，假设df包含OHLC数据
# df = pd.read_csv('your_data.csv')
df = df.copy()

# 计算指数移动平均线 (EMA)
ema_length = 150
df['ema'] = ta.trend.ema_indicator(df['close'], window=ema_length)

![](img/db73f448519efc462fbd9b60676ceb3e_9.png)

# 计算RSI (可选，本教程主要用于趋势过滤)
rsi_length = 12
df['rsi'] = ta.momentum.rsi(df['close'], window=rsi_length)

![](img/db73f448519efc462fbd9b60676ceb3e_10.png)

# 为演示，我们只取5000行数据以加快计算
df = df.tail(5000).reset_index(drop=True)
```

![](img/db73f448519efc462fbd9b60676ceb3e_12.png)

接下来，我们定义一个函数，通过检查价格是否持续位于移动平均线之上或之下来判断趋势。

```python
def trend_signal(data, window=15):
    """
    根据价格与EMA的位置关系判断趋势。
    参数:
        data: 包含'close'和'ema'列的DataFrame。
        window: 需要连续满足条件的K线数量。
    返回:
        signal: 1 (下降趋势), 2 (上升趋势), 3 (无明确趋势)
    """
    signals = []
    for i in range(len(data)):
        if i < window:
            signals.append(3)  # 数据不足，无法判断
            continue
        # 获取当前K线之前window根K线的收盘价和EMA值
        recent_closes = data['close'].iloc[i-window:i]
        recent_ema = data['ema'].iloc[i-window:i]
        # 判断是否全部在EMA之上（上升趋势）或之下（下降趋势）
        if all(recent_closes > recent_ema):
            signals.append(2)  # 上升趋势
        elif all(recent_closes < recent_ema):
            signals.append(1)  # 下降趋势
        else:
            signals.append(3)  # 震荡或无趋势
    return signals

# 应用趋势信号函数
df['ma_signal'] = trend_signal(df)
```

![](img/db73f448519efc462fbd9b60676ceb3e_14.png)

上一节我们介绍了如何用移动平均线判断基础趋势。本节中，我们来看看如何识别构成CHOCH模式的关键点——枢轴点。

![](img/db73f448519efc462fbd9b60676ceb3e_16.png)

---

## 3️⃣ 识别枢轴点

枢轴点是局部的高点或低点，是构成趋势模式的基础。我们将编写一个函数来识别它们。

![](img/db73f448519efc462fbd9b60676ceb3e_18.png)

```python
def find_pivot(data, index, window=5):
    """
    判断给定索引的K线是否为枢轴点。
    参数:
        data: 包含'high'和'low'列的DataFrame。
        index: 要检查的K线索引。
        window: 前后比较的K线数量。
    返回:
        1: 枢轴高点 (之后预期下跌)
        2: 枢轴低点 (之后预期上涨)
        0: 不是枢轴点
    """
    if index < window or index >= len(data) - window:
        return 0  # 处于边缘，不判断
    
    current_high = data['high'].iloc[index]
    current_low = data['low'].iloc[index]
    
    # 获取前后window根K线的最高价和最低价
    left_high = data['high'].iloc[index-window:index].max()
    right_high = data['high'].iloc[index+1:index+window+1].max()
    left_low = data['low'].iloc[index-window:index].min()
    right_low = data['low'].iloc[index+1:index+window+1].min()
    
    # 判断是否为枢轴高点
    if current_high > left_high and current_high > right_high:
        return 1
    # 判断是否为枢轴低点
    elif current_low < left_low and current_low < right_low:
        return 2
    else:
        return 0

![](img/db73f448519efc462fbd9b60676ceb3e_20.png)

# 应用枢轴点检测函数
pivot_values = []
for i in range(len(df)):
    pivot_values.append(find_pivot(df, i, window=5))
df['pivot'] = pivot_values
```

为了可视化，我们可以将枢轴高点标记为一种颜色，枢轴低点标记为另一种颜色。

现在我们已经能够识别趋势和关键的价格转折点。接下来，我们将进入核心部分：编写函数来检测CHOCH模式。

![](img/db73f448519efc462fbd9b60676ceb3e_22.png)

---

## 4️⃣ 检测CHOCH模式

![](img/db73f448519efc462fbd9b60676ceb3e_24.png)

这是最关键的一步。我们需要在一个特定的窗口内，检查最近的枢轴点序列是否符合“更高的高点和更高的低点”模式，并且是否出现了模式的突破。

![](img/db73f448519efc462fbd9b60676ceb3e_26.png)

![](img/db73f448519efc462fbd9b60676ceb3e_27.png)

以下是检测函数的详细步骤和代码：

```python
def detect_choch(data, current_index, lookback=40, window=6, limit1=0.002):
    """
    检测CHOCH模式。
    参数:
        data: 包含'high', 'low', 'pivot'列的DataFrame。
        current_index: 当前要检测的K线索引。
        lookback: 在当前K线之前回顾的K线数量。
        window: 为避免前瞻性偏差，排除的最近K线数。
        limit1: 主要价格波动的最小幅度限制。
    返回:
        1: 检测到CHOCH模式 (潜在趋势反转)
        0: 未检测到
    """
    # 1. 数据切片：取当前K线之前，但又排除最近window根K线的数据段
    start_idx = max(0, current_index - lookback - window)
    end_idx = current_index - window
    if end_idx <= start_idx:
        return 0
    df_slice = data.iloc[start_idx:end_idx].copy()
    
    # 2. 从切片中提取枢轴高点和低点
    high_pivots = df_slice[df_slice['pivot'] == 1]
    low_pivots = df_slice[df_slice['pivot'] == 2]
    
    # 需要至少3个高点和3个低点来形成模式
    if len(high_pivots) < 3 or len(low_pivots) < 3:
        return 0
    
    # 取最后3个高点和低点
    last_3_highs = high_pivots.tail(3)
    last_3_lows = low_pivots.tail(3)
    
    # 获取它们的索引和价格
    high_indices = last_3_highs.index.tolist()
    high_prices = last_3_highs['high'].tolist()
    low_indices = last_3_lows.index.tolist()
    low_prices = last_3_lows['low'].tolist()
    
    # 3. 条件1：检查枢轴点顺序 (低-高-低-高-低-高)
    # 确保索引顺序交替出现：低点1 < 高点1 < 低点2 < 高点2 < 低点3 < 高点3
    if not (low_indices[0] < high_indices[0] < low_indices[1] < high_indices[1] < low_indices[2] < high_indices[2]):
        return 0
    
    # 4. 条件2：检查价格差异，过滤微小波动
    limit2 = limit1 / 3.0  # 回撤运动的限制更宽松
    
    # 第一个上升段：低点1 -> 高点1
    if abs(high_prices[0] - low_prices[0]) <= limit1:
        return 0
    # 第一个回撤：高点1 -> 低点2
    if abs(high_prices[0] - low_prices[1]) <= limit2:
        return 0
    # 第二个上升段：低点2 -> 高点2
    if abs(high_prices[1] - low_prices[1]) <= limit1:
        return 0
    # 第二个回撤：高点2 -> 低点3
    if abs(high_prices[1] - low_prices[2]) <= limit2:
        return 0
    
    # 5. 条件3：检查模式突破 (两种类型)
    # 获取当前K线（突破候选K线）的价格
    current_low = data['low'].iloc[current_index]
    current_high = data['high'].iloc[current_index]
    
    # 模式1：价格跌破前一个低点（低点3）
    pattern1 = current_low < low_prices[2]
    
    # 模式2：最后一个高点（高点3）低于前一个高点（高点2），且价格跌破前一个低点（低点3）
    pattern2 = (high_prices[2] < high_prices[1]) and (current_low < low_prices[2])
    
    # 如果满足所有顺序、幅度条件，并且出现任何一种突破模式，则返回1
    if pattern1 or pattern2:
        return 1
    else:
        return 0

![](img/db73f448519efc462fbd9b60676ceb3e_29.png)

# 对整个数据集应用CHOCH检测
choch_signals = []
for i in range(len(df)):
    # 从足够后的位置开始检测，以确保有足够的历史数据
    if i < 100:
        choch_signals.append(0)
    else:
        choch_signals.append(detect_choch(df, i, lookback=40, window=6, limit1=0.002))
df['choch_detected'] = choch_signals
```

![](img/db73f448519efc462fbd9b60676ceb3e_31.png)

---

![](img/db73f448519efc462fbd9b60676ceb3e_33.png)

## 5️⃣ 可视化与参数调整

![](img/db73f448519efc462fbd9b60676ceb3e_35.png)

检测到信号后，我们可以将其可视化以验证效果。同时，理解参数的影响至关重要。

![](img/db73f448519efc462fbd9b60676ceb3e_37.png)

以下是关键参数及其影响：

*   **`lookback`**：回顾期。决定在多少根历史K线中寻找枢轴点来构建模式。太短可能找不到完整模式，太长可能包含过时信息。
*   **`window`**：排除窗口。防止使用过于接近当前K线的数据，避免“未来函数”偏差。
*   **`limit1`** 和 **`limit2`**：价格波动过滤器。`limit1`过滤主要趋势波动的幅度，`limit2`过滤回撤波动的幅度。它们帮助忽略市场噪音，确保检测到的是有意义的模式。`limit2`通常设为`limit1`的分数（如1/3）。
*   **枢轴点`window`**：在`find_pivot`函数中，用于确定局部高/低点的前后比较范围。

![](img/db73f448519efc462fbd9b60676ceb3e_39.png)

**建议**：这些参数需要根据交易品种（如EUR/USD、BTC）和时间框架（如1小时、日线）进行调整。初学者应从默认值开始，通过历史数据回测观察信号质量，再进行微调。

我们可以选取检测到信号的区域进行绘图：

```python
import matplotlib.pyplot as plt

![](img/db73f448519efc462fbd9b60676ceb3e_41.png)

![](img/db73f448519efc462fbd9b60676ceb3e_42.png)

# 假设我们在索引376处检测到一个信号
signal_index = 376
plot_start = max(0, signal_index - 50)
plot_end = min(len(df), signal_index + 50)

![](img/db73f448519efc462fbd9b60676ceb3e_43.png)

fig, ax = plt.subplots(figsize=(15, 8))
ax.plot(df['close'].iloc[plot_start:plot_end], label='Close Price', alpha=0.5)
# 标记枢轴点
high_pivots = df[df['pivot']==1]
low_pivots = df[df['pivot']==2]
ax.scatter(high_pivots.index, high_pivots['high'], color='purple', s=50, label='Pivot High', zorder=5)
ax.scatter(low_pivots.index, low_pivots['low'], color='orange', s=50, label='Pivot Low', zorder=5)
# 标记CHOCH信号
choch_points = df[df['choch_detected']==1]
ax.scatter(choch_points.index, choch_points['close'], color='red', s=100, marker='^', label='CHOCH Signal', zorder=6)
ax.axvline(x=signal_index, color='grey', linestyle='--', alpha=0.5)
ax.set_title(f'CHOCH Signal Detection around Index {signal_index}')
ax.legend()
plt.show()
```

---

![](img/db73f448519efc462fbd9b60676ceb3e_45.png)

## 总结

![](img/db73f448519efc462fbd9b60676ceb3e_47.png)

在本节课中，我们一起学习了CHOCH指标的原理与Python实现。我们从数据准备和趋势判断开始，然后实现了枢轴点的识别，最后构建了核心的CHOCH模式检测函数。该函数通过检查特定顺序的枢轴点、价格波动幅度以及两种突破模式，来发现潜在的趋势反转信号。

关键要点如下：
1.  CHOCH指标用于识别“更高高点-更高低点”或“更低低点-更低高点”趋势模式的突破。
2.  实现分为三步：趋势过滤、枢轴点识别、模式逻辑检测。
3.  参数（如`lookback`, `limit1`）对信号质量有显著影响，需根据具体市场进行调整。
4.  本教程提供的代码是一个起点，可以集成到更复杂的策略中进行回测和验证。

![](img/db73f448519efc462fbd9b60676ceb3e_49.png)

![](img/db73f448519efc462fbd9b60676ceb3e_50.png)

通过理解和应用这个指标，你可以开始自动化地扫描图表，寻找潜在的趋势变化机会。请记住，任何单一指标都应与其他分析工具结合使用，并进行充分的风险管理。