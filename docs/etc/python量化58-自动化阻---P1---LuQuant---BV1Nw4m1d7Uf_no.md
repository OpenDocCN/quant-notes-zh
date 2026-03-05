# Python量化58：自动化阻力与支撑检测 - P1 - LuQuant

## 概述
在本节课中，我们将学习如何在Python中自动检测金融图表中的支撑位和阻力位。我们将介绍一个相对简单的算法，并通过编写代码来实现它，最终将检测结果可视化在价格图表上。

---

![](img/f74f2d8f516a61880e4acdcd2fcb8162_1.png)

## 算法原理
上一节我们介绍了课程目标，本节中我们来看看支撑位和阻力位的检测算法是如何工作的。

支撑位和阻力位是技术分析中的核心概念。支撑位是指价格下跌时可能遇到买盘从而使跌势暂停或反转的价位；阻力位则是指价格上涨时可能遇到卖盘从而使涨势暂停或反转的价位。

我们的算法基于一个特定的价格形态来识别这些关键位：

![](img/f74f2d8f516a61880e4acdcd2fcb8162_3.png)

*   **对于支撑位**：我们寻找一个“低点-更低低点-低点”的形态。具体来说，在一根我们感兴趣的蜡烛（称为“L蜡烛”）之前，需要有N1根蜡烛，它们的低点依次降低。L蜡烛本身的低点必须是这个序列中的最低点。在L蜡烛之后，需要有N2根蜡烛，它们的低点依次升高。
*   **对于阻力位**：我们寻找一个“高点-更高高点-高点”的形态。具体来说，在一根我们感兴趣的蜡烛（L蜡烛）之前，需要有N1根蜡烛，它们的高点依次升高。L蜡烛本身的高点必须是这个序列中的最高点。在L蜡烛之后，需要有N2根蜡烛，它们的高点依次降低。

当上述模式被识别时，我们就认为找到了一个关键位。支撑位的价格值等于该形态中蜡烛的最低价格（即L蜡烛的低点），阻力位的价格值等于该形态中蜡烛的最高价格（即L蜡烛的高点）。

以下是算法的核心逻辑描述：
```python
# 伪代码逻辑
if (L蜡烛前N1根蜡烛的低点依次降低) and (L蜡烛是序列中最低点) and (L蜡烛后N2根蜡烛的低点依次升高):
    发现支撑位，价格 = L蜡烛.低点

![](img/f74f2d8f516a61880e4acdcd2fcb8162_5.png)

if (L蜡烛前N1根蜡烛的高点依次升高) and (L蜡烛是序列中最高点) and (L蜡烛后N2根蜡烛的高点依次降低):
    发现阻力位，价格 = L蜡烛.高点
```
参数`N1`和`N2`（即回顾和前瞻的蜡烛数量）是用户可以调整的，它们直接影响算法的敏感度。

---

## 代码实现
理解了算法原理后，我们现在来看看如何在Python中实现它。我们将使用Pandas进行数据处理。

首先，我们需要导入必要的库并加载数据。
```python
import pandas as pd
import mplfinance as mpf

# 加载数据，这里以EUR/USD日线数据为例
df = pd.read_csv(‘your_data.csv‘, index_col=0, parse_dates=True)
df.index.name = ‘Date‘

# 数据清理：移除交易量为0的日期（如节假日）
df_cleaned = df[df[‘Volume‘] != 0]
```

接下来，我们定义两个核心函数来检测支撑位和阻力位。

以下是支撑位检测函数：
```python
def is_support(df, i, n1, n2):
    """
    检查索引为i的蜡烛是否构成支撑位。
    参数:
        df: 包含‘Low‘列的价格DataFrame
        i: 当前测试蜡烛的索引
        n1: 回顾的蜡烛数量
        n2: 前瞻的蜡烛数量
    返回:
        1 如果构成支撑位，否则返回 0
    """
    # 检查i蜡烛之前的n1根蜡烛：低点应依次降低
    for j in range(i - n1 + 1, i + 1):
        if df[‘Low‘].iloc[j] >= df[‘Low‘].iloc[j - 1]:
            return 0
    # 检查i蜡烛之后的n2根蜡烛：低点应依次升高
    for j in range(i + 1, i + n2 + 1):
        if df[‘Low‘].iloc[j] <= df[‘Low‘].iloc[j - 1]:
            return 0
    # 所有条件满足，发现支撑位
    return 1
```

以下是阻力位检测函数：
```python
def is_resistance(df, i, n1, n2):
    """
    检查索引为i的蜡烛是否构成阻力位。
    参数:
        df: 包含‘High‘列的价格DataFrame
        i: 当前测试蜡烛的索引
        n1: 回顾的蜡烛数量
        n2: 前瞻的蜡烛数量
    返回:
        1 如果构成阻力位，否则返回 0
    """
    # 检查i蜡烛之前的n1根蜡烛：高点应依次升高
    for j in range(i - n1 + 1, i + 1):
        if df[‘High‘].iloc[j] <= df[‘High‘].iloc[j - 1]:
            return 0
    # 检查i蜡烛之后的n2根蜡烛：高点应依次降低
    for j in range(i + 1, i + n2 + 1):
        if df[‘High‘].iloc[j] >= df[‘High‘].iloc[j - 1]:
            return 0
    # 所有条件满足，发现阻力位
    return 1
```

有了检测函数，我们需要遍历数据，应用这些函数来找出所有的支撑位和阻力位。

以下是遍历检测和结果收集的代码：
```python
# 定义参数
n1 = 3  # 回顾的蜡烛数量
n2 = 2  # 前瞻的蜡烛数量

support_levels = []  # 存储支撑位 (索引, 价格)
resistance_levels = [] # 存储阻力位 (索引, 价格)

# 遍历数据框，注意起始索引和结束索引要避开边界
for i in range(n1, len(df_cleaned) - n2):
    if is_support(df_cleaned, i, n1, n2):
        support_levels.append((i, df_cleaned[‘Low‘].iloc[i]))
    if is_resistance(df_cleaned, i, n1, n2):
        resistance_levels.append((i, df_cleaned[‘High‘].iloc[i]))
```

---

## 结果优化与可视化
我们找到了关键位，但直接绘制可能会发现有些线靠得太近。为了提高图表可读性，我们可以对它们进行合并处理。

以下是合并过于接近的水平线的函数：
```python
def merge_nearby_levels(levels, threshold=0.005):
    """
    合并价格过于接近的水平线。
    参数:
        levels: 列表，元素为(索引, 价格)
        threshold: 合并阈值，价格差小于此值则合并
    返回:
        合并后的水平线列表
    """
    if not levels:
        return levels

    # 按价格排序
    sorted_levels = sorted(levels, key=lambda x: x[1])
    merged = [sorted_levels[0]]

    for current in sorted_levels[1:]:
        prev = merged[-1]
        # 如果当前价格与上一个合并后的价格差值小于阈值，则跳过（合并）
        if abs(current[1] - prev[1]) < threshold:
            continue
        else:
            merged.append(current)
    return merged

# 应用合并函数
merged_support = merge_nearby_levels(support_levels)
merged_resistance = merge_nearby_levels(resistance_levels)
```

最后，我们将清理合并后的支撑位和阻力位绘制到K线图上。

以下是使用`mplfinance`库进行可视化的代码：
```python
# 准备绘图数据：将支撑/阻力位转换为mplfinance需要的格式 (起始索引， 价格)
apds = []

# 添加支撑线
for idx, price in merged_support:
    # 让水平线从被检测蜡烛的前几根开始绘制，更美观
    start_idx = max(0, idx - 3)
    end_idx = min(len(df_cleaned) - 1, idx + 3)
    apd = mpf.make_addplot([price, price], dates=[df_cleaned.index[start_idx], df_cleaned.index[end_idx]], color=‘purple‘, width=1.5, alpha=0.7)
    apds.append(apd)

# 添加阻力线
for idx, price in merged_resistance:
    start_idx = max(0, idx - 3)
    end_idx = min(len(df_cleaned) - 1, idx + 3)
    apd = mpf.make_addplot([price, price], dates=[df_cleaned.index[start_idx], df_cleaned.index[end_idx]], color=‘blue‘, width=1.5, alpha=0.7)
    apds.append(apd)

# 绘制K线图及水平线
mpf.plot(df_cleaned, type=‘candle‘, style=‘charles‘, addplot=apds, title=‘自动检测支撑与阻力位‘, volume=True)
```

运行以上代码后，你将得到一张带有紫色支撑线和蓝色阻力线的K线图。

---

## 参数调整与算法评估
我们的算法已经能够检测出一些关键的支撑位和阻力位。然而，它并非完美。你可能会注意到图表上一些明显的水平没有被检测到，这是因为它们不符合我们设定的严格形态条件。

算法的敏感度主要由`N1`和`N2`这两个参数控制：

*   **降低`N1`或`N2`的值**（例如从3改为2）：算法会更敏感，能检测到更多、更短期的关键位，但也可能包含更多“噪音”。
*   **增加`N1`或`N2`的值**：算法会更严格，只检测更显著、更长期的关键位，但可能会错过一些重要的短期水平。

因此，在实际应用中，你需要根据所交易品种的特性、时间框架以及交易策略的需求，对`N1`和`N2`进行反复测试和调整，以找到最适合的参数组合。

---

![](img/f74f2d8f516a61880e4acdcd2fcb8162_7.png)

![](img/f74f2d8f516a61880e4acdcd2fcb8162_8.png)

## 总结
本节课中我们一起学习了如何用Python实现一个自动检测支撑位和阻力位的算法。我们从算法原理出发，定义了具体的价格形态，然后将其翻译成`is_support`和`is_resistance`两个函数。接着，我们遍历历史数据应用这些函数，收集检测到的关键位。为了提高可视化效果，我们增加了合并相近水平线的步骤。最后，我们使用`mplfinance`库将结果清晰地展示在K线图上。记住，这个算法是众多检测方法中的一种，其效果依赖于`N1`和`N2`参数的设置，在实际使用中需要根据具体情况进行优化和调整。