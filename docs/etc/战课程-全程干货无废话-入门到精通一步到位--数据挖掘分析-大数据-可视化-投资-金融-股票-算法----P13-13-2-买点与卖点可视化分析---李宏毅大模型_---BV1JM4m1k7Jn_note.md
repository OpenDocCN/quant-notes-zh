# Python金融分析与量化交易实战课程：13.2：买点与卖点可视化分析 📈

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_0.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_2.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_4.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，特别是通过计算和绘制移动平均线来识别股票的潜在买点和卖点。我们将使用苹果公司的股票数据作为示例，从数据预处理开始，逐步完成计算、标记和可视化分析。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_6.png)

## 数据准备与预处理

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_8.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_10.png)

首先，我们需要导入必要的工具包并加载数据。这一步是后续所有分析的基础。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_14.png)

以下是数据加载和初始处理的步骤：

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_16.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_18.png)

1.  读取数据文件，并指定第一列（时间）作为索引。
2.  解析时间索引，确保数据格式正确。
3.  从数据集中选择苹果公司（AAPL）的股票价格数据列。
4.  处理数据中可能存在的缺失值，以确保分析的准确性。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_20.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_22.png)

```python
# 读取数据，指定第一列为时间索引
data = pd.read_csv(‘data.csv‘, index_col=0, parse_dates=True)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_24.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_26.png)

# 选择苹果公司的股票价格列
apple_data = data[‘AAPL‘]

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_28.png)

# 删除缺失值
apple_data = apple_data.dropna()
```

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_30.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_32.png)

完成上述步骤后，我们便得到了一个干净、只包含苹果公司股价的`DataFrame`，可以用于后续分析。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_34.png)

## 计算短期与长期移动平均线

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_36.png)

上一节我们准备好了数据，本节中我们来看看如何计算技术分析中常用的指标——移动平均线。移动平均线能有效平滑股价波动，帮助我们观察趋势。

核心概念是计算指定时间窗口内股价的平均值。**短期移动平均线（SMA_short）**对价格变化更敏感，**长期移动平均线（SMA_long）**则更能反映长期趋势。其计算公式为：
**SMA = (P1 + P2 + ... + Pn) / n**
其中 `P` 为收盘价，`n` 为时间窗口大小。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_38.png)

考虑到我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察交叉点，我们将短期窗口设置为42天，长期窗口设置为252天（约一年交易日）。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_40.png)

```python
# 定义短期和长期移动平均线的窗口大小
short_window = 42
long_window = 252

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_42.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_44.png)

# 计算短期移动平均线
apple_data[‘SMA_short‘] = apple_data[‘AAPL‘].rolling(window=short_window).mean()

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_46.png)

# 计算长期移动平均线
apple_data[‘SMA_long‘] = apple_data[‘AAPL‘].rolling(window=long_window).mean()
```

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_48.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_50.png)

计算完成后，数据的前几行由于窗口不足会显示为缺失值（NaN），这是正常现象。我们可以查看数据尾部来确认计算成功。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_52.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_54.png)

## 可视化移动平均线与交叉点分析

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_56.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_58.png)

计算出移动平均线后，最直观的方式就是将其绘制出来进行观察。通过图表，我们可以清晰地看到股价走势以及两条均线的交叉情况。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_60.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_62.png)

以下是绘制股价与移动平均线的代码：

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_64.png)

```python
# 绘制股价和移动平均线
plt.figure(figsize=(14, 7))
plt.plot(apple_data[‘AAPL‘], label=‘AAPL Price‘, alpha=0.5)
plt.plot(apple_data[‘SMA_short‘], label=f‘{short_window}-Day SMA‘)
plt.plot(apple_data[‘SMA_long‘], label=f‘{long_window}-Day SMA‘)
plt.title(‘Apple Stock Price with Moving Averages‘)
plt.xlabel(‘Date‘)
plt.ylabel(‘Price‘)
plt.legend()
plt.show()
```

在生成的图表中，我们可以分析均线交叉点：
*   **黄金交叉**：当短期均线从下方上穿长期均线时，通常被视为**买入信号**，预示着上涨趋势可能开始。
*   **死亡交叉**：当短期均线从上方下穿长期均线时，通常被视为**卖出信号**，预示着下跌趋势可能开始。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_66.png)

通过观察图表，我们可以手动识别出这些关键的交叉点。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_68.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_70.png)

## 标记均线位置关系

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_72.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_74.png)

为了量化分析，我们需要在数据中标记出短期均线与长期均线的位置关系。这有助于我们程序化地识别交易信号。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_76.png)

在此之前，需要先删除计算移动平均线时产生的缺失值。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_78.png)

```python
# 删除因计算移动平均线而产生的缺失值
apple_data = apple_data.dropna()

# 标记位置关系：1表示短期均线高于长期均线，0表示低于
apple_data[‘Position‘] = np.where(apple_data[‘SMA_short‘] > apple_data[‘SMA_long‘], 1, 0)
```

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_80.png)

现在，数据框中新增了一列“Position”，其值为1或0，清晰地标明了每个时间点上两条均线的相对位置。当`Position`从0变为1时，对应黄金交叉（买点）；从1变为0时，对应死亡交叉（卖点）。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_82.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_84.png)

我们也可以将这一标记可视化：

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_86.png)

```python
# 创建双Y轴图，分别显示股价和位置标记
fig, ax1 = plt.subplots(figsize=(14, 7))

ax1.plot(apple_data[‘AAPL‘], label=‘AAPL Price‘, color=‘blue‘, alpha=0.6)
ax1.set_ylabel(‘Price‘, color=‘blue‘)
ax1.tick_params(axis=‘y‘, labelcolor=‘blue‘)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_88.png)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_90.png)

ax2 = ax1.twinx()
ax2.plot(apple_data[‘Position‘], label=‘Position (1=Short>Long)‘, color=‘red‘, alpha=0.3)
ax2.set_ylabel(‘Position‘, color=‘red‘)
ax2.tick_params(axis=‘y‘, labelcolor=‘red‘)

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_92.png)

plt.title(‘Stock Price with Position Signal‘)
fig.legend(loc=“upper left“)
plt.show()
```

## 策略思路：基于交叉点的模拟交易

最后，我们来探讨一个简单的策略思路。如果不采取任何策略，单纯在期初买入并持有到期末，其收益率由股价的最终涨跌决定。

然而，根据我们识别出的移动平均线交叉点，可以设计一个理论上的交易策略：
1.  **买入信号（黄金交叉）**：当`Position`列由0变为1时，买入股票。
2.  **卖出信号（死亡交叉）**：当`Position`列由1变为0时，卖出股票。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_94.png)

这个策略的核心思想是“高抛低吸”。例如，在死亡交叉点（股价可能即将下跌）卖出，锁定利润或减少亏损；在随后的黄金交叉点（股价可能即将上涨）再次买入。理想情况下，通过多次这样的操作，可以超越简单的买入持有策略，获得更高的累计回报或更好的风险调整后收益。

![](img/cdc9ef5f0e9bf76a4465dc0eec323764_96.png)

本节课中我们一起学习了如何利用Python进行金融数据的可视化分析。我们从数据预处理开始，计算了短期和长期移动平均线，并通过图表直观地分析了标识买点（黄金交叉）和卖点（死亡交叉）的交叉点。最后，我们还探讨了如何将这些交叉点转化为程序化的交易信号，为构建一个简单的量化交易策略奠定了基础。