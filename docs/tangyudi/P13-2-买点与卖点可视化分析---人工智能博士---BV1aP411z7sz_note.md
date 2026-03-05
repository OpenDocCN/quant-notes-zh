# 量化交易入门：P13：买点与卖点可视化分析 📈

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_0.png)

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_2.png)

在本节课中，我们将学习如何利用移动平均线（MA）策略，对股票价格数据进行可视化分析，并识别潜在的买入和卖出信号。我们将使用苹果公司的股价数据作为示例，通过计算短期和长期移动平均线，并观察它们的交叉点来制定交易策略。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_4.png)

---

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_6.png)

## 第一步：导入工具包与数据读取

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_8.png)

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_10.png)

首先，我们需要导入必要的Python库，并加载我们的股票价格数据集。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

接下来，我们读取包含多只股票价格的历史数据文件。数据集的第一列是时间，我们将它设置为索引并解析为日期时间格式。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_14.png)

```python
# 读取数据，指定第一列为索引（时间）
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_16.png)

读取数据后，我们选择苹果公司（AAPL）的股价列作为本次分析的对象。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_18.png)

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_20.png)

```python
# 选择苹果公司的股价列
apple_data = data[['AAPL']].copy()
```

---

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_22.png)

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_24.png)

## 第二步：数据预处理与缺失值处理

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_26.png)

在开始计算之前，我们需要确保数据的完整性。金融数据中常存在缺失值，这会影响移动平均线的计算。因此，我们的首要任务是清除这些缺失值。

```python
# 删除缺失值
apple_data = apple_data.dropna()
```

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_28.png)

处理完成后，我们可以查看一下数据的前几行，确认数据已准备就绪。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_30.png)

```python
print(apple_data.head())
```

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_32.png)

---

## 第三步：计算短期与长期移动平均线

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_34.png)

移动平均线是技术分析的核心工具。我们将计算两个不同时间窗口的移动平均线：一个短期窗口和一个长期窗口。它们的交叉点通常被视为交易信号。

我们首先定义短期和长期窗口的大小。考虑到我们的数据集跨度较长（约7-8年），为了在图表中更清晰地观察趋势，我们将窗口设置得比通常的5日和20日更大一些。

```python
# 定义短期和长期窗口大小
short_window = 42
long_window = 252
```

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_36.png)

接下来，我们使用Pandas的`rolling`和`mean`方法计算移动平均线，并将结果作为新列添加到数据框中。

```python
# 计算短期移动平均线
apple_data['short_ma'] = apple_data['AAPL'].rolling(window=short_window).mean()

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_38.png)

# 计算长期移动平均线
apple_data['long_ma'] = apple_data['AAPL'].rolling(window=long_window).mean()
```

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_40.png)

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_42.png)

计算完成后，数据的前期部分会因为窗口不足而出现缺失值，这是正常现象。我们可以查看数据的尾部来确认计算成功。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_44.png)

```python
print(apple_data.tail())
```

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_46.png)

---

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_48.png)

## 第四步：可视化移动平均线与交叉点

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_50.png)

现在，让我们将原始股价和两条移动平均线绘制在同一张图上，直观地观察它们的走势和交叉情况。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_52.png)

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_54.png)

```python
# 绘制股价与移动平均线
apple_data.plot(figsize=(14, 8))
plt.title('Apple Stock Price with Moving Averages')
plt.ylabel('Price')
plt.show()
```

在生成的图表中，您将看到：
*   **原始股价线**（通常为蓝色或红色）。
*   **短期移动平均线**（例如红色）。
*   **长期移动平均线**（例如绿色）。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_56.png)

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_58.png)

**关键观察点在于两条移动平均线的交叉：**
*   **黄金交叉（买入信号）**：当短期均线从下方上穿长期均线时，通常被视为上涨趋势的开始，是潜在的买入点。
*   **死亡交叉（卖出信号）**：当短期均线从上方下穿长期均线时，通常被视为下跌趋势的开始，是潜在的卖出点。

在图表中仔细寻找这些交叉点，它们是我们策略的基础。

---

## 第五步：标记均线位置关系

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_60.png)

为了量化这些信号，我们需要创建一个标志位，来明确记录每一天短期均线是高于还是低于长期均线。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_62.png)

首先，我们需要剔除在计算移动平均线时产生的缺失值，以确保数据的一致性。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_64.png)

```python
# 删除因计算移动平均线而产生的缺失值
apple_data = apple_data.dropna()
```

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_66.png)

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_68.png)

然后，我们使用`np.where`函数创建标志列。当短期均线大于长期均线时，标记为1；否则标记为0。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_70.png)

```python
import numpy as np

# 创建位置标志位
apple_data['position'] = np.where(apple_data['short_ma'] > apple_data['long_ma'], 1, 0)
```

我们可以查看数据的前几行，确认新列已添加。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_72.png)

```python
print(apple_data.head())
```

我们也可以将这个标志位绘制在图表上（使用次坐标轴），以便更直观地看到策略的“持仓”状态。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_74.png)

```python
# 绘制股价、均线及持仓标志
fig, ax1 = plt.subplots(figsize=(14, 8))

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_76.png)

ax1.plot(apple_data['AAPL'], label='AAPL Price', alpha=0.5)
ax1.plot(apple_data['short_ma'], label=f'Short MA ({short_window})')
ax1.plot(apple_data['long_ma'], label=f'Long MA ({long_window})')
ax1.set_ylabel('Price')
ax1.legend(loc='upper left')

ax2 = ax1.twinx()
ax2.plot(apple_data['position'], label='Position (1=Hold)', color='black', linestyle='--', alpha=0.7)
ax2.set_ylabel('Position')
ax2.legend(loc='upper right')

plt.title('Apple Stock with MA Crossover Strategy Signal')
plt.show()
```

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_78.png)

---

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_80.png)

## 第六步：理解策略逻辑与收益

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_82.png)

最后，我们来探讨一下这个简单策略背后的逻辑。

**被动持有策略**：如果投资者在最早的时间点买入股票并一直持有到最后，其收益完全取决于股价的最终涨跌。

**移动平均线交叉策略**：我们的策略提供了动态的信号。
*   当出现 **“黄金交叉”**（`position`从0变为1）时，策略发出**买入**信号。
*   当出现 **“死亡交叉”**（`position`从1变为0）时，策略发出**卖出**信号。

**策略优势的简单比喻**：
假设初始资金为100元。
*   **被动持有**：如果股价先跌后涨，净值可能从100元跌至80元再涨回100元，最终不赚不赔。
*   **交叉策略**：在死亡交叉点（股价开始下跌前）以100元卖出，在黄金交叉点（股价开始上涨前）以80元买入。这样，我们不仅避免了下跌损失，还用同样的资金买入了更多股份，当股价涨回100元时，我们的资产将超过100元，从而实现盈利。

这个策略的核心思想在于**试图通过均线交叉来捕捉趋势的转折点**，在上升趋势开始时持有，在下跌趋势开始时离场，以期获得超越简单买入持有的回报。

---

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_84.png)

**本节课总结**

在本节课中，我们一起学习了：
1.  **数据准备**：如何导入并预处理股票价格数据，处理缺失值。
2.  **指标计算**：如何计算短期和长期简单移动平均线（SMA）。
3.  **信号识别**：通过可视化图表识别移动平均线的“黄金交叉”和“死亡交叉”，它们分别对应潜在的买入和卖出信号。
4.  **策略标记**：如何使用`np.where`函数创建交易标志位，量化持仓状态。
5.  **逻辑理解**：理解了移动平均线交叉策略如何试图通过趋势跟踪来优化买卖时机，从而在理论上获得更好的风险调整后收益。

![](img/c1eb1f9b1eedf89c058f1823ab8fc725_86.png)

这为我们后续进行更复杂的策略回测和绩效分析打下了坚实的基础。