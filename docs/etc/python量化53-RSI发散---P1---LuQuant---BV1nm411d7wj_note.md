# Python量化53：RSI背离 - P1 - LuQuant

![](img/f4373d15be1cd7de4905540e71a265b6_1.png)

在本节课中，我们将学习如何用Python检测和编码RSI背离。RSI背离是一种常见的技术分析模式，当价格走势与RSI指标走势不一致时出现，可能预示着潜在的趋势反转。我们将通过两种方法来识别这种背离：一种是基于多个极值点的线性拟合，另一种是简单地比较两个连续的极值点。

## 概述
我们将从读取数据、计算RSI指标开始，然后编写算法来识别价格和RSI的枢轴点（即局部高点和低点）。接着，我们会实现两种不同的背离检测逻辑，并将结果可视化。最后，我们会讨论如何将这些信号整合到交易策略中。

---

## 1：数据准备与RSI计算

![](img/f4373d15be1cd7de4905540e71a265b6_3.png)

首先，我们需要导入必要的库并加载价格数据。我们将使用Pandas进行数据处理，并使用`pandas_ta`库来计算RSI指标。

```python
import pandas as pd
import numpy as np
import pandas_ta as ta

# 读取数据
df = pd.read_csv('eurusd_4h.csv')
# 清理数据，例如移除成交量为零的行
df = df[df['volume'] != 0].reset_index(drop=True)
print(df.head(10))
```

接下来，我们计算RSI指标。这里提供了两种方法：一种是使用`pandas_ta`库（默认使用指数移动平均），另一种是直接根据RSI公式计算（使用简单移动平均）。你可以选择其中一种进行实验。

```python
# 方法一：使用 pandas_ta 库计算RSI（指数移动平均）
df['rsi_ta'] = ta.rsi(df['close'], length=14)

# 方法二：自定义RSI计算函数（简单移动平均）
def calculate_rsi(prices, period=14):
    delta = prices.diff()
    gain = (delta.where(delta > 0, 0)).rolling(window=period).mean()
    loss = (-delta.where(delta < 0, 0)).rolling(window=period).mean()
    rs = gain / loss
    rsi = 100 - (100 / (1 + rs))
    return rsi

df['rsi_custom'] = calculate_rsi(df['close'], period=14)
```

现在，我们已经将RSI值添加到了数据框中。可以绘制一小段数据来检查价格和RSI的走势。

![](img/f4373d15be1cd7de4905540e71a265b6_5.png)

---

![](img/f4373d15be1cd7de4905540e71a265b6_7.png)

## 2：识别枢轴点（高点和低点）

上一节我们计算了RSI，本节中我们来看看如何识别价格和RSI图表中的局部高点和低点，即“枢轴点”。我们将编写一个函数，通过比较一根蜡烛与其前后若干根邻居蜡烛的高低来判断它是否为枢轴点。

以下是识别价格数据枢轴点的函数：

```python
def pivot_id(df, l, n1, n2):
    """
    判断给定索引 l 的蜡烛是否为枢轴点（高点或低点）。
    n1: 向前看的蜡烛数
    n2: 向后看的蜡烛数
    返回: 0（非枢轴点），1（低点），2（高点），3（同时是高点和低点，理论上罕见）
    """
    if l - n1 < 0 or l + n2 >= len(df):
        return 0

    pivot_low = 1
    pivot_high = 1
    for i in range(l - n1, l + n2 + 1):
        if df.loc[i, 'low'] < df.loc[l, 'low']:
            pivot_low = 0
        if df.loc[i, 'high'] > df.loc[l, 'high']:
            pivot_high = 0

    if pivot_low and pivot_high:
        return 3
    elif pivot_low:
        return 1
    elif pivot_high:
        return 2
    else:
        return 0
```

我们同样需要为RSI值编写一个类似的函数，因为RSI是单一数值序列。

```python
def pivot_id_rsi(df, l, n1, n2):
    """
    判断给定索引 l 的RSI值是否为枢轴点（高点或低点）。
    """
    if l - n1 < 0 or l + n2 >= len(df):
        return 0

    pivot_low = 1
    pivot_high = 1
    for i in range(l - n1, l + n2 + 1):
        if df.loc[i, 'rsi'] < df.loc[l, 'rsi']: # 假设RSI列名为'rsi'
            pivot_low = 0
        if df.loc[i, 'rsi'] > df.loc[l, 'rsi']:
            pivot_high = 0

    if pivot_low and pivot_high:
        return 3
    elif pivot_low:
        return 1
    elif pivot_high:
        return 2
    else:
        return 0
```

现在，我们可以将这些函数应用到整个数据框，为每根蜡烛标记其价格和RSI的枢轴点类型。

```python
# 应用函数，识别价格枢轴点
n1 = 5
n2 = 5
df['pivot'] = df.index.map(lambda x: pivot_id(df, x, n1, n2))

# 应用函数，识别RSI枢轴点
df['pivot_rsi'] = df.index.map(lambda x: pivot_id_rsi(df, x, n1, n2))
```

为了可视化这些点，我们可以创建辅助列来存储绘图时使用的坐标。

```python
def point_pos_price(df):
    """
    根据价格枢轴点类型，返回绘图点的y坐标。
    """
    if df['pivot'] == 1:
        return df['low'] - 1e-3
    elif df['pivot'] == 2:
        return df['high'] + 1e-3
    else:
        return np.nan

def point_pos_rsi(df):
    """
    根据RSI枢轴点类型，返回绘图点的y坐标。
    """
    if df['pivot_rsi'] == 1:
        return df['rsi'] - 1
    elif df['pivot_rsi'] == 2:
        return df['rsi'] + 1
    else:
        return np.nan

df['point_price'] = df.apply(point_pos_price, axis=1)
df['point_rsi'] = df.apply(point_pos_rsi, axis=1)
```

运行绘图代码，我们可以在图表上看到标记出的高点和低点，从而验证算法是否正常工作。

---

## 3：方法一 —— 基于线性拟合的背离检测

上一节我们成功识别了枢轴点，本节中我们来看看第一种背离检测方法：对多个极值点进行线性拟合，并比较价格趋势线与RSI趋势线的斜率。

其核心思想是：
1.  对于当前蜡烛，回顾其之前一定数量（例如30根）的蜡烛。
2.  在该回顾窗口内，找出所有的价格高点和低点，以及RSI的高点和低点。
3.  分别对价格的高点、低点以及RSI的高点、低点进行线性拟合，得到四条趋势线的斜率。
4.  首先判断价格趋势：如果价格高点和低点的拟合斜率都为正，则为上升趋势；如果都为负，则为下降趋势。
5.  然后，在确定的趋势下检查RSI：
    *   在价格**上升趋势**中，我们关注**RSI的高点**。如果其拟合斜率为负（即RSI高点在降低），则形成**看跌背离**（Bearish Divergence）。
    *   在价格**下降趋势**中，我们关注**RSI的低点**。如果其拟合斜率为正（即RSI低点在升高），则形成**看涨背离**（Bullish Divergence）。

以下是实现此逻辑的函数：

```python
from scipy import stats

def divergence_signal(df, candle_index, lookback=30):
    """
    基于线性拟合的背离检测。
    返回: 0（无背离），1（看跌背离），2（看涨背离）
    """
    start_index = max(0, candle_index - lookback)
    end_index = candle_index

    window_df = df.iloc[start_index:end_index]

    # 收集价格和RSI的极值点坐标 (索引, 值)
    price_highs = []
    price_lows = []
    rsi_highs = []
    rsi_lows = []

    for i in range(len(window_df)):
        idx = window_df.index[i]
        # 价格高点
        if df.loc[idx, 'pivot'] == 2:
            price_highs.append([idx, df.loc[idx, 'high']])
        # 价格低点
        if df.loc[idx, 'pivot'] == 1:
            price_lows.append([idx, df.loc[idx, 'low']])
        # RSI高点
        if df.loc[idx, 'pivot_rsi'] == 2:
            rsi_highs.append([idx, df.loc[idx, 'rsi']])
        # RSI低点
        if df.loc[idx, 'pivot_rsi'] == 1:
            rsi_lows.append([idx, df.loc[idx, 'rsi']])

    # 转换为numpy数组以便计算
    price_highs = np.array(price_highs)
    price_lows = np.array(price_lows)
    rsi_highs = np.array(rsi_highs)
    rsi_lows = np.array(rsi_lows)

    # 检查是否有足够点进行拟合（至少需要2个点）
    if len(price_highs) < 2 or len(price_lows) < 2 or len(rsi_highs) < 2 or len(rsi_lows) < 2:
        return 0

    # 线性拟合
    slope_price_high, _, _, _, _ = stats.linregress(price_highs[:, 0], price_highs[:, 1])
    slope_price_low, _, _, _, _ = stats.linregress(price_lows[:, 0], price_lows[:, 1])
    slope_rsi_high, _, _, _, _ = stats.linregress(rsi_highs[:, 0], rsi_highs[:, 1])
    slope_rsi_low, _, _, _, _ = stats.linregress(rsi_lows[:, 0], rsi_lows[:, 1])

    # 判断价格趋势
    price_up_trend = slope_price_low > 1e-4 and slope_price_high > 1e-4
    price_down_trend = slope_price_low < -1e-4 and slope_price_high < -1e-4

    # 检测背离
    if price_up_trend and slope_rsi_high < -0.1: # 上升趋势中，RSI高点下降 -> 看跌背离
        return 1
    elif price_down_trend and slope_rsi_low > 0.1: # 下降趋势中，RSI低点上升 -> 看涨背离
        return 2
    else:
        return 0
```

现在，我们可以将这个函数应用到数据框的每一行，生成背离信号列。请注意，由于计算量较大，建议先在小范围数据上测试。

```python
# 选取部分数据进行测试，例如前500根蜡烛
test_df = df.iloc[:500].copy()
lookback_period = 30

# 应用背离检测函数
test_df['div_signal_1'] = test_df.index.map(lambda x: divergence_signal(test_df, x, lookback=lookback_period))

# 查看信号分布
print(test_df['div_signal_1'].value_counts())
```

---

## 4：方法二 —— 基于两点比较的简化背离检测

上一节我们介绍了基于拟合的复杂方法，本节中我们来看看一种更简单、更常见的背离检测方法：只比较最近的两个极值点。

以下是该方法的逻辑步骤：
1.  对于当前蜡烛，回顾其之前一定数量的蜡烛。
2.  在回顾窗口内，找到价格和RSI的**最近两个高点**和**最近两个低点**。
3.  判断价格趋势：
    *   如果价格的**最近两个高点**在升高，且**最近两个低点**也在升高，则视为上升趋势。
    *   如果价格的**最近两个高点**在降低，且**最近两个低点**也在降低，则视为下降趋势。
4.  在确定的趋势下检查RSI：
    *   在价格**上升趋势**中，检查**RSI的最近两个高点**。如果第二个高点低于第一个高点，则形成**看跌背离**。
    *   在价格**下降趋势**中，检查**RSI的最近两个低点**。如果第二个低点高于第一个低点，则形成**看涨背离**。

以下是实现此逻辑的函数：

```python
def divergence_signal_simple(df, candle_index, lookback=30):
    """
    基于两点比较的简化背离检测。
    返回: 0（无背离），1（看跌背离），2（看涨背离）
    """
    start_index = max(0, candle_index - lookback)
    end_index = candle_index

    window_df = df.iloc[start_index:end_index]

    # 寻找价格和RSI的极值点
    price_highs = window_df[window_df['pivot'] == 2][['high']].tail(2)
    price_lows = window_df[window_df['pivot'] == 1][['low']].tail(2)
    rsi_highs = window_df[window_df['pivot_rsi'] == 2][['rsi']].tail(2)
    rsi_lows = window_df[window_df['pivot_rsi'] == 1][['rsi']].tail(2)

    # 检查是否有足够点（至少需要2个）
    if len(price_highs) < 2 or len(price_lows) < 2 or len(rsi_highs) < 2 or len(rsi_lows) < 2:
        return 0

    # 获取具体数值
    ph1, ph2 = price_highs.iloc[0]['high'], price_highs.iloc[1]['high']
    pl1, pl2 = price_lows.iloc[0]['low'], price_lows.iloc[1]['low']
    rh1, rh2 = rsi_highs.iloc[0]['rsi'], rsi_highs.iloc[1]['rsi']
    rl1, rl2 = rsi_lows.iloc[0]['rsi'], rsi_lows.iloc[1]['rsi']

    # 判断价格趋势
    price_up = (ph2 > ph1) and (pl2 > pl1)
    price_down = (ph2 < ph1) and (pl2 < pl1)

    # 检测背离
    if price_up and (rh2 < rh1):
        return 1  # 看跌背离
    elif price_down and (rl2 > rl1):
        return 2  # 看涨背离
    else:
        return 0
```

同样地，我们将这个函数应用到测试数据上。

```python
test_df['div_signal_2'] = test_df.index.map(lambda x: divergence_signal_simple(test_df, x, lookback=lookback_period))
print(test_df['div_signal_2'].value_counts())
```

现在，我们可以对比两种方法产生的信号。通常，基于两点比较的方法计算更快，信号更频繁；而基于拟合的方法更平滑，可能过滤掉一些噪音。

---

## 5：信号分析与策略思路

在本节中，我们将分析生成的背离信号，并探讨如何将其应用于交易策略。

首先，我们可以筛选出有信号的蜡烛，观察其分布。

![](img/f4373d15be1cd7de4905540e71a265b6_9.png)

```python
# 查看方法一产生的看跌背离信号
bearish_div_1 = test_df[test_df['div_signal_1'] == 1]
print("方法一检测到的看跌背离数量：", len(bearish_div_1))
print("出现看跌背离的索引示例：", bearish_div_1.index.tolist()[:10])

![](img/f4373d15be1cd7de4905540e71a265b6_11.png)

# 查看方法二产生的看涨背离信号
bullish_div_2 = test_df[test_df['div_signal_2'] == 2]
print("\n方法二检测到的看涨背离数量：", len(bullish_div_2))
print("出现看涨背离的索引示例：", bullish_div_2.index.tolist()[:10])
```

你会发现，信号通常会在一系列连续的蜡烛上出现，而不是孤立的一点。这为交易者提供了数小时甚至数天的决策窗口来选择入场时机。

**策略整合思路：**
RSI背离本身是一个预警信号，而非精确的入场指令。在实际交易中，通常需要结合其他技术分析工具进行确认：
1.  **价格形态**：在背离信号出现的位置，寻找看涨（如锤头线、启明星）或看跌（如射击之星、黄昏之星）的K线形态。
2.  **支撑与阻力**：观察背离是否发生在关键的支撑位或阻力位附近。例如，看涨背离出现在强支撑位，其反转成功的概率可能更高。
3.  **趋势线突破**：等待价格突破短期的下降趋势线（在看涨背离后）或上升趋势线（在看跌背离后）再入场。
4.  **风险管理**：始终设置止损。例如，在做多看涨背离时，将止损设置在近期低点下方；止盈目标可以设为止损距离的1.5倍或2倍。

**示例分析：**
假设我们在索引85附近检测到一个看跌背离（价格创新高，RSI高点降低）。我们可以：
*   **确认**：检查该位置是否有阻力位、是否出现看跌K线形态。
*   **入场**：如果确认信号成立，可以考虑在下一根蜡烛开盘时入场做空，或等待价格跌破某短期上升趋势线后入场。
*   **风控**：将止损设置在价格高点上方。止盈可以基于风险回报比来设定。

---

## 总结

本节课中我们一起学习了如何用Python实现RSI背离的检测。我们从数据准备和RSI计算开始，然后编写了识别价格与RSI枢轴点的函数。我们重点探讨了两种背离检测方法：第一种通过对多个极值点进行线性拟合来比较趋势斜率；第二种则通过简单比较最近两个极值点的水平来判断。最后，我们讨论了如何分析这些信号并将其与其他技术工具结合，以构建更稳健的交易策略。

![](img/f4373d15be1cd7de4905540e71a265b6_13.png)

![](img/f4373d15be1cd7de4905540e71a265b6_14.png)

记住，任何单一指标都有其局限性。RSI背离是一个有用的工具，但将其与市场结构、价格行为以及其他指标结合使用，才能提高交易决策的胜率。