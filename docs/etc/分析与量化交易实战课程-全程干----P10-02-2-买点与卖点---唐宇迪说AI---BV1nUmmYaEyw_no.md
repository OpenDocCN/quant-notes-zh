# Python金融分析与量化交易实战：P10：02-2-买点与卖点可视化分析 📈

![](img/77f12ecd43146236b348e43098f67d2e_0.png)

![](img/77f12ecd43146236b348e43098f67d2e_2.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，核心是计算股票的短期与长期移动平均线，并通过它们的交叉点来识别潜在的买入和卖出信号。我们将从数据预处理开始，逐步完成计算、标记和可视化，最终理解双均线策略的基本原理。

![](img/77f12ecd43146236b348e43098f67d2e_4.png)

---

![](img/77f12ecd43146236b348e43098f67d2e_6.png)

## 第一步：导入工具包与读取数据

![](img/77f12ecd43146236b348e43098f67d2e_8.png)

![](img/77f12ecd43146236b348e43098f67d2e_10.png)

首先，我们需要导入必要的Python库并加载数据集。我们将使用`pandas`进行数据处理，`matplotlib`进行绘图。

![](img/77f12ecd43146236b348e43098f67d2e_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

接下来，读取包含股票历史价格的数据文件。我们将指定第一列（时间）为索引，并解析时间格式。

![](img/77f12ecd43146236b348e43098f67d2e_14.png)

![](img/77f12ecd43146236b348e43098f67d2e_16.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/77f12ecd43146236b348e43098f67d2e_18.png)

数据集中包含多只股票的信息。在本教程中，我们将以苹果公司（AAPL）的股票为例进行分析。

![](img/77f12ecd43146236b348e43098f67d2e_20.png)

![](img/77f12ecd43146236b348e43098f67d2e_22.png)

```python
# 选择苹果公司的股票数据列
stock_data = data[['AAPL']]
```

![](img/77f12ecd43146236b348e43098f67d2e_24.png)

---

![](img/77f12ecd43146236b348e43098f67d2e_26.png)

## 第二步：数据预处理——处理缺失值

![](img/77f12ecd43146236b348e43098f67d2e_28.png)

在金融时间序列分析中，缺失值可能会影响移动平均等滚动计算的结果。因此，第一步是清理数据，去除所有缺失值。

![](img/77f12ecd43146236b348e43098f67d2e_30.png)

```python
# 删除包含缺失值的行
stock_data = stock_data.dropna()
```

![](img/77f12ecd43146236b348e43098f67d2e_32.png)

执行此操作后，我们获得了一个干净、连续的苹果公司股价序列。

---

![](img/77f12ecd43146236b348e43098f67d2e_34.png)

## 第三步：计算短期与长期移动平均线

上一节我们准备好了数据，本节中我们来看看如何计算关键的技术指标——移动平均线。移动平均线能平滑价格波动，帮助我们识别趋势。

![](img/77f12ecd43146236b348e43098f67d2e_36.png)

核心概念是计算指定时间窗口内收盘价的平均值。我们定义两个窗口：短期窗口和长期窗口。

```python
# 定义短期和长期移动平均的窗口大小
short_window = 40
long_window = 100
```

![](img/77f12ecd43146236b348e43098f67d2e_38.png)

![](img/77f12ecd43146236b348e43098f67d2e_40.png)

以下是计算移动平均线的步骤：

![](img/77f12ecd43146236b348e43098f67d2e_42.png)

1.  **计算短期移动平均线 (SMA_short)**：对股价序列应用一个滚动窗口，计算窗口内数据的均值。
2.  **计算长期移动平均线 (SMA_long)**：使用更大的窗口重复上述过程。

![](img/77f12ecd43146236b348e43098f67d2e_44.png)

![](img/77f12ecd43146236b348e43098f67d2e_46.png)

```python
# 计算短期移动平均线
stock_data['SMA_short'] = stock_data['AAPL'].rolling(window=short_window).mean()
# 计算长期移动平均线
stock_data['SMA_long'] = stock_data['AAPL'].rolling(window=long_window).mean()
```

![](img/77f12ecd43146236b348e43098f67d2e_48.png)

计算完成后，数据的前几行由于窗口不足会显示为缺失值（NaN），这是正常现象。我们可以查看数据尾部来确认计算成功。

![](img/77f12ecd43146236b348e43098f67d2e_50.png)

---

![](img/77f12ecd43146236b348e43098f67d2e_52.png)

![](img/77f12ecd43146236b348e43098f67d2e_54.png)

## 第四步：可视化移动平均线与交叉点

![](img/77f12ecd43146236b348e43098f67d2e_56.png)

计算完指标后，最直观的方式就是将其可视化。通过绘图，我们可以清晰地观察股价、短期均线和长期均线的走势关系。

![](img/77f12ecd43146236b348e43098f67d2e_58.png)

```python
# 绘制股价与移动平均线
stock_data.plot(figsize=(12, 8))
plt.title('Apple Stock Price with Moving Averages')
plt.show()
```

在生成的图表中，您将看到：
*   原始股价线（通常为蓝色或红色）。
*   短期移动平均线（例如红色）。
*   长期移动平均线（例如绿色）。

**关键观察点在于两条均线的交叉：**
*   **黄金交叉 (Golden Cross)**：当短期均线从下方**上穿**长期均线时，通常被视为**买入信号**📈。
*   **死亡交叉 (Death Cross)**：当短期均线从上方**下穿**长期均线时，通常被视为**卖出信号**📉。

![](img/77f12ecd43146236b348e43098f67d2e_60.png)

图表中的交叉点就是我们需要关注的潜在买卖点。

![](img/77f12ecd43146236b348e43098f67d2e_62.png)

![](img/77f12ecd43146236b348e43098f67d2e_64.png)

---

![](img/77f12ecd43146236b348e43098f67d2e_66.png)

## 第五步：标记均线位置关系

![](img/77f12ecd43146236b348e43098f67d2e_68.png)

![](img/77f12ecd43146236b348e43098f67d2e_70.png)

为了量化交叉信号，我们需要创建一个标志位来明确记录短期均线相对于长期均线的位置。

首先，需要删除在计算移动平均线时产生的前面那些缺失值行。

![](img/77f12ecd43146236b348e43098f67d2e_72.png)

```python
# 删除因计算移动平均而产生的缺失值行
stock_data = stock_data.dropna()
```

然后，使用`numpy`的`where`函数创建标志列。当短期均线大于长期均线时，标记为1；否则标记为0。

![](img/77f12ecd43146236b348e43098f67d2e_74.png)

```python
import numpy as np
# 创建位置关系标志位
stock_data['Position'] = np.where(stock_data['SMA_short'] > stock_data['SMA_long'], 1, 0)
```

![](img/77f12ecd43146236b348e43098f67d2e_76.png)

这个`Position`列清晰地指示了每个时间点，策略是应该处于持有（1）还是空仓（0）状态。`Position`从0变为1的点对应**买入点**，从1变为0的点对应**卖出点**。

![](img/77f12ecd43146236b348e43098f67d2e_78.png)

我们也可以将标志位绘制在图上以便观察。

```python
# 绘制股价、均线及买卖标志位
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(12, 10), sharex=True)

ax1.plot(stock_data['AAPL'], label='Price')
ax1.plot(stock_data['SMA_short'], label=f'SMA {short_window}')
ax1.plot(stock_data['SMA_long'], label=f'SMA {long_window}')
ax1.legend()
ax1.set_title('Apple Stock Price with Moving Averages')

![](img/77f12ecd43146236b348e43098f67d2e_80.png)

![](img/77f12ecd43146236b348e43098f67d2e_82.png)

ax2.plot(stock_data['Position'], label='Trading Signal (1=Hold, 0=No Hold)', color='red', drawstyle='steps-post')
ax2.legend()
ax2.set_title('Trading Signal Based on MA Crossover')

![](img/77f12ecd43146236b348e43098f67d2e_84.png)

plt.show()
```

---

## 第六步：理解双均线交叉策略的逻辑

现在，我们已经成功识别出了买卖点。让我们来理解这个策略如何理论上创造收益。

假设我们最初有100元本金：
*   **无策略情况 (Buy and Hold)**：在最早的时间点全仓买入并一直持有到最后。最终收益完全取决于股价的涨跌。如果股价从100元跌到80元，则亏损20元。
*   **双均线策略情况**：我们根据信号操作。
    1.  在出现**死亡交叉（卖出信号）**时，我们以100元的价格卖出股票，持有现金。
    2.  随后股价下跌。
    3.  当出现**黄金交叉（买入信号）**时，我们以80元的价格重新买入股票。
    4.  虽然股价下跌了，但我们通过“高卖低买”的操作，仍然持有相同的股数，却额外获得了20元的现金利润。这相当于在下跌趋势中依然保持了盈利或减少了亏损。

**核心思想**：该策略旨在抓住主要趋势，在上升趋势开始时买入，在下降趋势开始时卖出，从而避免大的回撤，提高资金使用效率。当然，这是一个简化模型，实际应用中还需考虑交易成本、滑点等因素。

![](img/77f12ecd43146236b348e43098f67d2e_86.png)

---

![](img/77f12ecd43146236b348e43098f67d2e_88.png)

本节课中我们一起学习了金融数据分析中的双均线交叉策略。我们从数据导入和清洗开始，逐步计算了短期与长期移动平均线，并通过可视化清晰地识别了“黄金交叉”和“死亡交叉”这两个关键买卖信号。最后，我们创建了交易信号标志位，并探讨了该策略如何通过趋势跟踪来试图获得超越简单买入持有的收益。这是构建自动化量化交易策略的重要基础步骤。