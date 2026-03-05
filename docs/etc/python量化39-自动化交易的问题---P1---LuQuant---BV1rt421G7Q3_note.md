# Python量化39：自动化交易的问题 - P1：头肩顶形态的自动检测 🧠

![](img/7efe326082091b38ff5fe9639b0265ae_1.png)

![](img/7efe326082091b38ff5fe9639b0265ae_2.png)

在本节课中，我们将学习如何在Python中自动检测头肩顶价格形态。通过编写程序来读取市场数据并发送提醒，你可以不必时刻盯盘，也能捕捉到潜在的市场机会。

## 概述

![](img/7efe326082091b38ff5fe9639b0265ae_4.png)

头肩顶是一种经典的反转形态，通常出现在上升趋势的末尾，预示着趋势可能发生逆转。它由三个连续的高点组成，中间的高点（头部）最高，两侧的高点（左肩和右肩）相对较低且高度相近。价格的低点连接起来形成一条“颈线”。理想情况下，价格跌破颈线后，形态完成。

这种形态可能存在变体。例如，颈线可能具有负斜率，这被称为“下降颈线”的头肩顶形态。在本教程中，我们将解析一个可以自动检测这些形态的算法，并学习如何用Python实现它。

![](img/7efe326082091b38ff5fe9639b0265ae_6.png)

包含完整源代码的Jupyter笔记本文件可以从视频描述中的链接下载。

![](img/7efe326082091b38ff5fe9639b0265ae_8.png)

---

![](img/7efe326082091b38ff5fe9639b0265ae_10.png)

## 算法步骤

我们的算法将遵循以下步骤来检测头肩顶形态。

### 1. 检测枢轴点 🔍

![](img/7efe326082091b38ff5fe9639b0265ae_12.png)

首先，我们必须检测价格图表中的枢轴点。枢轴点是指那些低点低于（或高点高于）其相邻若干根K线低点（或高点）的K线。

例如，我们寻找一根K线，其低点低于它左侧三根和右侧三根K线的低点。这根K线就是一个低枢轴点。在代码中，用于比较的相邻K线数量（例如3根、4根或更多）将作为一个变量，以便我们进行实验和调整，因为市场行为会变化，没有完美的固定参数。

在我们的程序中，我们将检测两种类型的枢轴点：
*   **强枢轴点**：与较宽范围内的邻居进行比较（例如，中心K线左侧和右侧各15根K线）。
*   **弱枢轴点**：与较少数量的邻居进行比较（例如，中心K线左侧和右侧各5根K线）。

弱枢轴点出现的频率会比强枢轴点更高，因为比较条件更容易满足。

### 2. 围绕强枢轴点搜索极值点 🎯

每次我们找到一个强枢轴点（通常是头部），我们就在其左右两侧的特定窗口内搜索弱枢轴点。这些弱枢轴点将作为潜在的肩部高点（最大值）和颈线连接点（最小值）。

具体来说，我们会记录强枢轴点左侧和右侧的弱枢轴点（包括高点和低点）的位置和价格。我们会分别统计在强枢轴点之前和之后找到的弱高点（最大值）和弱低点（最小值）的数量。

### 3. 定义形态与设置条件 ⚙️

为了确认一个头肩顶形态，我们需要满足以下条件：
1.  存在一个强枢轴点作为**头部**。
2.  在头部**之前**和**之后**，至少各存在一个弱枢轴点作为**肩部**（即最大值）。
3.  在头部**之前**和**之后**，至少各存在一个弱枢轴点作为**颈线的连接点**（即最小值）。
4.  头部的高度应显著高于两个肩部的高度。我们可以通过设置一个阈值来实现，例如：`头部价格 - 肩部价格 > 阈值`。
5.  连接两个低点（最小值）形成的**颈线**斜率应接近水平（例如，斜率的绝对值小于一个很小的值）。这可以根据你想检测的形态变体进行调整（例如，允许负斜率来检测“下降颈线”形态）。

此外，还可以添加更多过滤条件，例如要求头部到颈线的垂直距离与肩部到颈线的距离满足特定比例，以使检测更精确。

请记住，没有程序是完美的。我们的算法会检测到一些形态，同时也会错过一些。任何程序都有进一步改进的空间，以捕捉市场上更多的机会。

---

## 代码实现示例

以下是算法核心部分的简化逻辑框架，用于说明检测过程。

```python
import numpy as np
import pandas as pd

def detect_head_shoulders(df, lookback_candles=14, height_threshold=0.0015, slope_threshold=1e-4):
    """
    检测头肩顶形态。
    df: 包含价格和已计算出的强/弱枢轴点列的数据框
    lookback_candles: 在头部左右两侧搜索的K线数量
    height_threshold: 头部需高于肩部的最小价格差阈值
    slope_threshold: 颈线斜率的最大绝对值阈值
    """
    signals = [] # 用于存储检测到的形态索引

    # 假设 df 中已有 ‘is_strong_pivot‘ 和 ‘is_weak_pivot‘ 列
    for current_id in range(lookback_candles, len(df) - lookback_candles):
        # 条件1: 当前K线必须是强枢轴点（头部候选）
        if not df.loc[current_id, ‘is_strong_pivot‘]:
            continue

        # 初始化数组，用于存储头部周围的极值点
        max_prices = []
        max_indices = []
        min_prices = []
        min_indices = []

        # 在头部周围窗口内搜索弱枢轴点
        start = current_id - lookback_candles
        end = current_id + lookback_candles + 1
        for i in range(start, end):
            if df.loc[i, ‘is_weak_pivot‘]:
                price_high = df.loc[i, ‘high‘]
                price_low = df.loc[i, ‘low‘]
                # 简单判断：如果接近最高价，视为潜在肩部；接近最低价，视为潜在颈线点
                # 更严谨的做法需要区分高低点枢轴
                if abs(price_high - df.loc[i, ‘close‘]) < abs(price_low - df.loc[i, ‘close‘]):
                    max_prices.append(price_high)
                    max_indices.append(i)
                else:
                    min_prices.append(price_low)
                    min_indices.append(i)

        # 条件2 & 3: 检查是否有足够的肩部和颈线点
        # 需要至少：左肩1个高，右肩1个高，左颈1个低，右颈1个低
        # 这里进行简化检查，实际逻辑更复杂
        if len(max_prices) < 2 or len(min_prices) < 2:
            continue

        # 找到头部价格 (应该是强枢轴高点)
        head_price = df.loc[current_id, ‘high‘]
        head_index = current_id

        # 找到左肩和右肩（头部两侧的最高点）
        left_shoulder_price = max([p for i, p in zip(max_indices, max_prices) if i < head_index], default=None)
        right_shoulder_price = max([p for i, p in zip(max_indices, max_prices) if i > head_index], default=None)

        if left_shoulder_price is None or right_shoulder_price is None:
            continue

        # 条件4: 检查头部是否显著高于双肩
        if not (head_price - left_shoulder_price > height_threshold and
                head_price - right_shoulder_price > height_threshold):
            continue

        # 找到颈线点（头部两侧的最低点）
        left_neck_idx = [i for i in min_indices if i < head_index]
        right_neck_idx = [i for i in min_indices if i > head_index]
        if len(left_neck_idx) < 1 or len(right_neck_idx) < 1:
            continue

        # 使用线性回归计算颈线斜率
        neck_indices = [min(left_neck_idx), min(right_neck_idx)] # 取最靠近头部的两个点
        neck_prices = [df.loc[i, ‘low‘] for i in neck_indices]
        # 简单斜率计算: (y2 - y1) / (x2 - x1)
        neck_slope = (neck_prices[1] - neck_prices[0]) / (neck_indices[1] - neck_indices[0])

        # 条件5: 检查颈线斜率是否接近水平
        if abs(neck_slope) > slope_threshold:
            continue

        # 所有条件满足，记录信号
        signals.append(head_index)

    return signals
```

**代码说明**：
1.  函数遍历数据框中的每一根K线。
2.  首先检查当前K线是否为强枢轴点（潜在头部）。
3.  在头部周围的窗口内，收集所有的弱枢轴点，并区分为潜在肩部（高点）和颈线点（低点）。
4.  检查是否至少找到两个肩部点和两个颈线点。
5.  确认头部价格显著高于左右肩部价格。
6.  使用找到的两个颈线点计算颈线斜率，并检查其是否接近水平。
7.  如果所有条件都满足，则将头部K线的索引记录为检测到的信号。

---

## 总结

本节课中，我们一起学习了头肩顶形态的基本概念，并详细讲解了一个用于自动检测该形态的算法步骤。该算法的核心在于：
1.  **检测多级别枢轴点**（强、弱），作为形态的候选点。
2.  **围绕强枢轴点（头部）进行搜索**，定位肩部（高点）和颈线点（低点）。
3.  **设置一系列几何与价格条件**（如高度差、斜率）来过滤和确认有效的头肩顶形态。

![](img/7efe326082091b38ff5fe9639b0265ae_14.png)

![](img/7efe326082091b38ff5fe9639b0265ae_15.png)

通过实现这样的程序，你可以初步自动化对特定图表形态的监控。需要注意的是，这只是一个起点。在实际应用中，你需要根据不同的市场品种和周期调整参数（如`lookback_candles`， `height_threshold`），并可能添加更多过滤规则（如成交量确认、时间对称性、比率要求等）来提高信号的可靠性。金融市场中没有“圣杯”，持续的测试、优化和风险管理才是关键。