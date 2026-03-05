# Python金融分析与量化交易实战课程：P9：9.2-买点与卖点可视化分析 📈

![](img/c61af35470d881fb95aaf981c9aec3d0_0.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_2.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_4.png)

在本节课中，我们将学习如何基于移动平均线策略，对股票数据进行可视化分析，并识别潜在的买入和卖出信号。我们将使用Python的Pandas和Matplotlib库，通过计算短期和长期移动平均线，并观察它们的交叉点来制定交易策略。

![](img/c61af35470d881fb95aaf981c9aec3d0_6.png)

---

![](img/c61af35470d881fb95aaf981c9aec3d0_8.png)

## 数据准备与预处理

![](img/c61af35470d881fb95aaf981c9aec3d0_10.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_12.png)

首先，我们需要导入必要的工具包并加载数据。我们将使用Pandas进行数据处理，Matplotlib进行可视化。

![](img/c61af35470d881fb95aaf981c9aec3d0_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/c61af35470d881fb95aaf981c9aec3d0_16.png)

接下来，我们读取股票数据文件。假设数据文件为`data.csv`，其中第一列是时间索引。

![](img/c61af35470d881fb95aaf981c9aec3d0_18.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_20.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/c61af35470d881fb95aaf981c9aec3d0_22.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_24.png)

为了简化分析，我们选择苹果公司的股票数据作为示例。同时，数据中可能存在缺失值，这会影响移动平均线的计算，因此我们需要先去除缺失值。

![](img/c61af35470d881fb95aaf981c9aec3d0_26.png)

```python
# 选择苹果公司的股票列
apple_data = data[['AAPL']].copy()
# 去除缺失值
apple_data.dropna(inplace=True)
print(apple_data.head())
```

![](img/c61af35470d881fb95aaf981c9aec3d0_28.png)

---

![](img/c61af35470d881fb95aaf981c9aec3d0_30.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_32.png)

## 计算移动平均线

上一节我们完成了数据加载和清洗。本节中，我们来看看如何计算短期和长期移动平均线。

![](img/c61af35470d881fb95aaf981c9aec3d0_34.png)

移动平均线是技术分析中常用的指标，用于平滑价格数据并识别趋势。短期移动平均线对价格变化更敏感，而长期移动平均线则反映更长期的趋势。

以下是计算移动平均线的步骤：

![](img/c61af35470d881fb95aaf981c9aec3d0_36.png)

1.  定义短期和长期的时间窗口。考虑到我们的数据跨度较长（约7-8年），我们将短期窗口设为42天，长期窗口设为252天，以便在图表中更清晰地观察趋势。
2.  使用Pandas的`rolling`和`mean`方法计算移动平均值。

```python
# 定义短期和长期窗口
short_window = 42
long_window = 252

![](img/c61af35470d881fb95aaf981c9aec3d0_38.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_40.png)

# 计算短期移动平均线
apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()
# 计算长期移动平均线
apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()

![](img/c61af35470d881fb95aaf981c9aec3d0_42.png)

print(apple_data.tail())
```

![](img/c61af35470d881fb95aaf981c9aec3d0_44.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_46.png)

由于移动平均线计算需要足够的数据点，结果的前几行会出现NaN（非数字）值，这是正常现象。

![](img/c61af35470d881fb95aaf981c9aec3d0_48.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_50.png)

---

![](img/c61af35470d881fb95aaf981c9aec3d0_52.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_54.png)

## 可视化移动平均线与交叉点

![](img/c61af35470d881fb95aaf981c9aec3d0_56.png)

计算完移动平均线后，我们可以将其可视化，以直观地观察短期和长期均线的走势及其交叉情况。

![](img/c61af35470d881fb95aaf981c9aec3d0_58.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_60.png)

```python
# 绘制股价与移动平均线
plt.figure(figsize=(10, 6))
plt.plot(apple_data['AAPL'], label='Apple Stock Price', alpha=0.5)
plt.plot(apple_data['SMA_short'], label=f'{short_window}-Day SMA', color='red')
plt.plot(apple_data['SMA_long'], label=f'{long_window}-Day SMA', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

在生成的图表中，我们可以分析均线的交叉点：
*   **黄金交叉**：当短期移动平均线从下方上穿长期移动平均线时，通常被视为**买入信号**，预示着价格可能开始上涨。
*   **死亡交叉**：当短期移动平均线从上方下穿长期移动平均线时，通常被视为**卖出信号**，预示着价格可能开始下跌。

---

![](img/c61af35470d881fb95aaf981c9aec3d0_62.png)

## 标记买卖信号

![](img/c61af35470d881fb95aaf981c9aec3d0_64.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_66.png)

上一节我们通过图表观察了交叉点。本节中，我们将通过编程方式自动标记这些潜在的买卖信号。

![](img/c61af35470d881fb95aaf981c9aec3d0_68.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_70.png)

首先，我们需要确保数据中不再含有NaN值，然后创建一个标志位来记录短期均线是否高于长期均线。

![](img/c61af35470d881fb95aaf981c9aec3d0_72.png)

```python
# 去除因计算移动平均线产生的NaN值
apple_data.dropna(inplace=True)

![](img/c61af35470d881fb95aaf981c9aec3d0_74.png)

# 创建标志位：1表示短期均线高于长期均线，0表示低于
apple_data['Position'] = np.where(apple_data['SMA_short'] > apple_data['SMA_long'], 1, 0)
print(apple_data.head())
```

现在，我们可以将买卖信号（即`Position`列的变化点）也绘制在图上。

![](img/c61af35470d881fb95aaf981c9aec3d0_76.png)

```python
plt.figure(figsize=(10, 6))
plt.plot(apple_data['AAPL'], label='Apple Stock Price', alpha=0.5)
plt.plot(apple_data['SMA_short'], label=f'{short_window}-Day SMA')
plt.plot(apple_data['SMA_long'], label=f'{long_window}-Day SMA')

![](img/c61af35470d881fb95aaf981c9aec3d0_78.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_80.png)

# 在次坐标轴上绘制买卖信号标志位
plt.twinx()
plt.plot(apple_data['Position'], label='Trading Signal (Buy/Sell)', color='gray', alpha=0.3, drawstyle='steps-post')
plt.ylabel('Signal (1=Buy, 0=Sell)')
plt.legend(loc='upper left')

plt.title('Apple Stock Price with Trading Signals')
plt.show()
```

---

![](img/c61af35470d881fb95aaf981c9aec3d0_82.png)

## 理解策略逻辑

![](img/c61af35470d881fb95aaf981c9aec3d0_84.png)

![](img/c61af35470d881fb95aaf981c9aec3d0_86.png)

通过上述分析，我们得到了基于双移动平均线交叉的交易信号。这个策略的核心逻辑是：
*   当`Position`标志从0变为1时（黄金交叉），执行**买入**操作。
*   当`Position`标志从1变为0时（死亡交叉），执行**卖出**操作。

这种策略试图通过捕捉趋势的早期变化来获利。与单纯买入并持有的策略相比，它旨在通过主动的买卖操作，在价格下跌前卖出，在价格上涨前买入，从而可能获得更高的回报或降低风险。

例如，假设初始资金为100元。在价格高点（死亡交叉信号）卖出股票，然后在随后的低点（黄金交叉信号）再次买入。即使股价从100元跌至80元，通过这次“高卖低买”的操作，投资者实际上可能实现了盈利，因为他在100元时持有了现金，并在80元时买入了更多股份。

---

## 总结

![](img/c61af35470d881fb95aaf981c9aec3d0_88.png)

本节课中我们一起学习了如何利用Python进行金融数据的可视化分析，并实现了一个简单的双移动平均线交易策略。我们完成了以下步骤：
1.  **数据准备**：导入并清洗股票数据，处理缺失值。
2.  **指标计算**：计算了短期和长期移动平均线。
3.  **可视化分析**：绘制了股价和移动平均线，直观识别了“黄金交叉”和“死亡交叉”。
4.  **信号标记**：通过编程创建了买卖信号标志位，并进行了可视化。
5.  **策略理解**：探讨了基于移动平均线交叉的交易策略的基本逻辑，即通过识别趋势变化点来指导买卖决策，旨在提升投资回报。

![](img/c61af35470d881fb95aaf981c9aec3d0_90.png)

这为我们后续进行更复杂的量化回测和策略评估奠定了基础。