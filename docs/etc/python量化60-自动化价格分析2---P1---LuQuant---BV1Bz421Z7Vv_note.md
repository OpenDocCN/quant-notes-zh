# Python量化60：自动化价格分析2 - P1 - 吞噬蜡烛模式统计验证 📊

在本教程中，我们将学习如何利用Python对金融市场中的“吞噬蜡烛”模式进行统计验证。我们将获取欧元/美元的历史数据，编写代码自动识别看涨和看跌吞噬模式，并分析这些模式出现后，价格在短期内是否表现出预期的走势。通过将模式预测结果与随机预测进行对比，我们可以评估吞噬蜡烛作为交易信号的有效性。

---

![](img/9aa5231d3e7fd94c4efea8a06466aabd_1.png)

## 1. 理解吞噬蜡烛模式 🕯️

吞噬蜡烛模式是技术分析中常见的反转形态，通常预示着当前趋势可能发生转变。它主要分为两种类型：看涨吞噬模式和看跌吞噬模式。

上一节我们介绍了本教程的目标，本节中我们来看看吞噬蜡烛的具体定义。

以下是两种吞噬模式的核心定义：

*   **看涨吞噬模式 (Bullish Engulfing Pattern)**:
    *   **形态**：出现在下降趋势中。第一根是实体较短的**下降趋势蜡烛**（阴线）。第二根是实体较长的**上升趋势蜡烛**（阳线）。
    *   **条件**：第二根蜡烛的**开盘价** `<=` 第一根蜡烛的**收盘价**，且第二根蜡烛的**收盘价** `>=` 第一根蜡烛的**开盘价**。直观上，第二根阳线完全“吞噬”了前一根阴线。
    *   **预期**：该形态表明买方力量压倒卖方，**预示价格可能从此开始上涨**。

*   **看跌吞噬模式 (Bearish Engulfing Pattern)**:
    *   **形态**：出现在上升趋势中。第一根是实体较短的**上升趋势蜡烛**（阳线）。第二根是实体较长的**下降趋势蜡烛**（阴线）。
    *   **条件**：第二根蜡烛的**开盘价** `>=` 第一根蜡烛的**收盘价**，且第二根蜡烛的**收盘价** `<=` 第一根蜡烛的**开盘价**。直观上，第二根阴线完全“吞噬”了前一根阳线。
    *   **预期**：该形态表明卖方力量压倒买方，**预示价格可能从此开始下跌**。

我们的验证思路是：在历史数据中识别这些模式，然后检查模式出现后未来N根蜡烛的价格走势，是否与模式预测的方向一致。

---

## 2. 数据准备与加载 📈

为了进行统计分析，我们首先需要获取并加载历史价格数据。本节我们将使用`pandas`库来读取和处理数据。

![](img/9aa5231d3e7fd94c4efea8a06466aabd_3.png)

以下是数据加载步骤：

```python
import pandas as pd

# 加载历史价格数据文件（例如CSV格式）
df = pd.read_csv(‘your_historical_data.csv‘)

# 查看数据框的尾部，了解数据结构和内容
print(df.tail())
print(f“数据总行数（蜡烛数）: {len(df)}“)
```

通常，数据应包含以下列：`Timestamp`（时间戳）, `Open`（开盘价）, `High`（最高价）, `Low`（最低价）, `Close`（收盘价）, `Volume`（成交量）。每一行代表一个交易周期（例如1天）的K线数据。

---

## 3. 识别吞噬蜡烛信号 🔍

现在，我们将编写一个核心函数，遍历历史数据，自动识别出看涨和看跌吞噬蜡烛，并为它们打上标签。

上一节我们准备好了数据，本节中我们来实现信号识别函数。

以下是`identify_engulfing_signals`函数的关键步骤：

1.  **初始化**：创建与数据长度相同的列表，用于存储信号（0:无信号，1:看跌吞噬，2:看涨吞噬）和K线实体大小。
2.  **定义过滤条件**：设置一个最小实体差异（如`0.0030`），以过滤掉实体过小、趋势不明确的“十字星”K线，确保信号的强度。
3.  **遍历判断**：从第二根K线开始循环，判断当前K线与前一根K线是否构成吞噬形态。
    *   **看跌吞噬判断逻辑**：
        *   前一根K线为阳线（`close > open`）且实体足够大。
        *   当前K线为阴线（`close < open`）且实体足够大。
        *   当前K线开盘价 `>=` 前一根K线收盘价（允许微小偏差）。
        *   当前K线收盘价 `<=` 前一根K线开盘价（允许微小偏差）。
    *   **看涨吞噬判断逻辑**：与看跌吞噬对称。
4.  **存储信号**：将符合条件的K线位置标记为1或2。

```python
def identify_engulfing_signals(dataframe, min_body_diff=0.0030):
    length = len(dataframe)
    highs = list(dataframe[‘High‘])
    lows = list(dataframe[‘Low‘])
    closes = list(dataframe[‘Close‘])
    opens = list(dataframe[‘Open‘])

    signals = [0] * length
    body_diffs = [0] * length

    for i in range(1, length):
        # 计算当前K线实体大小（绝对值）
        body_diff = abs(opens[i] - closes[i])
        body_diffs[i] = body_diff

        # 计算前一根K线实体大小
        prev_body_diff = abs(opens[i-1] - closes[i-1])

        # 忽略实体过小的K线
        if body_diff < min_body_diff or prev_body_diff < min_body_diff:
            continue

        # 判断看跌吞噬模式
        if (opens[i] >= closes[i-1] and # 当前开盘高于或等于前收盘
            closes[i] <= opens[i-1] and # 当前收盘低于或等于前开盘
            closes[i] < opens[i] and    # 当前为阴线
            closes[i-1] > opens[i-1]):  # 前一根为阳线
            signals[i] = 1 # 标记为看跌吞噬

        # 判断看涨吞噬模式
        elif (opens[i] <= closes[i-1] and # 当前开盘低于或等于前收盘
              closes[i] >= opens[i-1] and # 当前收盘高于或等于前开盘
              closes[i] > opens[i] and    # 当前为阳线
              closes[i-1] < opens[i-1]):  # 前一根为阴线
            signals[i] = 2 # 标记为看涨吞噬

    return signals

# 应用函数，将信号列添加到数据框中
df[‘Signal‘] = identify_engulfing_signals(df)
# 统计信号数量
bearish_count = (df[‘Signal‘] == 1).sum()
bullish_count = (df[‘Signal‘] == 2).sum()
print(f“看跌吞噬信号数: {bearish_count}“)
print(f“看涨吞噬信号数: {bullish_count}“)
```

---

## 4. 定义验证目标与评估函数 🎯

识别出信号后，我们需要定义如何验证其有效性。我们的方法是：检查吞噬信号出现后，未来N根蜡烛的价格是否朝着预期方向移动了至少X个点。

上一节我们学会了识别信号，本节中我们来定义如何验证这些信号的预测效果。

以下是目标评估函数`evaluate_target`的逻辑：

![](img/9aa5231d3e7fd94c4efea8a06466aabd_5.png)

1.  **输入参数**：数据框、信号列、要观察的未来蜡烛数量（`bars_to_consider`）、价格需要移动的最小点数（`point_limit`）。
2.  **遍历信号**：找到所有标记为吞噬信号（1或2）的K线。
3.  **检查价格行为**：
    *   对于**看跌吞噬信号（1）**：获取吞噬信号K线的收盘价。检查随后`bars_to_consider`根K线的最低价。计算收盘价与这些最低价之间的差值。**如果任何一个差值（即下跌幅度）超过`point_limit`**，则认为该次预测“成功”（趋势向下，标记为1）。
    *   对于**看涨吞噬信号（2）**：逻辑对称，检查随后`bars_to_consider`根K线的最高价，看上涨幅度是否超过`point_limit`。成功则标记为2。
    *   如果价格波动未达到`point_limit`，则标记为0（无明确趋势）。
4.  **返回结果**：返回一个与数据框等长的列表，记录每次信号对应的真实短期趋势（0,1,2）。

![](img/9aa5231d3e7fd94c4efea8a06466aabd_6.png)

```python
def evaluate_target(dataframe, signal_series, bars_to_consider=3, point_limit=0.0030):
    length = len(dataframe)
    trends = [0] * length
    highs = list(dataframe[‘High‘])
    lows = list(dataframe[‘Low‘])
    closes = list(dataframe[‘Close‘])

    for i in range(length - bars_to_consider):
        signal = signal_series[i]
        if signal == 0:
            continue # 忽略无信号的位置

        close_price = closes[i]

        if signal == 1: # 看跌吞噬，期待价格下跌
            # 查看后续 bars_to_consider 根蜡烛的最低价
            subsequent_lows = lows[i+1 : i+1+bars_to_consider]
            # 计算收盘价与后续最低价的差值（正数表示下跌了多少）
            diffs = [close_price - low for low in subsequent_lows]
            # 如果任何一个下跌幅度超过点限，则视为成功预测下跌
            if any(diff > point_limit for diff in diffs):
                trends[i] = 1

        elif signal == 2: # 看涨吞噬，期待价格上涨
            # 查看后续 bars_to_consider 根蜡烛的最高价
            subsequent_highs = highs[i+1 : i+1+bars_to_consider]
            # 计算后续最高价与收盘价的差值（正数表示上涨了多少）
            diffs = [high - close_price for high in subsequent_highs]
            # 如果任何一个上涨幅度超过点限，则视为成功预测上涨
            if any(diff > point_limit for diff in diffs):
                trends[i] = 2

    return trends

![](img/9aa5231d3e7fd94c4efea8a06466aabd_8.png)

# 应用评估函数，生成真实趋势列
df[‘Real_Trend‘] = evaluate_target(df, df[‘Signal‘], bars_to_consider=3, point_limit=0.0030)
```

![](img/9aa5231d3e7fd94c4efea8a06466aabd_9.png)

---

## 5. 计算预测精度与统计分析 📊

现在，我们有了预测信号（`Signal`）和验证后的真实趋势（`Real_Trend`），可以计算吞噬蜡烛模式的预测准确率了。

上一节我们得到了信号的验证结果，本节中我们来进行最终的精度计算与统计分析。

以下是计算精度和对比分析的步骤：

1.  **创建结果列**：对比`Signal`和`Real_Trend`，只有当两者方向一致（同为1或同为2）时，才认为预测正确。
2.  **计算准确率**：
    *   对于看跌吞噬：`预测正确的看跌次数 / 总的看跌信号次数`
    *   对于看涨吞噬：`预测正确的看涨次数 / 总的看涨信号次数`
3.  **与随机预测对比**：为了确认吞噬模式是否真的有效，我们生成一个随机信号序列，用同样的方法计算其“准确率”。如果吞噬模式的准确率显著高于随机预测（通常为50%左右），则说明其具有统计意义。

```python
import numpy as np

# 初始化结果列，0表示预测错误或未预测
df[‘Result‘] = 0

# 条件1: 看跌吞噬预测正确 (Signal=1 且 Real_Trend=1)
condition_bearish_correct = (df[‘Signal‘] == 1) & (df[‘Real_Trend‘] == 1)
df.loc[condition_bearish_correct, ‘Result‘] = 1

# 条件2: 看涨吞噬预测正确 (Signal=2 且 Real_Trend=2)
condition_bullish_correct = (df[‘Signal‘] == 2) & (df[‘Real_Trend‘] == 2)
df.loc[condition_bullish_correct, ‘Result‘] = 2

# 计算看跌吞噬准确率
bearish_signals = (df[‘Signal‘] == 1).sum()
bearish_correct = (df[‘Result‘] == 1).sum()
bearish_accuracy = bearish_correct / bearish_signals if bearish_signals > 0 else 0
print(f“看跌吞噬模式准确率: {bearish_accuracy:.2%}“)

# 计算看涨吞噬准确率
bullish_signals = (df[‘Signal‘] == 2).sum()
bullish_correct = (df[‘Result‘] == 2).sum()
bullish_accuracy = bullish_correct / bullish_signals if bullish_signals > 0 else 0
print(f“看涨吞噬模式准确率: {bullish_accuracy:.2%}“)

# --- 随机预测对比 ---
import random
# 生成随机信号（0,1,2），其中1和2的概率各占可能的一半（忽略0）
random_signals = [random.choice([1, 2]) if random.random() > 0.5 else 0 for _ in range(len(df))]
df[‘Random_Signal‘] = random_signals
# 评估随机信号的“真实趋势”
df[‘Random_Trend‘] = evaluate_target(df, df[‘Random_Signal‘], bars_to_consider=3, point_limit=0.0030)
# 用同样的方法计算随机预测的“准确率”
random_accuracy = np.mean((df[‘Random_Signal‘] == df[‘Random_Trend‘]) & (df[‘Random_Signal‘] != 0))
print(f“随机预测准确率: {random_accuracy:.2%}“)
```

**关键点**：通过调整`bars_to_consider`（观察窗口）和`point_limit`（盈利目标），可以测试不同交易假设下的策略表现。吞噬模式的准确率应稳定地高于随机基线，才能证明其作为交易信号的潜在价值。

---

## 6. 可视化与误报分析 📉

分析误报（False Positive，即信号出现但预测方向错误）的案例，有助于理解策略的弱点。我们可以将误报发生前后的K线图绘制出来，进行直观检查。

以下是进行误报分析的代码示例：

```python
import matplotlib.pyplot as plt
import mplfinance as mpf # 一个专业的K线图绘制库

# 1. 找到误报的索引位置
# 例如，看涨吞噬信号（Signal=2）但真实趋势是下跌（Real_Trend=1）或盘整（0）
false_bullish = df[(df[‘Signal‘] == 2) & (df[‘Real_Trend‘] != 2)].index.tolist()
# 同理可查找看跌吞噬的误报
false_bearish = df[(df[‘Signal‘] == 1) & (df[‘Real_Trend‘] != 1)].index.tolist()

print(f“看涨吞噬误报次数: {len(false_bullish)}“)
print(f“看跌吞噬误报次数: {len(false_bearish)}“)

# 2. 绘制特定误报案例的K线图
if len(false_bullish) > 0:
    sample_idx = false_bullish[0] # 取第一个误报案例
    start_idx = max(0, sample_idx - 20) # 看信号前20根K线
    end_idx = min(len(df), sample_idx + 20) # 看信号后20根K线
    plot_df = df.iloc[start_idx:end_idx].copy()
    # 设置绘图参数，标记出吞噬信号的位置
    apds = [
        mpf.make_addplot((plot_df[‘Signal‘] == 2), type=‘scatter‘, markersize=50, marker=‘^‘, color=‘g‘, label=‘Bullish Engulf‘),
        mpf.make_addplot((plot_df[‘Signal‘] == 1), type=‘scatter‘, markersize=50, marker=‘v‘, color=‘r‘, label=‘Bearish Engulf‘),
    ]
    mpf.plot(plot_df.set_index(‘Timestamp‘), type=‘candle‘, addplot=apds, style=‘charles‘, title=‘False Bullish Engulfing Example‘)
```

通过可视化，我们可以观察误报发生时市场的整体结构（如是否处于强趋势中、是否有重要支撑阻力位），这为优化策略（例如结合趋势过滤器）提供了线索。

---

## 总结 ✨

在本教程中，我们一起完成了一个完整的吞噬蜡烛模式统计验证流程：

1.  **理解模式**：明确了看涨与看跌吞噬蜡烛的图形特征和市场含义。
2.  **数据处理**：使用`pandas`加载和查看了金融时间序列数据。
3.  **信号识别**：编写了`identify_engulfing_signals`函数，基于定义的条件自动在历史数据中标记吞噬模式。
4.  **目标验证**：编写了`evaluate_target`函数，定义了如何量化“预测成功”（价格在特定窗口内朝预期方向移动特定点数）。
5.  **精度计算**：通过对比预测信号和验证后的趋势，计算了吞噬模式作为交易信号的预测准确率。
6.  **统计对比**：引入了随机预测作为基线，证实了有效的技术形态其准确率应显著高于随机水平。
7.  **深入分析**：通过可视化误报案例，加深了对策略局限性的理解。

![](img/9aa5231d3e7fd94c4efea8a06466aabd_11.png)

**核心结论**：吞噬蜡烛模式在历史数据中显示出一定的预测能力（例如准确率可能达到70%-80%），显著优于随机猜测。然而，它并非百分之百可靠，存在误报。因此，在实际交易中，**建议将吞噬信号与其他技术指标（如移动平均线、相对强弱指数RSI、趋势线等）结合使用**，以构建更稳健、过滤条件更严格的交易策略。你可以通过调整观察窗口`bars_to_consider`和盈利目标`point_limit`来寻找最适合特定市场的参数组合。