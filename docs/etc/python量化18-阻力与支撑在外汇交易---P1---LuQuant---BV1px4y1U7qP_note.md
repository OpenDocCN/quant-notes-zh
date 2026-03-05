# Python量化交易18：第1章：结合RSI与支撑阻力位进行外汇交易 📈

![](img/acebd819831b27ce997e00fc54cf7cda_1.png)

![](img/acebd819831b27ce997e00fc54cf7cda_2.png)

在本节课中，我们将学习一种不同于传统RSI超买超卖区域使用方法的交易策略。我们将结合支撑位、阻力位和RSI动量来寻找更有效的交易入场点，并使用Python实现自动化检测。

![](img/acebd819831b27ce997e00fc54cf7cda_4.png)

## 概述

传统的RSI策略通常关注超买（如RSI>70）和超卖（如RSI<30）区域来寻找反转机会。然而，本教程将介绍一种替代方法：**使用RSI来判断趋势动量，并结合价格在关键支撑/阻力位的反应来过滤交易信号**。这种方法旨在捕捉趋势内的回调入场机会，而非单纯的逆势反转。

![](img/acebd819831b27ce997e00fc54cf7cda_6.png)

上一节我们提到了传统方法的局限性，本节中我们来看看如何构建我们新的策略框架。

## 核心概念与策略逻辑

我们的策略基于以下几个核心步骤：

1.  **检测关键水平**：识别图表上的支撑位和阻力位。
2.  **定义入场条件**：价格在关键水平附近出现特定的K线形态。
3.  **使用RSI过滤**：利用RSI动量确认趋势方向，只交易顺趋势的信号。

以下是策略的详细步骤：

### 1. 检测支撑位与阻力位

我们不依赖固定的水平线，而是通过寻找“分型”来自动检测潜在的关键价格区域。一个分型蜡烛是指其高点（对于阻力）或低点（对于支撑）显著高于或低于其前后一定数量的相邻蜡烛。

**代码描述：检测低点分型（潜在支撑位）**
```python
def is_support(dataframe, i, n1, n2):
    # 检查第i根蜡烛是否是低点分型
    # n1: 向左比较的蜡烛数量
    # n2: 向右比较的蜡烛数量
    condition = (
        dataframe['low'].iloc[i] < dataframe['low'].iloc[i-n1:i].min() and
        dataframe['low'].iloc[i] < dataframe['low'].iloc[i+1:i+n2+1].min()
    )
    return condition
```
`is_resistance` 函数的逻辑与之相反，用于检测高点分型。

![](img/acebd819831b27ce997e00fc54cf7cda_8.png)

### 2. 合并相近水平

初步检测到的水平可能彼此非常接近。我们可以将它们合并为一个单一水平，以简化图表并突出更重要的区域。

**方法**：计算两个水平之间的距离。如果距离小于设定的阈值（例如，对于EUR/USD，可使用 `distance_threshold = 0.0001`），则用它们的平均值替代这两个水平，或仅保留其中一个。

![](img/acebd819831b27ce997e00fc54cf7cda_10.png)

### 3. 定义入场信号条件

当价格接近一个关键水平时，需要满足以下三个条件才生成交易信号：

1.  **接近水平**：当前K线的影线（上影线或下影线）非常接近支撑位或阻力位。
2.  **K线主体位置**：
    *   对于**支撑位**：K线实体整体应位于支撑位**之上**。
    *   对于**阻力位**：K线实体整体应位于阻力位**之下**。
3.  **前序K线包容性**：当前K线之前的数根K线也应被“包容”在同一关键水平区域（即，对于支撑位，它们的实体也在该水平之上；对于阻力位，则在之下）。

这表示市场在测试关键水平后，出现了明确的拒绝反应。

### 4. 使用RSI进行趋势动量过滤

这是与传统方法的关键区别。我们不用RSI判断超买超卖，而是用它来确认当前的趋势动量方向。

*   **看涨信号过滤**：仅当 `RSI > 55`（示例值）时，才考虑生成的看涨反转信号。这表示市场处于上升动量中，我们只做多。
*   **看跌信号过滤**：仅当 `RSI < 45`（示例值）时，才考虑生成的看跌反转信号。这表示市场处于下降动量中，我们只做空。

**公式描述：**
```
有效看涨信号 = (价格接近支撑位 && 满足K线形态条件) && (RSI > 上阈值)
有效看跌信号 = (价格接近阻力位 && 满足K线形态条件) && (RSI < 下阈值)
```

## Python代码实现与回测

现在，让我们将上述逻辑转化为Python代码，并在历史数据上进行可视化测试。

### 数据准备

首先，我们加载数据并计算RSI指标。

```python
import pandas as pd
import pandas_ta as ta # 用于计算RSI

# 加载数据
df = pd.read_csv('eurusd_h1.csv')
# 过滤零成交量蜡烛（如周末）
df = df[df['volume'] > 0].reset_index(drop=True)

# 计算RSI
df['rsi'] = ta.rsi(df['close'], length=14)

# 为了演示清晰，选取部分数据
df_sample = df.iloc[:2000].copy()
```

### 实现核心函数

我们需要实现之前章节描述的所有函数：`is_support`, `is_resistance`, `check_candle_signal` 等。以下是信号检查函数的框架：

```python
def check_candle_signal(df, current_index, n1=8, n2=6, lookback=140):
    """
    检查指定蜡烛是否产生交易信号。
    df: 数据DataFrame
    current_index: 当前蜡烛索引
    n1, n2: 检测分型时前后比较的蜡烛数
    lookback: 向前查找多少根蜡烛来构建支撑阻力位集合
    """
    # 1. 获取过去lookback期内的数据片段
    data_slice = df.iloc[current_index-lookback:current_index+1]

    # 2. 检测该片段内的所有支撑位和阻力位
    support_levels = []
    resistance_levels = []
    for i in range(n1, len(data_slice) - n2):
        if is_support(data_slice, i, n1, n2):
            support_levels.append(data_slice['low'].iloc[i])
        if is_resistance(data_slice, i, n1, n2):
            resistance_levels.append(data_slice['high'].iloc[i])

    # 3. 合并相近的水平（代码略）
    # merged_levels = merge_levels(support_levels + resistance_levels, threshold=0.0001)

    # 4. 检查当前蜡烛是否接近任一关键水平并满足形态条件
    # 同时检查RSI条件
    current_candle = df.iloc[current_index]
    signal = 0 # 0:无信号, 1:看跌, 2:看涨

    # 示例逻辑：检查是否接近阻力且RSI<45
    for level in merged_resistance_levels:
        if is_near_resistance(current_candle, level) and current_candle['rsi'] < 45:
            # 进一步检查前序K线包容性条件...
            if previous_candles_contained(df, current_index, level, side='below'):
                signal = 1 # 看跌信号
                break

    # 类似逻辑检查看涨信号（接近支撑且RSI>55）
    # ...

    return signal
```

### 信号可视化

![](img/acebd819831b27ce997e00fc54cf7cda_12.png)

计算出信号后，我们可以将其标注在价格图表上进行分析。

![](img/acebd819831b27ce997e00fc54cf7cda_14.png)

```python
import matplotlib.pyplot as plt

![](img/acebd819831b27ce997e00fc54cf7cda_16.png)

![](img/acebd819831b27ce997e00fc54cf7cda_17.png)

# 计算一段数据内的所有信号
signals = []
for i in range(200, len(df_sample)):
    sig = check_candle_signal(df_sample, i)
    signals.append(sig)

df_sample_with_signals = df_sample.iloc[200:].copy()
df_sample_with_signals['signal'] = signals

# 绘图
plt.figure(figsize=(16,8))
plt.plot(df_sample_with_signals.index, df_sample_with_signals['close'], label='Close Price')

# 标注看跌信号
bear_signals = df_sample_with_signals[df_sample_with_signals['signal'] == 1]
plt.scatter(bear_signals.index, bear_signals['close'], color='purple', marker='v', s=100, label='Bearish Signal')

# 标注看涨信号
bull_signals = df_sample_with_signals[df_sample_with_signals['signal'] == 2]
plt.scatter(bull_signals.index, bull_signals['close'], color='green', marker='^', s=100, label='Bullish Signal')

plt.legend()
plt.show()
```

## 策略评估与注意事项

![](img/acebd819831b27ce997e00fc54cf7cda_19.png)

运行代码后，我们可以在图表上观察到生成的交易信号。一些信号恰好捕捉到了趋势在关键水平反弹的启动点，表现为良好的入场机会。然而，并非所有信号都有效，部分信号出现后价格并未如期移动。

![](img/acebd819831b27ce997e00fc54cf7cda_21.png)

这引出了几个重要的实践要点：

![](img/acebd819831b27ce997e00fc54cf7cda_23.png)

![](img/acebd819831b27ce997e00fc54cf7cda_24.png)

*   **信号非圣杯**：该策略提供的是**入场提示**，而非百分之百的盈利保证。风险管理（止损、止盈）和仓位控制同样至关重要。
*   **参数优化**：`n1`、`n2`、`lookback`、RSI阈值、水平合并距离等参数都需要根据不同的交易品种和时间框架进行测试和优化。
*   **趋势判断**：使用RSI动量过滤是为了顺趋势交易。可以考虑使用更平滑的RSI均值（例如过去5期均值）来避免噪音。
*   **水平转化**：支撑位一旦被有效突破，就可能转化为阻力位，反之亦然。策略中可以加入对水平角色动态判断的逻辑。

![](img/acebd819831b27ce997e00fc54cf7cda_25.png)

## 总结

本节课中我们一起学习了一种将支撑阻力位与RSI动量结合的外汇交易策略。我们摒弃了传统RSI的超买超卖用法，转而利用它来确认趋势方向，并在价格回调至关键水平时寻找顺趋势的入场机会。通过Python，我们实现了从自动检测关键水平、定义复杂入场条件到最终信号可视化的完整流程。

![](img/acebd819831b27ce997e00fc54cf7cda_27.png)

![](img/acebd819831b27ce997e00fc54cf7cda_29.png)

记住，这是一个策略框架的起点。你可以在此基础上进行修改，例如加入更多确认指标、优化参数、或者整合到更大的自动化交易系统中。持续的回测和验证是量化交易不可或缺的环节。