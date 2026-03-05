# 量化策略：P1：相似K线对比图

在本节课中，我们将学习如何构建一个“相似K线对比图”工具。这个工具的核心思想是，通过算法在历史数据中寻找与当前K线形态相似的片段，并将它们并列展示，以辅助我们进行模式识别和交易决策。

## 概述

“相似K线对比图”是一种技术分析工具。它基于一个假设：历史会重演，相似的K线形态可能预示着相似的价格走势。通过将当前K线形态与历史中相似的形态进行对比，我们可以获得更多市场行为的参考信息。

上一节我们介绍了工具的基本概念，本节中我们来看看构建这个工具需要哪些核心组件和步骤。

## 核心组件与步骤

![](img/e7912a0bee614f967318be1d86de1e61_0.png)

构建相似K线对比图主要涉及三个关键环节：数据准备、相似度计算和结果可视化。

### 1. 数据准备

首先，我们需要获取并处理金融时间序列数据，通常是股票或指数的日线/分钟线数据。数据应至少包含开盘价、最高价、最低价和收盘价。

以下是数据准备的关键步骤：

*   **获取数据**：从数据库或API（如Tushare、AKShare）获取原始K线数据。
*   **数据清洗**：处理缺失值、异常值，确保数据连续性。
*   **特征工程**：为了更有效地比较形态，我们不仅使用原始价格，还可能计算一些衍生特征，例如：
    *   归一化的价格序列（将一段时间内的价格缩放到同一尺度）。
    *   技术指标（如RSI、MACD）。
    *   形态特征（如是否出现“锤子线”、“吞没形态”等）。

### 2. 相似度计算

这是工具的核心。我们需要定义一个数学方法来量化两条K线序列的“相似程度”。

一种常见的方法是使用**动态时间规整**算法。DTW可以有效地比较两个长度可能不同的时间序列，计算它们之间的最小距离。距离越小，表示形态越相似。

其核心公式可以表示为寻找一个规整路径，使得两个序列的累积距离最小：

`DTW(X, Y) = min_{π} √( Σ_{(i, j) ∈ π} (X[i] - Y[j])² )`

其中，`π` 是规整路径，`X` 和 `Y` 是两个时间序列。

在代码中，我们可以使用现成的库来计算：

```python
from dtw import dtw
import numpy as np

# 假设 current_pattern 和 historical_pattern 是归一化后的价格序列
current_pattern = np.array([...])
historical_pattern = np.array([...])

# 计算DTW距离
distance, _, _, _ = dtw(current_pattern, historical_pattern)
```

除了DTW，也可以考虑使用欧几里得距离（要求序列等长）、余弦相似度或基于深度学习的特征提取方法。

### 3. 结果可视化

计算出与当前K线最相似的若干个历史片段后，我们需要将它们清晰地展示出来。

以下是可视化的要点：

*   **并列绘图**：使用子图功能，将当前K线图和Top N个相似的历史K线片段并列绘制。
*   **信息标注**：在每个历史片段图上，标注该形态出现后的后续走势（例如，未来5根K线的涨跌幅），作为参考。
*   **交互功能**（可选）：允许用户调整查找的参数，如K线片段长度、相似度算法权重等。

上一节我们详细拆解了构建流程，本节中我们来看看一个简化的代码框架示例。

## 代码框架示例

以下是一个高度简化的Python代码框架，展示了核心逻辑：

```python
import pandas as pd
import numpy as np
from dtw import dtw
import matplotlib.pyplot as plt

![](img/e7912a0bee614f967318be1d86de1e61_2.png)

def find_similar_patterns(current_data, historical_data, pattern_length=20, top_n=5):
    """
    寻找相似K线形态
    """
    # 1. 从当前数据截取最新一段作为当前形态
    current_pattern = normalize(current_data[-pattern_length:])
    
    similarities = []
    # 2. 遍历历史数据（使用滑动窗口）
    for i in range(pattern_length, len(historical_data) - pattern_length):
        hist_pattern = normalize(historical_data[i-pattern_length:i])
        
        # 3. 计算相似度（这里用DTW距离的负值作为相似度得分，得分越高越相似）
        dist, _, _, _ = dtw(current_pattern, hist_pattern)
        similarity_score = -dist  # 距离越小，得分越高
        
        # 记录位置和得分
        similarities.append((i, similarity_score, hist_pattern))
    
    # 4. 按相似度得分排序，返回Top N
    similarities.sort(key=lambda x: x[1], reverse=True)
    return similarities[:top_n]

def normalize(series):
    """归一化价格序列"""
    return (series - series.min()) / (series.max() - series.min())

# 假设 df 是包含‘close’价格的DataFrame
df = pd.read_csv(‘your_price_data.csv’)
current_close = df[‘close’].values

# 查找相似形态
top_patterns = find_similar_patterns(current_close, current_close)

# 可视化（此处省略详细绘图代码）
print(f“找到 {len(top_patterns)} 个相似形态。”)
```

## 总结

本节课中我们一起学习了“相似K线对比图”工具的构建方法。我们首先了解了其基本概念和应用价值，然后逐步拆解了**数据准备**、**相似度计算**（核心是**DTW算法**）和**结果可视化**三个关键步骤，并给出了一个简明的代码框架。通过这个工具，我们可以系统性地从历史中寻找与当前市场相似的情景，为量化策略开发和主观交易决策提供数据驱动的参考。记住，这只是一个分析工具，实际应用中需要结合其他因子进行综合判断。