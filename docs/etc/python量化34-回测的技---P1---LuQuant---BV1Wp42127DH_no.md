# Python量化34：P1：基于吞没形态的回测策略教程 📈

## 概述
在本节课中，我们将学习如何为一个基于“吞没形态”的简单交易策略编写Python回测程序。我们将使用SP500指数三年的15分钟K线数据，详细解释策略逻辑，并通过代码实现完整的回测流程，包括信号生成、可视化以及绩效评估。

---

## 策略逻辑与核心概念

上一节我们介绍了课程目标，本节中我们来看看策略的核心逻辑。

我们寻找的是K线图中的“吞没形态”。这种形态可能预示着趋势的反转。一个标准的看跌吞没形态由一根阳线（上涨蜡烛）后紧跟一根更大的阴线（下跌蜡烛）组成，且阴线的实体完全“吞没”阳线的实体。看涨吞没形态则相反。

为了增加信号的可靠性并减少噪音，我们为形态识别添加了两个额外的过滤条件：

1.  **最小吞没高度**：对于看跌吞没，指前一根阳线的收盘价与当前阴线的开盘价之间的距离。我们要求这个距离大于一个设定的最小值（例如 `0.01`）。公式表示为：
    `当前蜡烛开盘价 - 前一根蜡烛收盘价 > min_engulf_diff`
2.  **最大影线长度**：对于发出信号的蜡烛（即吞没蜡烛），其影线（或称“灯芯”、“尾巴”）长度必须小于一个最大值（例如 `0.02`）。这确保了信号蜡烛的实体足够强，影线干扰小。对于看跌吞没，我们检查上影线：
    `当前蜡烛最高价 - 当前蜡烛开盘价 < max_wick`

此外，为了获得更多交易机会，我们对经典定义进行了扩展：允许最多用N根连续的蜡烛来共同完成对前一根蜡烛的“吞没”。这个参数 `lookback_candles` 可以在程序中调整。

---

## 数据准备与加载

理解了策略逻辑后，我们需要获取并准备数据。以下是加载数据的步骤。

首先，我们导入必要的库并读取CSV格式的历史价格数据。数据时间范围为2019年至2022年，时间周期为15分钟。

![](img/2f3baf5b9116562b10484f545fa78485_1.png)

![](img/2f3baf5b9116562b10484f545fa78485_2.png)

```python
import pandas as pd

# 加载数据
df_spy = pd.read_csv(‘your_data.csv‘)
# 将时间列设置为索引，并确保为GMT时区
df_spy.index = pd.to_datetime(df_spy[‘datetime‘], utc=True)
```

为了后续处理方便，我们过滤掉没有价格波动的蜡烛（例如停盘时段），并创建一个新的数据副本。

```python
# 过滤掉最高价等于最低价的无效蜡烛
df = df_spy[df_spy[‘high‘] != df_spy[‘low‘]].copy()
# 重置索引为连续的整数，便于循环处理
df.reset_index(drop=True, inplace=True)
```

---

## 信号生成函数

数据准备就绪后，接下来我们编写核心函数来识别吞没形态信号。

以下是定义信号生成函数 `engulfing` 的步骤。该函数会检查给定索引位置的蜡烛是否形成了有效的吞没信号。

```python
def engulfing(df, l, lookback_candles=3, min_engulf_diff=0.01, max_wick=0.02):
    """
    检测吞没形态。
    参数:
        df: 包含OHLC数据的DataFrame
        l: 当前要检测的蜡烛索引
        lookback_candles: 允许完成吞没的最大蜡烛数量
        min_engulf_diff: 最小吞没高度
        max_wick: 最大允许影线长度
    返回:
        2: 看涨吞没信号
        1: 看跌吞没信号
        0: 无信号
    """
    # 检查看跌吞没形态
    for i in range(lookback_candles):
        current_idx = l - i
        prev_idx = l - i - 1
        
        if current_idx < 1 or prev_idx < 0:
            break
            
        # 条件1: 当前为阴线，前一根为阳线
        condition1 = (df.at[current_idx, ‘open‘] > df.at[current_idx, ‘close‘]) and \
                     (df.at[prev_idx, ‘close‘] > df.at[prev_idx, ‘open‘])
        # 条件2: 满足最小吞没高度
        condition2 = (df.at[current_idx, ‘open‘] - df.at[prev_idx, ‘close‘]) > min_engulf_diff
        # 条件3: 满足最大影线限制（上影线）
        condition3 = (df.at[current_idx, ‘high‘] - df.at[current_idx, ‘open‘]) < max_wick
        # 条件4: 检查是否在允许的蜡烛数内完成了吞没（收盘价低于前一根的开盘价）
        condition4 = df.at[current_idx, ‘close‘] < df.at[prev_idx, ‘open‘]
        
        if condition1 and condition2 and condition3 and condition4:
            return 1  # 看跌信号
    
    # 检查看涨吞没形态（逻辑与看跌对称）
    for i in range(lookback_candles):
        current_idx = l - i
        prev_idx = l - i - 1
        
        if current_idx < 1 or prev_idx < 0:
            break
            
        condition1 = (df.at[current_idx, ‘close‘] > df.at[current_idx, ‘open‘]) and \
                     (df.at[prev_idx, ‘open‘] > df.at[prev_idx, ‘close‘])
        condition2 = (df.at[prev_idx, ‘close‘] - df.at[current_idx, ‘open‘]) > min_engulf_diff
        condition3 = (df.at[current_idx, ‘open‘] - df.at[current_idx, ‘low‘]) < max_wick
        condition4 = df.at[current_idx, ‘close‘] > df.at[prev_idx, ‘open‘]
        
        if condition1 and condition2 and condition3 and condition4:
            return 2  # 看涨信号
    
    return 0  # 无信号
```

然后，我们将这个函数应用到整个数据集，生成信号列。

```python
# 为每一行计算信号
signals = []
for i in range(len(df)):
    sig = engulfing(df, i, lookback_candles=3, min_engulf_diff=0.01, max_wick=0.02)
    signals.append(sig)

df[‘signal‘] = signals
```

---

## 信号可视化

生成信号后，最好能直观地检查它们是否出现在预期的K线位置。

我们可以使用绘图库（如 `mplfinance` 或 `plotly`）将K线图和信号标记绘制出来。看涨信号在蜡烛下方用紫色圆点标记，看跌信号在蜡烛上方标记。

```python
import mplfinance as mpf

# 选取一段数据用于可视化
plot_df = df.iloc[4040:4200].copy()
# 准备标记数据
markers = []
for idx, row in plot_df.iterrows():
    if row[‘signal‘] == 2:
        markers.append((idx, row[‘low‘], ‘^‘, ‘purple‘)) # 看涨，下方标记
    elif row[‘signal‘] == 1:
        markers.append((idx, row[‘high‘], ‘v‘, ‘purple‘)) # 看跌，上方标记

# 绘制图表
apd = [mpf.make_addplot([marker[1] for marker in markers], type=‘scatter‘, markersize=50, marker=marker[2], color=marker[3]) for marker in markers]
mpf.plot(plot_df, type=‘candle‘, addplot=apd, style=‘charles‘)
```
通过可视化，我们可以初步判断信号是否合理，例如在明显的反转点附近出现。

---

## 回测与绩效分析

确认信号生成正确后，最关键的一步是进行回测，量化策略的潜在表现。

我们将实现一个简单的回测引擎。它根据信号进行买卖，并设置止损和止盈规则。我们测试三种不同的风险控制方式：

1.  **固定止损止盈**：例如，止损为3个点，止盈为止损的2倍。
2.  **基于ATR的动态止损**：使用平均真实波幅（ATR）来设定更适应市场波动的止损。
3.  **追踪止损**：当利润达到一定水平后，止损位跟随价格移动，以保护浮动盈利。

以下是回测的核心循环结构示例：

```python
# 初始化变量
capital = 1000
position = None
entry_price = 0
stop_loss = 0
take_profit = 0
trailing_stop = False
trailing_distance = 5

equity_curve = [capital]

for i in range(len(df)):
    current_price = df.at[i, ‘close‘]
    atr = df.at[i, ‘atr‘] # 假设已计算好ATR列
    
    # 平仓逻辑（检查止损、止盈、追踪止损）
    if position == ‘long‘:
        if current_price <= stop_loss or current_price >= take_profit:
            # 执行平仓，更新资金
            position = None
        if trailing_stop and current_price > entry_price:
            # 更新追踪止损位
            stop_loss = max(stop_loss, current_price - trailing_distance)
    
    # 开仓逻辑
    if position is None and df.at[i, ‘signal‘] == 2: # 收到看涨信号
        position = ‘long‘
        entry_price = current_price
        # 设置初始风控
        # 方式1: 固定
        # stop_loss = entry_price - 3
        # take_profit = entry_price + 6
        # 方式2: 基于ATR
        stop_loss = entry_price - 2 * atr
        take_profit = entry_price + 3 * atr
        
    # 记录当前权益
    equity_curve.append(capital)
```

运行回测后，我们计算关键绩效指标：

*   **总回报率**：策略最终收益百分比。
*   **胜率**：盈利交易次数占总交易次数的比例。
*   **最大回撤**：资金曲线从峰值到谷底的最大跌幅，衡量策略风险。

例如，使用固定止损时，策略三年回报可能为213%，但最大回撤高达-60%，风险很大。而使用基于ATR的动态止损或追踪止损后，回报可能变为89%或422%，最大回撤也有所改善，但仍在-50%左右，表明策略存在较大波动风险。

---

## 策略优化与改进建议

看到初步的回测结果，我们意识到原始策略存在风险。本节我们来探讨一些可能的优化方向。

回测结果显示最大回撤较大，这意味着策略在某些时期会经历大幅亏损。为了提升策略的稳健性，可以考虑以下改进措施：

以下是几个关键的优化思路：

*   **结合趋势过滤器**：仅在主要趋势方向上进行交易。例如，在价格高于某条移动平均线时只做多，低于时只做空，避免逆势交易。
*   **多策略并行**：不要依赖单一策略。可以同时运行2-3个相关性较低的不同策略，当一个策略失效时，其他策略可能盈利，从而平滑整体资金曲线。
*   **参数优化与谨慎过拟合**：对 `min_engulf_diff`、`max_wick`、`lookback_candles` 等参数进行测试，但需注意避免在历史数据上过度优化。
*   **手动确认**：此策略生成的信号也可作为手动交易的参考。当图表中出现符合条件的小影线吞没蜡烛时，交易者可以结合其他技术分析工具进行决策。

---

## 总结

本节课中我们一起学习了如何为一个基于吞没形态的交易策略构建完整的Python回测系统。

我们从策略的逻辑定义开始，引入了**最小吞没高度**和**最大影线长度**两个核心过滤条件，并允许使用多根蜡烛完成吞没。接着，我们完成了数据加载、信号生成函数的编写，并通过可视化初步验证了信号。然后，我们实现了回测引擎，测试了固定止损、ATR动态止损和追踪止损三种风控方式，并分析了它们的绩效表现。最后，我们讨论了策略风险（如较大的最大回撤）并提出了结合趋势过滤、多策略并行等优化建议。

![](img/2f3baf5b9116562b10484f545fa78485_4.png)

本策略代码提供了一个基础框架，你可以在此基础上融入自己的交易想法，进行更深入的测试和优化。记住，没有任何策略能永远有效，风险管理永远是交易的核心。