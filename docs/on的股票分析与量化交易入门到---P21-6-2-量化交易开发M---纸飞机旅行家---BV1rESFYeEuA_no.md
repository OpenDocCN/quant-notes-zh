# 基于Python的股票分析与量化交易入门到实践 - P21：6.2 量化交易开发Matplotlib应用-股票技术分析实战_基于Matplotlib实现KDJ 📈

## 概述
在本节课中，我们将学习如何使用Python的Matplotlib库来绘制KDJ指标图。KDJ是一种重要的技术分析指标，用于判断股票的超买和超卖状态。我们将从KDJ的基本概念讲起，逐步深入到其算法原理，并最终通过代码实战演示如何计算和可视化KDJ指标。

---

## 什么是KDJ？🤔
上一节我们介绍了如何使用Matplotlib绘制MACD指标。本节中，我们来看看另一个常用的技术指标——KDJ。

KDJ的中文名称是随机指数。它主要通过价格波动来反映股价走势的强弱以及超买和超卖现象。KDJ主要用于分析短期行情，其核心作用是识别市场的超买和超卖区域。

以下是KDJ指标的两个关键应用场景：
*   **超买区**：当KDJ值超过80时，通常意味着市场处于超买状态，股价后续可能下跌。
*   **超卖区**：当KDJ值低于30时，通常意味着市场处于超卖状态，股价后续可能上涨。

此外，KDJ线的交叉（例如K线上穿D线形成“金叉”）常被视作买入或卖出的交易信号。

---

## KDJ算法详解 🧮
了解了KDJ的基本作用后，本节我们来深入探讨其具体的计算算法。

KDJ的计算主要涉及四个指标：RSV、K值、D值和J值。它们的业务意义无需死记硬背，更多是统计学上的应用。其核心思想是通过统计方法找出股价变化的强弱情况。

以下是KDJ指标的计算步骤：

1.  **计算未成熟随机值RSV**：
    RSV反映了当前收盘价在最近N天价格区间内的相对位置。
    **公式**：`RSV = (收盘价 - N日内最低价) / (N日内最高价 - N日内最低价) * 100`
    通常，参数N取9。

2.  **计算K值**：
    K值是RSV的指数移动平均值，平滑了RSV的波动。
    **公式**：`当日K值 = 2/3 * 前一日K值 + 1/3 * 当日RSV`
    这是一个递归计算。如果前一日没有K值，则默认用50代替。系数2/3和1/3是约定俗成的参数。

3.  **计算D值**：
    D值是K值的指数移动平均值，对K值进行了二次平滑。
    **公式**：`当日D值 = 2/3 * 前一日D值 + 1/3 * 当日K值`
    同样，如果前一日没有D值，默认用50代替。

4.  **计算J值**：
    J值是K值与D值的差值，反应了K值与D值的离散程度，波动最为剧烈。
    **公式**：`J值 = 3 * 当日K值 - 2 * 当日D值`

具体的公式推导涉及较多数学和金融知识，本课程不展开讨论，大家掌握上述计算流程即可。

---

## 关键Python函数介绍 ⚙️
在动手编码实现KDJ之前，我们需要先了解两个关键的Pandas函数，它们能极大简化我们的计算过程。

![](img/0624050e8f2f0f50fe5ff5950b5c4107_1.png)

以下是实现KDJ算法时常用的两个Pandas函数：

*   **`rolling`函数**：
    这是Pandas中的移动窗口函数。在计算RSV时，我们需要得到N日内的最高价和最低价，`rolling`函数可以方便地按照固定的窗口大小（如9天）进行滑动计算。
    **代码示例**：`df[‘Low’].rolling(window=9).min()` 用于计算9日最低价。

*   **`expanding`函数**：
    这是Pandas中的扩展窗口函数，在金融工程中应用广泛。与`rolling`固定窗口不同，`expanding`窗口会从起始点开始不断累积扩大。它常与`max()`、`min()`等方法连用。
    **代码示例**：`df[‘High’].expanding().max()` 用于计算从开始到当前行的历史最高价。

---

## 代码实战：计算与绘制KDJ 📊
掌握了算法和关键函数后，本节我们进入代码实战环节，看看如何用Python实现KDJ的计算与绘图。

我们首先实现计算KDJ指标的核心函数。

```python
import pandas as pd

def calculate_kdj(df, n=9):
    """
    计算KDJ指标
    :param df: 包含‘High‘, ‘Low‘, ‘Close‘列的DataFrame
    :param n: 计算周期，默认为9
    :return: 添加了‘K‘, ‘D‘, ‘J‘列的DataFrame
    """
    # 计算N日内最低价 (LN) 和最高价 (HN)
    low_list = df[‘Low‘].rolling(window=n, min_periods=1).min()
    high_list = df[‘High‘].rolling(window=n, min_periods=1).max()

    # 计算RSV
    rsv = (df[‘Close‘] - low_list) / (high_list - low_list) * 100
    rsv.fillna(50, inplace=True) # 处理除零等情况，填充为50

    # 计算K值 (RSV的指数移动平均)
    df[‘K‘] = rsv.ewm(com=2, adjust=False).mean() # com=2 对应 alpha=1/3

    # 计算D值 (K值的指数移动平均)
    df[‘D‘] = df[‘K‘].ewm(com=2, adjust=False).mean()

    # 计算J值
    df[‘J‘] = 3 * df[‘K‘] - 2 * df[‘D‘]

    return df
```

接下来，我们使用Matplotlib将计算出的KDJ指标绘制成图表。

```python
import matplotlib.pyplot as plt

# 假设 df 是已经计算好KDJ指标的DataFrame
df = calculate_kdj(your_stock_data)

![](img/0624050e8f2f0f50fe5ff5950b5c4107_3.png)

fig, ax = plt.subplots(figsize=(14, 7))

# 绘制K线
ax.plot(df.index, df[‘K‘], color=‘red‘, label=‘K‘, linewidth=1)
# 绘制D线
ax.plot(df.index, df[‘D‘], color=‘yellow‘, label=‘D‘, linewidth=1)
# 绘制J线
ax.plot(df.index, df[‘J‘], color=‘blue‘, label=‘J‘, linewidth=1)

# 设置图表标题和标签
ax.set_title(‘平安银行KDJ指标图‘, fontsize=16)
ax.set_xlabel(‘日期‘)
ax.set_ylabel(‘KDJ值‘)
ax.legend(loc=‘upper left‘)
ax.grid(True, linestyle=‘--‘, alpha=0.5)

# 设置x轴刻度密度，避免重叠
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

运行以上代码，即可生成KDJ指标图。在图中，您可以观察K、D、J三条线的走势及其交叉情况，结合超买区（>80）和超卖区（<30）来判断市场状态。

---

## 总结 🎯
本节课中，我们一起学习了KDJ指标从理论到实践的完整过程。

首先，我们介绍了KDJ指标，它主要基于统计学意义，用于反映股价短期行情的超买和超卖情况。

接着，我们详细讲解了KDJ的算法，其核心是按顺序计算四个指标：RSV、K值、D值和J值。关键公式包括RSV的比例计算，以及K、D值的指数平滑递归计算。

最后，在代码实战部分，我们利用Pandas的`rolling`和`ewm`函数高效地实现了KDJ的计算，并使用Matplotlib将其清晰地可视化出来。通过`rolling`函数我们轻松得到了N日内的极值，而`ewm`函数则帮助我们实现了算法的平滑步骤。

![](img/0624050e8f2f0f50fe5ff5950b5c4107_5.png)

掌握KDJ指标的计算与绘制，是您构建量化交易策略、进行技术分析的重要一步。