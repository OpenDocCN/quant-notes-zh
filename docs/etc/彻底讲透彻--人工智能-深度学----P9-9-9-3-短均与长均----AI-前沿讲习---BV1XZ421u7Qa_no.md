# Python量化交易：9.9.3：短均与长均计算实例 📈

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_1.png)

在本节课中，我们将学习一个基础的股票序列分析策略。该策略的核心是计算短期和长期移动平均线，并通过观察它们的交叉点来判断买入和卖出的时机，即所谓的“黄金交叉”和“死亡交叉”。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_3.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_5.png)

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何通过Python代码实现一个具体的移动平均线分析策略。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_7.png)

## 计算短期与长期移动平均线

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_9.png)

要分析黄金交叉和死亡交叉，我们需要两个关键指标：短期移动平均线和长期移动平均线。短期平均线通常反映近期价格趋势，而长期平均线则反映更长时间的整体趋势。

以下是计算这两个指标的具体步骤。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_11.png)

### 1. 选择数据与设定窗口

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_13.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_15.png)

首先，我们需要选定分析的股票数据。这里我们以苹果公司的股票数据为例。接着，我们需要设定计算移动平均的窗口大小。短期窗口通常较小，例如5天（代表一周的交易日）；长期窗口则较大，例如20天或30天。由于我们的数据集时间跨度较长，为了更清晰地展示交叉现象，我们将适当放大窗口。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_17.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_19.png)

```python
# 假设 df 是包含苹果股票价格（例如‘Close’列）的DataFrame
# 计算短期移动平均线，窗口为10天
short_window = 10
df[‘M1’] = df[‘Close’].rolling(window=short_window).mean()

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_21.png)

# 计算长期移动平均线，窗口为30天
long_window = 30
df[‘M2’] = df[‘Close’].rolling(window=long_window).mean()
```

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_23.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_25.png)

### 2. 可视化移动平均线与股价

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_27.png)

计算完成后，我们可以将股价、短期均线和长期均线绘制在同一张图上进行观察。这有助于直观地识别交叉点。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_29.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_31.png)

```python
import matplotlib.pyplot as plt

# 绘制股价与移动平均线
plt.figure(figsize=(12, 6))
plt.plot(df[‘Close’], label=‘Apple Stock Price’, alpha=0.5)
plt.plot(df[‘M1’], label=‘Short-term MA (10-day)’, color=‘green’)
plt.plot(df[‘M2’], label=‘Long-term MA (30-day)’, color=‘red’)
plt.legend()
plt.show()
```

为了更清晰地观察交叉细节，我们可以截取数据中的一段时间（例如一年）进行绘图。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_33.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_35.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_37.png)

## 识别黄金交叉与死亡交叉

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_39.png)

在图表中，我们可以手动识别两种重要的交叉形态：
*   **黄金交叉**：当短期移动平均线从下方上穿长期移动平均线时，通常被视为买入信号，预示着可能开启上涨趋势。
*   **死亡交叉**：当短期移动平均线从上方下穿长期移动平均线时，通常被视为卖出信号，预示着可能开启下跌趋势。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_41.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_43.png)

## 使用代码标记交叉信号

手动观察图表虽然直观，但我们可以用代码自动标记出这些交叉区域，使分析更加精确。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_45.png)

以下是实现自动标记的步骤。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_47.png)

### 1. 创建信号列

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_49.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_51.png)

我们使用 `np.where` 函数来比较短期均线 `M1` 和长期均线 `M2`。当 `M1 > M2` 时，标记为 `1`，代表看多信号；当 `M1 < M2` 时，标记为 `-1`，代表看空信号。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_53.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_55.png)

```python
import numpy as np

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_57.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_59.png)

# 生成交易信号：1 表示短期均线高于长期均线，-1 表示低于
df[‘Position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
```

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_61.png)

### 2. 双坐标轴可视化

由于信号值（1和-1）与股价数值范围差异巨大，直接绘制在同一坐标轴上会难以辨认。因此，我们使用双坐标轴进行绘图：左轴显示股价和移动平均线，右轴显示交易信号。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_63.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_65.png)

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_67.png)

# 左Y轴：绘制股价和均线
ax1.plot(df[‘Close’], label=‘Price’, color=‘blue’, alpha=0.3)
ax1.plot(df[‘M1’], label=‘10-day MA’, color=‘green’)
ax1.plot(df[‘M2’], label=‘30-day MA’, color=‘red’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

# 创建右Y轴，绘制交易信号
ax2 = ax1.twinx()
ax2.plot(df[‘Position’], label=‘Trading Signal’, color=‘purple’, linestyle=‘--’)
ax2.set_ylabel(‘Signal (1/-1)’)
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc=‘upper right’)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_69.png)

plt.title(‘Stock Price with Moving Averages and Trading Signals’)
plt.show()
```

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_71.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_73.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_75.png)

在这张图中，紫色虚线在 `1` 的位置波动，代表短期均线位于长期均线之上（潜在上涨期）；在 `-1` 的位置波动，代表短期均线位于长期均线之下（潜在下跌期）。信号线从 `-1` 上穿到 `1` 的点对应**黄金交叉**，从 `1` 下穿到 `-1` 的点对应**死亡交叉**。

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_77.png)

![](img/b15b68d3be1f6aa2ed1e7508f8fd00e4_79.png)

本节课中我们一起学习了如何利用Python计算股票的短期和长期移动平均线，并通过可视化方法识别交易中常见的“黄金交叉”与“死亡交叉”信号。我们还实现了用代码自动标记这些信号区域，使得趋势分析更加直观和系统化。这是构建量化交易策略的一个基础而重要的环节。