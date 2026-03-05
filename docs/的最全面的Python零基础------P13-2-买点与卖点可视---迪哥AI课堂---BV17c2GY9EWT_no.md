# Python金融分析量化交易：P13：2-买点与卖点可视化分析 📈

![](img/494ecfe44c423187aedf65a37b052901_0.png)

![](img/494ecfe44c423187aedf65a37b052901_2.png)

![](img/494ecfe44c423187aedf65a37b052901_4.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，核心是计算股票的短期与长期移动平均线，并基于它们的交叉点来识别潜在的买入和卖出信号。我们将从数据预处理开始，逐步完成计算、标记和可视化，最终理解双均线策略的基本原理。

![](img/494ecfe44c423187aedf65a37b052901_6.png)

## 数据准备与预处理

![](img/494ecfe44c423187aedf65a37b052901_8.png)

![](img/494ecfe44c423187aedf65a37b052901_10.png)

首先，我们需要导入必要的工具包并加载数据。以下是实现步骤。

![](img/494ecfe44c423187aedf65a37b052901_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/494ecfe44c423187aedf65a37b052901_14.png)

接下来，我们读取股票数据文件。这里假设数据文件为`data.csv`，并且第一列是时间索引。

![](img/494ecfe44c423187aedf65a37b052901_16.png)

![](img/494ecfe44c423187aedf65a37b052901_18.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/494ecfe44c423187aedf65a37b052901_20.png)

数据加载后，我们选择苹果公司（AAPL）的股票价格数据作为分析示例，并处理可能存在的缺失值，以确保后续计算的准确性。

![](img/494ecfe44c423187aedf65a37b052901_22.png)

![](img/494ecfe44c423187aedf65a37b052901_24.png)

```python
# 选择苹果公司的股票价格列
data = data[['AAPL']]
# 删除缺失值
data = data.dropna()
print(data.head())
```

![](img/494ecfe44c423187aedf65a37b052901_26.png)

## 计算移动平均线

![](img/494ecfe44c423187aedf65a37b052901_28.png)

上一节我们准备好了数据，本节中我们来看看如何计算关键的短期和长期移动平均线。移动平均线是分析股票趋势的重要工具。

![](img/494ecfe44c423187aedf65a37b052901_30.png)

![](img/494ecfe44c423187aedf65a37b052901_32.png)

首先，我们需要定义短期和长期的时间窗口。考虑到我们的数据集时间跨度较长（约7-8年），我们将窗口设置得稍大一些，以便在图表中更清晰地观察趋势。

```python
# 定义短期和长期窗口
short_window = 40
long_window = 100
```

![](img/494ecfe44c423187aedf65a37b052901_34.png)

定义好窗口后，我们就可以计算移动平均线了。以下是计算短期移动平均线（SMA）的公式和代码。

**公式：**
`短期移动平均线 (SMA_short) = 过去 short_window 天收盘价的平均值`

![](img/494ecfe44c423187aedf65a37b052901_36.png)

![](img/494ecfe44c423187aedf65a37b052901_38.png)

**代码实现：**
```python
# 计算短期移动平均线
data['SMA_short'] = data['AAPL'].rolling(window=short_window).mean()
# 计算长期移动平均线
data['SMA_long'] = data['AAPL'].rolling(window=long_window).mean()
```

![](img/494ecfe44c423187aedf65a37b052901_40.png)

![](img/494ecfe44c423187aedf65a37b052901_42.png)

计算完成后，我们可以查看数据的尾部，确认移动平均线已成功添加。由于移动平均计算需要足够的数据点，初始部分会出现一些缺失值（NaN），这是正常现象。

![](img/494ecfe44c423187aedf65a37b052901_44.png)

```python
print(data.tail())
```

![](img/494ecfe44c423187aedf65a37b052901_46.png)

![](img/494ecfe44c423187aedf65a37b052901_48.png)

## 可视化移动平均线与交叉点

![](img/494ecfe44c423187aedf65a37b052901_50.png)

![](img/494ecfe44c423187aedf65a37b052901_52.png)

计算完移动平均线后，我们可以通过图表直观地观察股价与两条均线的走势，并识别它们的交叉点。

![](img/494ecfe44c423187aedf65a37b052901_54.png)

以下是绘制股价与双均线图的代码。

![](img/494ecfe44c423187aedf65a37b052901_56.png)

![](img/494ecfe44c423187aedf65a37b052901_58.png)

```python
# 绘制股价与移动平均线
data.plot(figsize=(12, 8))
plt.title('Apple Stock Price with Moving Averages')
plt.ylabel('Price')
plt.xlabel('Date')
plt.show()
```

![](img/494ecfe44c423187aedf65a37b052901_60.png)

在生成的图表中，我们可以观察到：
*   **原始股价线**（通常为蓝色或红色）。
*   **短期移动平均线**（例如红色线），对价格变化更敏感。
*   **长期移动平均线**（例如绿色线），趋势更平滑。

![](img/494ecfe44c423187aedf65a37b052901_62.png)

![](img/494ecfe44c423187aedf65a37b052901_64.png)

**关键交叉点分析：**
当短期均线从下方上穿长期均线时，形成“黄金交叉”，通常被视为**买入信号**。
当短期均线从上方下穿长期均线时，形成“死亡交叉”，通常被视为**卖出信号**。
在图表中，我们可以清晰地找到这些交叉点。

![](img/494ecfe44c423187aedf65a37b052901_66.png)

## 标记买卖信号

为了量化分析，我们需要在数据中明确标记出这些信号。这涉及到创建一个新的列来指示短期均线是否高于长期均线。

![](img/494ecfe44c423187aedf65a37b052901_68.png)

首先，我们需要再次清理数据，移除计算移动平均线时产生的缺失值。

![](img/494ecfe44c423187aedf65a37b052901_70.png)

![](img/494ecfe44c423187aedf65a37b052901_72.png)

```python
# 移除因计算移动平均线而产生的缺失值
data = data.dropna()
```

![](img/494ecfe44c423187aedf65a37b052901_74.png)

![](img/494ecfe44c423187aedf65a37b052901_76.png)

接着，我们创建一个名为 `Position` 的标志列。当短期均线高于长期均线时，标记为1（代表持有或买入信号）；否则标记为0（代表卖出或观望信号）。

![](img/494ecfe44c423187aedf65a37b052901_78.png)

以下是使用 `np.where` 函数实现此逻辑的代码。

```python
import numpy as np
# 创建买卖信号标志位
data['Position'] = np.where(data['SMA_short'] > data['SMA_long'], 1, 0)
print(data.head())
```

![](img/494ecfe44c423187aedf65a37b052901_80.png)

现在，数据框中新增了 `Position` 列，我们可以根据此列的值来识别策略状态。

![](img/494ecfe44c423187aedf65a37b052901_82.png)

## 可视化买卖信号

![](img/494ecfe44c423187aedf65a37b052901_84.png)

![](img/494ecfe44c423187aedf65a37b052901_86.png)

最后，我们将买卖信号也绘制在图表上，以便更直观地理解策略的触发点。

![](img/494ecfe44c423187aedf65a37b052901_88.png)

由于股价和信号（0或1）的数值范围差异很大，我们需要使用双Y轴来绘制。以下是实现代码。

```python
# 创建图形和主Y轴
fig, ax1 = plt.subplots(figsize=(14, 8))
# 在主Y轴上绘制股价和移动平均线
ax1.plot(data['AAPL'], label='AAPL Price', alpha=0.5)
ax1.plot(data['SMA_short'], label=f'{short_window}-Day SMA', color='red')
ax1.plot(data['SMA_long'], label=f'{long_window}-Day SMA', color='green')
ax1.set_ylabel('Price')
ax1.legend(loc='upper left')
# 创建次Y轴，用于绘制买卖信号
ax2 = ax1.twinx()
ax2.plot(data['Position'], label='Trading Signal (Buy=1)', color='blue', alpha=0.3, drawstyle='steps-post')
ax2.set_ylabel('Signal')
ax2.legend(loc='upper right')
plt.title('Apple Stock Price with Moving Averages and Trading Signals')
plt.show()
```

![](img/494ecfe44c423187aedf65a37b052901_90.png)

在这张综合图表中，我们不仅能看见股价和双均线的走势，还能看到底部代表买卖信号的阶梯状线条。信号为1的区域，对应着策略认为应该持有或买入的时期。

![](img/494ecfe44c423187aedf65a37b052901_92.png)

![](img/494ecfe44c423187aedf65a37b052901_94.png)

## 策略逻辑与收益概念

通过上述可视化，我们可以理解双均线交叉策略的核心逻辑：它试图捕捉趋势的转变。

*   **什么都不做的策略**：如果从一开始买入并一直持有到最后，你的收益完全取决于股价的最终涨跌。
*   **双均线策略**：该策略试图通过“死亡交叉”卖出和“黄金交叉”买回来规避下跌、参与上涨。理想情况下，这能实现“高抛低吸”，从而可能获得比单纯持有更高的收益，或者减少在下跌行情中的损失。

例如，假设股价从100元跌到80元。
*   **单纯持有**：你会亏损20元。
*   **理想的双均线策略**：在“死亡交叉”点（假设股价为95元）附近卖出，然后在“黄金交叉”点（假设股价为75元）附近买回。这样，你不仅避免了部分下跌，还用同样的资金买入了更多股份，为后续上涨积累了优势。

**请注意**：这是一个简化的示例。实际交易中需要考虑交易成本、滑点、以及信号滞后性（均线基于历史数据）等问题。

![](img/494ecfe44c423187aedf65a37b052901_96.png)

---

![](img/494ecfe44c423187aedf65a37b052901_98.png)

本节课中我们一起学习了如何使用Python进行金融数据的可视化分析。我们从导入数据和预处理开始，计算了短期与长期移动平均线，并通过图表直观展示了它们的交叉点。接着，我们创建了买卖信号标志位，并最终在一个图中综合展示了股价、均线以及交易信号。这个过程揭示了双均线交叉策略如何试图识别市场趋势的转变，为量化交易策略的初步构建打下了基础。在后续课程中，我们可以基于这些信号进行回测，来验证策略的历史表现。