# Python金融分析量化交易：P13：2-买点与卖点可视化分析 📈

![](img/7125b245ef60cef0f44ec8431be9fdfc_0.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_2.png)

在本节课中，我们将学习如何利用双均线策略（短期均线与长期均线）来识别股票的潜在买点和卖点，并通过Python进行可视化分析。我们将以苹果公司的股价数据为例，从数据预处理、计算均线到策略信号标记，一步步完成分析。

![](img/7125b245ef60cef0f44ec8431be9fdfc_4.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_6.png)

## 数据准备与预处理

![](img/7125b245ef60cef0f44ec8431be9fdfc_8.png)

首先，我们需要导入必要的工具包并加载数据。

![](img/7125b245ef60cef0f44ec8431be9fdfc_10.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

读取包含多只股票历史价格的数据文件，并将第一列（时间）设置为索引。

![](img/7125b245ef60cef0f44ec8431be9fdfc_14.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_16.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/7125b245ef60cef0f44ec8431be9fdfc_18.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_20.png)

我们的目标是分析苹果公司（AAPL）的股价。因此，我们从数据集中提取出苹果的股价列。

![](img/7125b245ef60cef0f44ec8431be9fdfc_22.png)

```python
apple_data = data[['AAPL']].copy()
```

![](img/7125b245ef60cef0f44ec8431be9fdfc_24.png)

数据中可能存在缺失值，这会影响后续的滚动窗口计算。因此，第一步是删除这些缺失值。

![](img/7125b245ef60cef0f44ec8431be9fdfc_26.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_28.png)

```python
apple_data.dropna(inplace=True)
```

![](img/7125b245ef60cef0f44ec8431be9fdfc_30.png)

执行上述操作后，我们便得到了一个干净、只包含苹果股价的`DataFrame`。

![](img/7125b245ef60cef0f44ec8431be9fdfc_32.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_34.png)

## 计算短期与长期均线

上一节我们准备好了数据，本节中我们来看看如何计算移动平均线。双均线策略的核心是计算两条不同周期的移动平均线，通常一条是短期均线（如5日），另一条是长期均线（如20日）。由于我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察交叉点，我们将短期窗口设置为42天，长期窗口设置为252天。

![](img/7125b245ef60cef0f44ec8431be9fdfc_36.png)

以下是计算均线的步骤：

1.  定义短期和长期的窗口大小。
2.  使用`rolling`方法计算指定窗口内的收盘价均值。

![](img/7125b245ef60cef0f44ec8431be9fdfc_38.png)

```python
# 定义窗口大小
short_window = 42
long_window = 252

# 计算短期均线 (SMA - Simple Moving Average)
apple_data['SMA_Short'] = apple_data['AAPL'].rolling(window=short_window).mean()

![](img/7125b245ef60cef0f44ec8431be9fdfc_40.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_42.png)

# 计算长期均线
apple_data['SMA_Long'] = apple_data['AAPL'].rolling(window=long_window).mean()
```

![](img/7125b245ef60cef0f44ec8431be9fdfc_44.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_46.png)

计算完成后，数据的前面部分会出现许多`NaN`值，这是因为最初的`short_window-1`和`long_window-1`个数据点不足以形成一个完整的计算窗口。我们可以查看一下数据的尾部以确认计算成功。

![](img/7125b245ef60cef0f44ec8431be9fdfc_48.png)

```python
print(apple_data.tail())
```

![](img/7125b245ef60cef0f44ec8431be9fdfc_50.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_52.png)

## 可视化均线与交叉点

![](img/7125b245ef60cef0f44ec8431be9fdfc_54.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_56.png)

计算完均线后，我们可以通过绘图来直观地观察股价走势以及两条均线的交叉情况。

![](img/7125b245ef60cef0f44ec8431be9fdfc_58.png)

```python
# 绘制股价与双均线
apple_data[['AAPL', 'SMA_Short', 'SMA_Long']].plot(figsize=(15, 8))
plt.title('Apple Stock Price with Moving Averages')
plt.ylabel('Price')
plt.show()
```

![](img/7125b245ef60cef0f44ec8431be9fdfc_60.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_62.png)

在生成的图表中，我们可以观察到：
*   **原始股价**（通常为蓝色或红色线）的波动。
*   **短期均线**（如红色线）对价格变化反应更敏感，波动较大。
*   **长期均线**（如绿色线）更加平滑，代表长期趋势。

**关键点在于两条均线的交叉**：
*   **黄金交叉**：当短期均线从下向上穿越长期均线时，通常被视为**买入信号**。
*   **死亡交叉**：当短期均线从上向下穿越长期均线时，通常被视为**卖出信号**。

在图中寻找这些交叉点，便是我们策略的基础。

![](img/7125b245ef60cef0f44ec8431be9fdfc_64.png)

## 标记交易信号

![](img/7125b245ef60cef0f44ec8431be9fdfc_66.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_68.png)

为了量化这些交叉点，我们需要在数据中创建一个标志位，来明确指示每个时间点是应该持有（短期均线高于长期均线）还是应该观望/卖出（短期均线低于长期均线）。

![](img/7125b245ef60cef0f44ec8431be9fdfc_70.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_72.png)

但在创建标志位之前，需要先剔除均线计算产生的`NaN`值，以确保数据的一致性。

![](img/7125b245ef60cef0f44ec8431be9fdfc_74.png)

```python
# 删除因滚动计算产生的缺失值
apple_data.dropna(inplace=True)
```

接下来，我们创建`Position`列。当短期均线大于长期均线时，标记为`1`（代表持有或买入信号）；否则标记为`0`（代表卖出或空仓信号）。

![](img/7125b245ef60cef0f44ec8431be9fdfc_76.png)

```python
apple_data['Position'] = np.where(apple_data['SMA_Short'] > apple_data['SMA_Long'], 1, 0)
```

![](img/7125b245ef60cef0f44ec8431be9fdfc_78.png)

我们可以再次绘图，这次将买卖信号（`Position`）在单独的Y轴上显示，以便对比。

![](img/7125b245ef60cef0f44ec8431be9fdfc_80.png)

![](img/7125b245ef60cef0f44ec8431be9fdfc_82.png)

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

ax1.plot(apple_data['AAPL'], label='Apple Price', alpha=0.5)
ax1.plot(apple_data['SMA_Short'], label=f'{short_window}-Day SMA')
ax1.plot(apple_data['SMA_Long'], label=f'{long_window}-Day SMA')
ax1.set_ylabel('Price')
ax1.legend(loc='upper left')

![](img/7125b245ef60cef0f44ec8431be9fdfc_84.png)

ax2 = ax1.twinx()
ax2.plot(apple_data['Position'], label='Trading Signal (1=Hold/Buy)', color='black', linestyle='--', alpha=0.7)
ax2.set_ylabel('Signal')
ax2.legend(loc='upper right')

![](img/7125b245ef60cef0f44ec8431be9fdfc_86.png)

plt.title('Apple Stock Price with Trading Signals')
plt.show()
```

![](img/7125b245ef60cef0f44ec8431be9fdfc_88.png)

## 策略逻辑与收益思考

![](img/7125b245ef60cef0f44ec8431be9fdfc_90.png)

通过可视化，我们已经能清晰地看到策略发出的买卖信号。现在我们来理解这个策略如何创造收益。

![](img/7125b245ef60cef0f44ec8431be9fdfc_92.png)

假设我们有两种投资方式：
1.  **买入并持有**：在最初的时间点投入100元购买股票，并一直持有到最后，期间不做任何操作。最终的收益完全取决于股价的涨跌。
2.  **双均线策略**：根据我们标记的`Position`信号进行操作。当信号为`1`时，我们持有股票；当信号变为`0`时，我们卖出股票持有现金，直到信号再次变为`1`时买入。

双均线策略的核心优势在于它试图**规避大的下跌趋势**。例如，在股价长期下跌（死亡交叉出现）前，策略会发出卖出信号，使我们逃离部分下跌；而在趋势重新向好（黄金交叉出现）时，又提示我们买入。这样，通过多次“高卖低买”的循环，策略旨在提升整体收益率，降低单纯持有带来的回撤风险。

在接下来的课程中，我们将具体计算并对比这两种方式的累计收益，从而验证双均线策略的有效性。

## 总结

![](img/7125b245ef60cef0f44ec8431be9fdfc_94.png)

本节课中我们一起学习了双均线策略的分析流程：
1.  **数据预处理**：提取目标股票数据并处理缺失值。
2.  **计算均线**：使用`rolling().mean()`方法计算短期和长期移动平均线。
3.  **可视化分析**：绘制股价与双均线图，直观识别“黄金交叉”和“死亡交叉”。
4.  **信号标记**：通过比较双均线大小，在数据中创建买卖信号标志位。
5.  **策略理解**：理解了双均线策略通过捕捉趋势转换来试图获得超额收益的基本逻辑。

![](img/7125b245ef60cef0f44ec8431be9fdfc_96.png)

我们已为后续的收益回测和策略评估打下了坚实的基础。