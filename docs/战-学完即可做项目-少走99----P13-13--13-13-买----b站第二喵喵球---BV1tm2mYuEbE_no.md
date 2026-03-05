# 量化交易：13：买点与卖点可视化分析 📈

![](img/c820bc9e1d94965ebd71ea0c769fedc0_0.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_2.png)

在本节课中，我们将学习如何通过计算短期和长期移动平均线，来识别股票交易中的潜在买点和卖点，并将这些关键点进行可视化分析。我们将以苹果公司的股票数据为例，一步步完成数据处理、指标计算和结果展示。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_4.png)

---

![](img/c820bc9e1d94965ebd71ea0c769fedc0_6.png)

## 概述与数据准备

![](img/c820bc9e1d94965ebd71ea0c769fedc0_8.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_10.png)

首先，我们需要导入必要的工具包并加载数据。我们将使用Pandas进行数据处理，Matplotlib进行绘图。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_12.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

接下来，读取包含股票价格的历史数据文件。我们将第一列指定为时间索引，并解析时间格式。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_16.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/c820bc9e1d94965ebd71ea0c769fedc0_18.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_20.png)

数据加载后，我们选择苹果公司（AAPL）的股票价格列作为分析对象。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_22.png)

```python
apple_data = data[['AAPL']].copy()
```

![](img/c820bc9e1d94965ebd71ea0c769fedc0_24.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_26.png)

在开始分析前，一个重要的预处理步骤是处理缺失值。缺失值可能会影响后续移动平均线的计算。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_28.png)

```python
apple_data.dropna(inplace=True)
```

![](img/c820bc9e1d94965ebd71ea0c769fedc0_30.png)

现在，我们已经准备好了用于分析的、干净的苹果公司股票价格数据。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_32.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_34.png)

---

![](img/c820bc9e1d94965ebd71ea0c769fedc0_36.png)

## 计算移动平均线

![](img/c820bc9e1d94965ebd71ea0c769fedc0_38.png)

上一节我们准备好了数据，本节中我们来看看如何计算关键的移动平均线指标。移动平均线是技术分析中常用的工具，用于平滑价格数据并识别趋势。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_40.png)

以下是计算移动平均线的步骤：

1.  **定义时间窗口**：首先，我们需要设定短期和长期移动平均线的计算周期。考虑到我们的数据时间跨度较长（约7-8年），为了在图表中更清晰地观察，我们将短期窗口设为42天，长期窗口设为252天。在实际操作中，短期常用5天，长期常用20天。
    ```python
    short_window = 42
    long_window = 252
    ```

2.  **计算短期移动平均线**：使用`rolling`方法创建滑动窗口，并计算窗口内价格的均值。
    ```python
    apple_data['SMA_Short'] = apple_data['AAPL'].rolling(window=short_window).mean()
    ```

![](img/c820bc9e1d94965ebd71ea0c769fedc0_42.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_44.png)

3.  **计算长期移动平均线**：用同样的方法计算长期移动平均线。
    ```python
    apple_data['SMA_Long'] = apple_data['AAPL'].rolling(window=long_window).mean()
    ```

计算完成后，数据的前几行由于窗口不足会显示为缺失值（NaN），这是正常现象。我们可以查看数据的尾部来确认计算成功。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_46.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_48.png)

```python
print(apple_data.tail())
```

![](img/c820bc9e1d94965ebd71ea0c769fedc0_50.png)

---

![](img/c820bc9e1d94965ebd71ea0c769fedc0_52.png)

## 可视化移动平均线与交叉点

![](img/c820bc9e1d94965ebd71ea0c769fedc0_54.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_56.png)

计算完移动平均线后，我们可以通过绘图来直观地观察它们的走势以及相互之间的关系。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_58.png)

```python
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='AAPL Price', alpha=0.5)
plt.plot(apple_data['SMA_Short'], label=f'SMA {short_window}', color='red')
plt.plot(apple_data['SMA_Long'], label=f'SMA {long_window}', color='green')
plt.legend()
plt.show()
```

![](img/c820bc9e1d94965ebd71ea0c769fedc0_60.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_62.png)

在生成的图表中，我们可以观察到：
*   **原始股价**（通常颜色较浅）的波动。
*   **短期均线**（红色）对价格变化反应更敏感，波动较大。
*   **长期均线**（绿色）更加平滑，代表长期趋势。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_64.png)

关键点在于两条均线的**交叉**：
*   **黄金交叉**：当短期均线从下方上穿长期均线时，通常被视为**买入信号**。
*   **死亡交叉**：当短期均线从上方下穿长期均线时，通常被视为**卖出信号**。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_66.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_68.png)

在图表中寻找这些交叉点，就是我们在寻找的潜在买卖点。

---

## 标记买卖信号

![](img/c820bc9e1d94965ebd71ea0c769fedc0_70.png)

为了量化这些交叉点，我们需要创建一个信号列。这个信号将明确指示在每一个时间点，根据我们的双均线策略，应该是持有（短期趋势向好）还是观望（短期趋势向坏）。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_72.png)

首先，确保我们已经去除了计算移动平均线时产生的缺失值。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_74.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_76.png)

```python
apple_data.dropna(inplace=True)
```

![](img/c820bc9e1d94965ebd71ea0c769fedc0_78.png)

接着，我们使用`np.where`函数来创建信号列。规则是：当短期均线高于长期均线时，标记为1（买入/持有信号）；否则标记为0（卖出/观望信号）。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_80.png)

```python
import numpy as np
apple_data['Signal'] = np.where(apple_data['SMA_Short'] > apple_data['SMA_Long'], 1, 0)
```

![](img/c820bc9e1d94965ebd71ea0c769fedc0_82.png)

现在，数据框中新增了一列`Signal`，我们可以通过绘图将股价、均线和买卖信号同时展示出来。

```python
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_84.png)

# 第一个子图：价格与均线
ax1.plot(apple_data['AAPL'], label='Price', alpha=0.5)
ax1.plot(apple_data['SMA_Short'], label=f'SMA {short_window}', color='red')
ax1.plot(apple_data['SMA_Long'], label=f'SMA {long_window}', color='green')
ax1.set_ylabel('Price')
ax1.legend()
ax1.set_title('AAPL Price with Moving Averages')

![](img/c820bc9e1d94965ebd71ea0c769fedc0_86.png)

# 第二个子图：买卖信号
ax2.plot(apple_data['Signal'], label='Trading Signal', color='purple', drawstyle='steps-post')
ax2.set_ylabel('Signal (1=Buy, 0=Sell)')
ax2.set_xlabel('Date')
ax2.legend()
ax2.set_ylim(-0.5, 1.5)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_88.png)

plt.show()
```

---

## 策略逻辑与收益思考

![](img/c820bc9e1d94965ebd71ea0c769fedc0_90.png)

![](img/c820bc9e1d94965ebd71ea0c769fedc0_92.png)

通过前面的步骤，我们已经能够系统地识别出基于双均线的买卖点。现在我们来思考这个策略的意义。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_94.png)

假设我们有两种投资方式：
1.  **买入并持有**：在期初买入股票并一直持有到期末，其收益完全取决于股价的最终涨跌。
2.  **双均线策略**：根据我们生成的`Signal`进行交易。当信号为1时买入或持有，当信号变为0时卖出。这样，我们试图在下跌趋势开始前卖出，在上涨趋势开始时买入。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_96.png)

双均线策略的核心优势在于它试图**规避大的下跌趋势**。例如，在一次股价下跌中，买入并持有者会承受损失。而双均线交易者则可能在下跌初期就卖出股票，保留现金，并在趋势重新转好时再次买入。这样，他不仅避免了部分损失，还可能通过低买高卖获得额外收益。

因此，对比“买入并持有”的收益，回测我们这个双均线策略的收益，是验证其有效性的关键。这将是后续课程中要进行的工作：计算策略的累计回报率，并与基准进行比较。

---

## 总结

本节课中我们一起学习了量化交易中一个基础且重要的策略——双移动平均线交叉策略的核心分析方法。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_98.png)

我们首先完成了数据导入和预处理，选择了苹果公司的股价数据并清除了缺失值。接着，我们定义了短期和长期窗口，计算了相应的简单移动平均线。通过可视化图表，我们直观地观察了股价与均线的走势，并理解了“黄金交叉”和“死亡交叉”作为买卖信号的含义。然后，我们通过创建信号列（Signal），将交叉点的判断量化成了具体的交易指令（1为买入/持有，0为卖出/观望）。最后，我们探讨了该策略试图通过趋势判断来规避风险、提升潜在收益的基本逻辑。

![](img/c820bc9e1d94965ebd71ea0c769fedc0_100.png)

通过本课的学习，你已经掌握了将理论交易策略转化为具体代码和可视化分析结果的基本流程，这是构建更复杂量化模型的重要一步。