# Python量化交易：第8课：自动化价格突破检测 📈

![](img/9652b49ca12f3d5d2d0c0d522f634faa_1.png)

在本节课中，我们将学习如何使用Python自动检测金融市场中的价格突破模式。我们将编写一个算法，识别价格在特定水平（支撑位或阻力位）多次反弹后形成的突破信号，并生成相应的交易信号。

## 概述

价格突破是技术分析中的一个重要概念，通常指价格在多次测试某个关键水平后，最终向一个方向强力运动。本节课的目标是构建一个自动化系统来检测这种模式。我们将通过以下步骤实现：加载并处理数据、识别价格枢轴点、定义关键区域、检测突破信号，并进行可视化验证。

---

## 数据准备与加载 📊

![](img/9652b49ca12f3d5d2d0c0d522f634faa_3.png)

首先，我们需要准备市场数据。在本例中，我们使用欧元/美元（EUR/USD）货币对的小时级别数据，时间跨度为2003年至2023年。

以下是数据加载和初步处理的代码：

![](img/9652b49ca12f3d5d2d0c0d522f634faa_5.png)

```python
import pandas as pd

# 加载CSV数据文件
df = pd.read_csv('eurusd_hourly.csv')

# 过滤掉交易量为0的蜡烛图（市场无变动）
df = df[df['volume'] > 0]

# 重置索引
df = df.reset_index(drop=True)

# 添加指数移动平均线（EMA）指标，此处以20周期为例
df['ema_20'] = df['close'].ewm(span=20, adjust=False).mean()

# 打印数据尾部以检查
print(df.tail())
```

为了在开发阶段提高代码运行效率，我们可以先对数据框进行切片，处理一部分数据。待核心函数调试无误后，再应用于整个数据集。

```python
# 切片前5000根蜡烛进行测试
df_sample = df.head(5000).copy()
```

![](img/9652b49ca12f3d5d2d0c0d522f634faa_7.png)

![](img/9652b49ca12f3d5d2d0c0d522f634faa_8.png)

---

![](img/9652b49ca12f3d5d2d0c0d522f634faa_9.png)

![](img/9652b49ca12f3d5d2d0c0d522f634faa_11.png)

## 识别价格枢轴点 🔍

![](img/9652b49ca12f3d5d2d0c0d522f634faa_12.png)

上一节我们准备好了数据，本节中我们来看看如何识别价格图表中的关键转折点，即枢轴点。枢轴点分为高点（高于相邻蜡烛）和低点（低于相邻蜡烛）。

![](img/9652b49ca12f3d5d2d0c0d522f634faa_14.png)

我们定义一个函数 `pivot_high_low` 来检测枢轴点。该函数会检查指定蜡烛是否高于（或低于）其左右两侧一定窗口内的所有邻居蜡烛。

![](img/9652b49ca12f3d5d2d0c0d522f634faa_15.png)

以下是枢轴点检测函数的代码：

![](img/9652b49ca12f3d5d2d0c0d522f634faa_17.png)

![](img/9652b49ca12f3d5d2d0c0d522f634faa_19.png)

```python
def pivot_high_low(df, candle_idx, window=10):
    """
    检测给定索引的蜡烛是否为枢轴点。
    参数:
        df: 包含‘high’, ‘low’列的数据框。
        candle_idx: 要检查的蜡烛索引。
        window: 检查左右邻居的窗口大小。
    返回:
        1: 枢轴高点
        2: 枢轴低点
        0: 非枢轴点
    """
    if candle_idx < window or candle_idx >= len(df) - window:
        return 0

    current_high = df.loc[candle_idx, 'high']
    current_low = df.loc[candle_idx, 'low']

    left_highs = df.loc[candle_idx-window:candle_idx-1, 'high']
    right_highs = df.loc[candle_idx+1:candle_idx+window, 'high']
    left_lows = df.loc[candle_idx-window:candle_idx-1, 'low']
    right_lows = df.loc[candle_idx+1:candle_idx+window, 'low']

    # 检查是否为枢轴高点
    if (current_high > left_highs.max()) and (current_high > right_highs.max()):
        return 1
    # 检查是否为枢轴低点
    elif (current_low < left_lows.min()) and (current_low < right_lows.min()):
        return 2
    else:
        return 0
```

![](img/9652b49ca12f3d5d2d0c0d522f634faa_20.png)

运行此函数，将检测结果保存到数据框的新列中，并生成用于可视化的坐标点。

```python
# 应用函数检测所有蜡烛的枢轴点
window_pivot = 10
df_sample['pivot'] = df_sample.apply(lambda row: pivot_high_low(df_sample, row.name, window=window_pivot), axis=1)

# 为可视化生成坐标点
df_sample['pivot_high_price'] = df_sample.apply(lambda row: row['high'] if row['pivot'] == 1 else None, axis=1)
df_sample['pivot_low_price'] = df_sample.apply(lambda row: row['low'] if row['pivot'] == 2 else None, axis=1)
```

---

## 定义并检测突破结构 🚀

在识别出枢轴点后，下一步是检测由这些枢轴点构成的突破结构。一个有效的突破结构需要至少三个枢轴点在一个“关键区域”内对齐，随后价格收盘突破该区域的边界。

我们定义函数 `detect_structure` 来实现这一逻辑。该函数需要考虑几个关键参数以避免未来函数偏差：
*   `lookback_candles`: 向后查看多少根蜡烛以寻找枢轴点。
*   `gap_window`: 在检测当前蜡烛时，需要跳过的近期蜡烛窗口，防止使用未来数据。
*   `zone_width`: 定义关键区域的宽度，允许枢轴点价格在一定范围内波动。

以下是突破结构检测函数的代码：

```python
def detect_structure(df, candle_idx, lookback_candles=60, gap_window=11, zone_width=10e-3):
    """
    在指定蜡烛位置检测突破结构。
    参数:
        df: 包含价格和枢轴点信息的数据框。
        candle_idx: 当前检测的蜡烛索引。
        lookback_candles: 向后寻找枢轴点的蜡烛数量。
        gap_window: 为避免前瞻偏差而跳过的窗口大小。
        zone_width: 关键区域的宽度。
    返回:
        0: 无突破
        1: 看跌突破（向下突破支撑）
        2: 看涨突破（向上突破阻力）
    """
    # 边界检查
    if candle_idx < lookback_candles + gap_window or candle_idx >= len(df) - 1:
        return 0

    # 创建本地数据切片，排除gap_window以避免前瞻偏差
    start_idx = candle_idx - lookback_candles - gap_window
    end_idx = candle_idx - gap_window
    local_df = df.loc[start_idx:end_idx].copy()

    # 获取切片内的最后三个枢轴高点和低点
    last_high_pivots = local_df[local_df['pivot'] == 1].tail(3)
    last_low_pivots = local_df[local_df['pivot'] == 2].tail(3)

    current_close = df.loc[candle_idx, 'close']

    # 1. 检查看跌突破（向下突破支撑区域）
    if len(last_low_pivots) >= 3:
        low_prices = last_low_pivots['low'].values
        avg_low = low_prices.mean()
        # 检查三个低点是否在关键区域内对齐
        support_condition = all(abs(low - avg_low) <= zone_width for low in low_prices)
        if support_condition:
            # 检查是否收盘价跌破区域下边界
            if avg_low - current_close > zone_width * 2:
                return 1

    # 2. 检查看涨突破（向上突破阻力区域）
    if len(last_high_pivots) >= 3:
        high_prices = last_high_pivots['high'].values
        avg_high = high_prices.mean()
        # 检查三个高点是否在关键区域内对齐
        resistance_condition = all(abs(high - avg_high) <= zone_width for high in high_prices)
        if resistance_condition:
            # 检查是否收盘价涨破区域上边界
            if current_close - avg_high > zone_width * 2:
                return 2

    return 0
```

![](img/9652b49ca12f3d5d2d0c0d522f634faa_22.png)

现在，我们可以将此函数应用于数据框，生成突破信号。

![](img/9652b49ca12f3d5d2d0c0d522f634faa_23.png)

![](img/9652b49ca12f3d5d2d0c0d522f634faa_25.png)

```python
# 为数据框中的每一根蜡烛检测突破结构
df_sample['detected_pattern'] = df_sample.apply(
    lambda row: detect_structure(df_sample, row.name, lookback_candles=60, gap_window=11, zone_width=10e-3),
    axis=1
)

![](img/9652b49ca12f3d5d2d0c0d522f634faa_27.png)

# 筛选出有信号的行
signals = df_sample[df_sample['detected_pattern'] != 0]
print(signals[['open', 'high', 'low', 'close', 'detected_pattern']].head())
```

![](img/9652b49ca12f3d5d2d0c0d522f634faa_28.png)

![](img/9652b49ca12f3d5d2d0c0d522f634faa_30.png)

---

![](img/9652b49ca12f3d5d2d0c0d522f634faa_31.png)

## 结果可视化与验证 👁️

![](img/9652b49ca12f3d5d2d0c0d522f634faa_33.png)

为了验证算法是否有效，我们需要将检测到的信号在价格图表上可视化。我们可以使用 `mplfinance` 或 `plotly` 等库来绘制K线图，并在检测到枢轴点和突破信号的位置添加标记。

![](img/9652b49ca12f3d5d2d0c0d522f634faa_34.png)

以下是使用 `mplfinance` 进行可视化的简化示例：

![](img/9652b49ca12f3d5d2d0c0d522f634faa_36.png)

```python
import mplfinance as mpf

![](img/9652b49ca12f3d5d2d0c0d522f634faa_38.png)

# 选择包含信号的一段数据进行绘图
plot_df = df_sample.iloc[4050:4120].copy()

![](img/9652b49ca12f3d5d2d0c0d522f634faa_40.png)

![](img/9652b49ca12f3d5d2d0c0d522f634faa_42.png)

# 准备附加的绘图内容（枢轴高点和突破信号）
apds = []
# 添加枢轴高点标记
if 'pivot_high_price' in plot_df.columns:
    highs = plot_df[plot_df['pivot_high_price'].notnull()]
    apds.append(mpf.make_addplot(highs['pivot_high_price'], type='scatter', markersize=50, marker='v', color='green'))
# 添加枢轴低点标记
if 'pivot_low_price' in plot_df.columns:
    lows = plot_df[plot_df['pivot_low_price'].notnull()]
    apds.append(mpf.make_addplot(lows['pivot_low_price'], type='scatter', markersize=50, marker='^', color='red'))
# 添加突破信号标记（例如，看涨突破用向上箭头）
bullish_breaks = plot_df[plot_df['detected_pattern'] == 2]
apds.append(mpf.make_addplot(bullish_breaks['high']*1.0005, type='scatter', markersize=80, marker='$\u2191$', color='blue'))

![](img/9652b49ca12f3d5d2d0c0d522f634faa_44.png)

# 绘制图表
mpf.plot(plot_df, type='candle', style='charles', addplot=apds, title='EUR/USD 突破信号检测', volume=False)
```

![](img/9652b49ca12f3d5d2d0c0d522f634faa_45.png)

![](img/9652b49ca12f3d5d2d0c0d522f634faa_47.png)

通过观察图表，我们可以检查算法识别的枢轴点是否合理，以及突破信号是否发生在价格确实穿越关键区域之后。例如，在索引4087附近，算法识别出一个由三个枢轴高点构成的阻力区域，并随后在价格向上突破时发出了看涨信号，之后价格也确实延续了上涨趋势。

![](img/9652b49ca12f3d5d2d0c0d522f634faa_49.png)

---

## 总结

![](img/9652b49ca12f3d5d2d0c0d522f634faa_51.png)

在本节课中，我们一起学习了如何构建一个自动化价格突破检测系统。我们从数据加载和处理开始，然后实现了枢轴点的识别算法。接着，我们定义了检测突破结构的核心函数，该函数能够寻找在关键区域内对齐的多个枢轴点，并在价格突破该区域边界时发出信号。最后，我们通过可视化方法验证了算法的有效性。

![](img/9652b49ca12f3d5d2d0c0d522f634faa_53.png)

![](img/9652b49ca12f3d5d2d0c0d522f634faa_54.png)

这个系统可以作为一个独立的指标使用，也可以与其他技术指标（如移动平均线）结合，以过滤趋势方向，从而构建更复杂的交易策略。通过调整 `zone_width`、`lookback_candles` 等参数，您可以针对不同的交易品种和时间框架优化该检测器。