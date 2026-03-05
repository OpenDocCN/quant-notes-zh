# 金融数据分析：P16：2-买点与卖点可视化分析 📈

![](img/6e4638893084dc81d6bcd61ebcd87ed3_0.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_2.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，特别是通过计算短期和长期移动平均线来识别股票的潜在买点和卖点。我们将从数据预处理开始，逐步计算技术指标，并最终通过图表直观地展示交易信号。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_4.png)

---

![](img/6e4638893084dc81d6bcd61ebcd87ed3_6.png)

## 第一步：导入工具包与读取数据

![](img/6e4638893084dc81d6bcd61ebcd87ed3_8.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_10.png)

首先，我们需要导入必要的Python库并加载我们的股票数据。

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_12.png)

接下来，我们读取包含股票历史价格的数据文件。这里假设数据文件为`data.csv`，并且第一列是日期，我们将它设置为索引。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_14.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_16.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_18.png)

数据读取完成后，我们选择苹果公司（AAPL）的股票价格作为分析示例。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_20.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_22.png)

```python
# 选择苹果公司的股票价格列，这里假设列名为‘AAPL’
stock_data = data[['AAPL']].copy()
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_24.png)

---

## 第二步：数据预处理

![](img/6e4638893084dc81d6bcd61ebcd87ed3_26.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_28.png)

在开始分析之前，我们需要处理数据中可能存在的缺失值，以确保后续计算的准确性。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_30.png)

以下是数据预处理步骤：
1.  选择目标股票的数据列。
2.  使用`dropna()`方法移除所有包含缺失值的行。

```python
# 移除缺失值
stock_data.dropna(inplace=True)
# 查看处理后的数据前几行
print(stock_data.head())
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_32.png)

经过预处理，我们得到了一个干净、连续的苹果公司股价序列。

---

![](img/6e4638893084dc81d6bcd61ebcd87ed3_34.png)

## 第三步：计算移动平均线

上一节我们准备好了数据，本节中我们来看看如何计算技术指标。我们将计算短期和长期简单移动平均线（SMA），这是识别趋势和交叉点的关键。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_36.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_38.png)

首先，定义短期和长期的时间窗口。由于我们的数据跨度较长（约7-8年），为了在图表中更清晰地观察，我们将窗口设置得比通常的5日和20日更大一些。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_40.png)

```python
# 定义短期和长期窗口
short_window = 40
long_window = 100
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_42.png)

接下来，我们使用Pandas的`rolling()`和`mean()`方法计算移动平均线，并将结果作为新列添加到数据框中。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_44.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_46.png)

```python
# 计算短期移动平均线
stock_data['SMA_Short'] = stock_data['AAPL'].rolling(window=short_window).mean()
# 计算长期移动平均线
stock_data['SMA_Long'] = stock_data['AAPL'].rolling(window=long_window).mean()
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_48.png)

计算完成后，我们可以查看数据的尾部，确认移动平均线已成功计算。由于移动平均计算需要足够的数据点，前`long_window-1`行会出现NaN值，这是正常现象。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_50.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_52.png)

```python
print(stock_data.tail())
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_54.png)

---

![](img/6e4638893084dc81d6bcd61ebcd87ed3_56.png)

## 第四步：可视化股价与移动平均线

![](img/6e4638893084dc81d6bcd61ebcd87ed3_58.png)

计算完指标后，我们可以通过绘图来直观地观察股价与两条移动平均线的关系。

以下是绘图步骤：
1.  创建一个图形。
2.  分别绘制原始股价、短期均线和长期均线。
3.  添加图例和标题。

```python
plt.figure(figsize=(14, 7))
plt.plot(stock_data['AAPL'], label='AAPL Price', color='red', alpha=0.5)
plt.plot(stock_data['SMA_Short'], label=f'{short_window}-Day SMA', color='blue')
plt.plot(stock_data['SMA_Long'], label=f'{long_window}-Day SMA', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_60.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_62.png)

在生成的图表中，我们可以寻找短期均线（蓝线）与长期均线（绿线）的交叉点：
*   **黄金交叉**：短期均线从下向上穿越长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿越长期均线，通常被视为**卖出信号**。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_64.png)

---

![](img/6e4638893084dc81d6bcd61ebcd87ed3_66.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_68.png)

## 第五步：标记交易信号

![](img/6e4638893084dc81d6bcd61ebcd87ed3_70.png)

为了量化这些交叉点，我们需要创建一个标志位来记录短期均线是否高于长期均线。

首先，确保移除计算移动平均线时产生的缺失值。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_72.png)

```python
stock_data.dropna(inplace=True)
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_74.png)

然后，使用`np.where()`函数创建`Position`列。当短期均线大于长期均线时，标记为1，否则标记为0。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_76.png)

```python
import numpy as np
stock_data['Position'] = np.where(stock_data['SMA_Short'] > stock_data['SMA_Long'], 1, 0)
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_78.png)

现在，数据框中新增了一列`Position`，它清晰地表明了每个时间点上我们的理论持仓状态（1代表应持有，0代表应空仓）。

```python
print(stock_data[['AAPL', 'SMA_Short', 'SMA_Long', 'Position']].head())
```

我们还可以将持仓位置单独绘制出来，以便更清晰地观察策略信号的变化。

![](img/6e4638893084dc81d6bcd61ebcd87ed3_80.png)

```python
plt.figure(figsize=(14, 7))
plt.plot(stock_data['Position'], label='Trading Signal (Position)', color='purple')
plt.title('Buy/Sell Signals Based on Moving Average Crossover')
plt.xlabel('Date')
plt.ylabel('Position (1=Buy, 0=Sell)')
plt.legend()
plt.show()
```

![](img/6e4638893084dc81d6bcd61ebcd87ed3_82.png)

![](img/6e4638893084dc81d6bcd61ebcd87ed3_84.png)

---

## 第六步：理解策略逻辑

本节中，我们来探讨一下这个均线交叉策略的核心逻辑。其基本思想是“趋势跟踪”。

*   **买入逻辑**：当`Position`从0变为1时（黄金交叉），表明短期趋势转强，可能开启上涨行情，因此产生买入信号。
*   **卖出逻辑**：当`Position`从1变为0时（死亡交叉），表明短期趋势转弱，可能开启下跌行情，因此产生卖出信号。

这个策略的目标并非在最高点卖出、最低点买入，而是通过跟随主要趋势，在上涨初期买入，在下跌初期卖出，从而规避大的下跌风险，捕获主要的上涨波段。理论上，这可以改善“买入并持有”策略的收益风险比。

---

![](img/6e4638893084dc81d6bcd61ebcd87ed3_86.png)

## 总结

![](img/6e4638893084dc81d6bcd61ebcd87ed3_88.png)

本节课中我们一起学习了如何利用Python实现一个简单的双移动平均线交叉策略，并进行可视化分析。我们从数据导入和清洗开始，逐步计算了短期与长期移动平均线，在图表上识别了潜在的“黄金交叉”和“死亡交叉”点。接着，我们创建了交易信号标志位，将策略逻辑量化。这个过程展示了如何将基础的技术分析思想转化为可执行、可验证的代码，为后续进行更复杂的量化回测和策略优化打下了坚实的基础。