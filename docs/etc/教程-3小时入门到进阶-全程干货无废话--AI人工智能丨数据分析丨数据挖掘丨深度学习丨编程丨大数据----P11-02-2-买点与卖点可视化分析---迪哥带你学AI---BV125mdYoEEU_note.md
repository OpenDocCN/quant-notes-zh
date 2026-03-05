# Python金融分析与量化交易实战教程：P11：02-2-买点与卖点可视化分析 📈

![](img/34a72c30036c8d2008b995e9d7eab0d3_0.png)

![](img/34a72c30036c8d2008b995e9d7eab0d3_2.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，特别是通过计算短期和长期移动平均线来识别股票的潜在买点和卖点。我们将使用苹果公司的股价数据作为示例，通过代码演示数据处理、指标计算和结果可视化的完整流程。

![](img/34a72c30036c8d2008b995e9d7eab0d3_4.png)

---

![](img/34a72c30036c8d2008b995e9d7eab0d3_6.png)

## 第一步：导入工具包与读取数据

![](img/34a72c30036c8d2008b995e9d7eab0d3_8.png)

![](img/34a72c30036c8d2008b995e9d7eab0d3_10.png)

首先，我们需要导入必要的Python库并加载数据。我们将使用`pandas`进行数据处理，`matplotlib`进行绘图。

![](img/34a72c30036c8d2008b995e9d7eab0d3_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/34a72c30036c8d2008b995e9d7eab0d3_14.png)

接下来，读取包含股价数据的CSV文件。我们将第一列（时间）设置为索引，并解析日期格式。

![](img/34a72c30036c8d2008b995e9d7eab0d3_16.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/34a72c30036c8d2008b995e9d7eab0d3_18.png)

![](img/34a72c30036c8d2008b995e9d7eab0d3_20.png)

数据读取完成后，我们选择苹果公司（AAPL）的股价列进行分析。

![](img/34a72c30036c8d2008b995e9d7eab0d3_22.png)

```python
data = data[['AAPL']]
```

![](img/34a72c30036c8d2008b995e9d7eab0d3_24.png)

---

![](img/34a72c30036c8d2008b995e9d7eab0d3_26.png)

## 第二步：数据预处理

![](img/34a72c30036c8d2008b995e9d7eab0d3_28.png)

在开始分析之前，我们需要处理数据中可能存在的缺失值，以确保后续计算的准确性。

![](img/34a72c30036c8d2008b995e9d7eab0d3_30.png)

![](img/34a72c30036c8d2008b995e9d7eab0d3_32.png)

```python
data = data.dropna()
```

执行上述操作后，我们得到了一个干净的、只包含苹果公司股价的数据集。

![](img/34a72c30036c8d2008b995e9d7eab0d3_34.png)

---

## 第三步：计算短期与长期移动平均线

移动平均线是技术分析中常用的指标。我们将计算两个不同时间窗口的移动平均线：一个短期窗口和一个长期窗口。

![](img/34a72c30036c8d2008b995e9d7eab0d3_36.png)

以下是计算移动平均线的步骤：

![](img/34a72c30036c8d2008b995e9d7eab0d3_38.png)

1.  定义短期和长期的时间窗口大小。
2.  使用`rolling`方法计算指定窗口内的股价均值。

![](img/34a72c30036c8d2008b995e9d7eab0d3_40.png)

```python
# 定义短期和长期窗口大小
short_window = 50
long_window = 200

![](img/34a72c30036c8d2008b995e9d7eab0d3_42.png)

![](img/34a72c30036c8d2008b995e9d7eab0d3_44.png)

# 计算短期移动平均线
data['SMA_short'] = data['AAPL'].rolling(window=short_window).mean()

![](img/34a72c30036c8d2008b995e9d7eab0d3_46.png)

# 计算长期移动平均线
data['SMA_long'] = data['AAPL'].rolling(window=long_window).mean()
```

![](img/34a72c30036c8d2008b995e9d7eab0d3_48.png)

![](img/34a72c30036c8d2008b995e9d7eab0d3_50.png)

计算完成后，数据的前几行可能包含`NaN`值，这是因为移动平均计算需要足够的初始数据点。我们可以使用`tail()`方法查看最近的数据。

![](img/34a72c30036c8d2008b995e9d7eab0d3_52.png)

---

![](img/34a72c30036c8d2008b995e9d7eab0d3_54.png)

## 第四步：可视化股价与移动平均线

![](img/34a72c30036c8d2008b995e9d7eab0d3_56.png)

现在，我们可以将原始股价与计算出的两条移动平均线绘制在同一张图上，以便直观地观察它们的走势和交叉点。

![](img/34a72c30036c8d2008b995e9d7eab0d3_58.png)

```python
plt.figure(figsize=(14, 7))
plt.plot(data['AAPL'], label='AAPL Price', alpha=0.5)
plt.plot(data['SMA_short'], label=f'{short_window}-Day SMA', color='red')
plt.plot(data['SMA_long'], label=f'{long_window}-Day SMA', color='green')
plt.title('AAPL Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

在生成的图表中，我们可以观察到：
*   **黄金交叉**：当短期均线从下方上穿长期均线时，通常被视为**买入信号**。
*   **死亡交叉**：当短期均线从上方下穿长期均线时，通常被视为**卖出信号**。

---

![](img/34a72c30036c8d2008b995e9d7eab0d3_60.png)

![](img/34a72c30036c8d2008b995e9d7eab0d3_62.png)

## 第五步：标记均线位置关系

![](img/34a72c30036c8d2008b995e9d7eab0d3_64.png)

为了量化交叉信号，我们创建一个新的列来标记短期均线是否高于长期均线。这有助于我们系统性地识别买卖点。

![](img/34a72c30036c8d2008b995e9d7eab0d3_66.png)

![](img/34a72c30036c8d2008b995e9d7eab0d3_68.png)

首先，确保数据中已无缺失值。

![](img/34a72c30036c8d2008b995e9d7eab0d3_70.png)

```python
data = data.dropna()
```

然后，使用`np.where`函数创建标志位：
*   当短期均线大于长期均线时，标记为`1`。
*   否则，标记为`0`。

![](img/34a72c30036c8d2008b995e9d7eab0d3_72.png)

```python
import numpy as np
data['Position'] = np.where(data['SMA_short'] > data['SMA_long'], 1, 0)
```

![](img/34a72c30036c8d2008b995e9d7eab0d3_74.png)

我们也可以将这个标志位绘制在图表上，使用次坐标轴来清晰展示。

![](img/34a72c30036c8d2008b995e9d7eab0d3_76.png)

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/34a72c30036c8d2008b995e9d7eab0d3_78.png)

ax1.plot(data['AAPL'], label='Price', color='blue', alpha=0.5)
ax1.plot(data['SMA_short'], label='Short SMA', color='red')
ax1.plot(data['SMA_long'], label='Long SMA', color='green')
ax1.set_ylabel('Price')

ax2 = ax1.twinx()
ax2.plot(data['Position'], label='Position (1=Buy)', color='purple', alpha=0.3, drawstyle='steps-post')
ax2.set_ylabel('Position Signal')
ax2.set_ylim(-0.5, 1.5)

plt.title('AAPL Price, Moving Averages and Trading Signal')
fig.legend(loc='upper left')
plt.show()
```

![](img/34a72c30036c8d2008b995e9d7eab0d3_80.png)

---

![](img/34a72c30036c8d2008b995e9d7eab0d3_82.png)

![](img/34a72c30036c8d2008b995e9d7eab0d3_84.png)

## 第六步：理解策略逻辑

通过上述分析，我们建立了一个简单的双均线交易策略的逻辑基础：
1.  **买入**：当`Position`标志从`0`变为`1`时（黄金交叉），执行买入操作。
2.  **卖出**：当`Position`标志从`1`变为`0`时（死亡交叉），执行卖出操作。

这个策略的核心思想是**跟随趋势**。在上升趋势开始时买入，在趋势可能反转时卖出，旨在捕捉主要的价格变动，避免在下跌行情中持有头寸。

与单纯买入并持有的策略相比，这种基于信号的策略试图通过主动交易来获取更高的回报或降低风险。

---

## 总结

![](img/34a72c30036c8d2008b995e9d7eab0d3_86.png)

本节课中我们一起学习了金融数据可视化分析的关键步骤：
1.  **数据准备**：导入库、读取数据并进行预处理（处理缺失值）。
2.  **指标计算**：计算了短期和长期简单移动平均线（SMA）。
3.  **可视化分析**：绘制了股价与移动平均线图，直观识别了“黄金交叉”和“死亡交叉”。
4.  **信号生成**：通过比较两条均线，创建了交易信号标志位，并进行了可视化。

![](img/34a72c30036c8d2008b995e9d7eab0d3_88.png)

通过本课的学习，我们掌握了如何将理论上的交易信号（双均线交叉）通过Python代码转化为具体的、可视化的分析结果，为后续构建和回测量化交易策略打下了坚实的基础。