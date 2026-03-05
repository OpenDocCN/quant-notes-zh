# 金融量化交易实战：P13：2-买点与卖点可视化分析 📈

![](img/23c54c1ec82042d7281eefa701ba8c5c_0.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_2.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_4.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，特别是通过计算短期和长期移动平均线来识别股票的潜在买点与卖点。我们将从数据预处理开始，逐步计算技术指标，并最终通过图表直观地展示交易信号。

![](img/23c54c1ec82042d7281eefa701ba8c5c_6.png)

---

![](img/23c54c1ec82042d7281eefa701ba8c5c_8.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_10.png)

## 数据准备与预处理

![](img/23c54c1ec82042d7281eefa701ba8c5c_12.png)

首先，我们需要导入必要的工具包并加载数据。以下是核心步骤：

![](img/23c54c1ec82042d7281eefa701ba8c5c_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/23c54c1ec82042d7281eefa701ba8c5c_16.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_18.png)

接下来，我们读取股票数据文件。数据的第一列是时间，我们将其设置为索引并解析为日期时间格式。

![](img/23c54c1ec82042d7281eefa701ba8c5c_20.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/23c54c1ec82042d7281eefa701ba8c5c_22.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_24.png)

为了简化分析，我们选择苹果公司的股票数据作为示例，并处理数据中可能存在的缺失值。

![](img/23c54c1ec82042d7281eefa701ba8c5c_26.png)

```python
# 选择苹果公司的股票列
apple_data = data[['AAPL']].copy()
# 删除缺失值
apple_data.dropna(inplace=True)
# 查看前几行数据
print(apple_data.head())
```

![](img/23c54c1ec82042d7281eefa701ba8c5c_28.png)

完成以上步骤后，我们得到了一个干净、只包含苹果股票价格的数据集。

![](img/23c54c1ec82042d7281eefa701ba8c5c_30.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_32.png)

---

## 计算移动平均线

![](img/23c54c1ec82042d7281eefa701ba8c5c_34.png)

上一节我们准备好了数据，本节中我们来看看如何计算技术指标。移动平均线是分析股票趋势的常用工具。我们需要分别计算短期和长期移动平均线。

首先，定义短期和长期的时间窗口。由于我们的数据集跨度较大（约7-8年），为了在图表中更清晰地观察，我们将窗口值设置得稍大一些。

![](img/23c54c1ec82042d7281eefa701ba8c5c_36.png)

```python
short_window = 40
long_window = 100
```

![](img/23c54c1ec82042d7281eefa701ba8c5c_38.png)

接着，我们使用Pandas的滚动窗口函数计算移动平均线，并将结果添加到数据框中。

![](img/23c54c1ec82042d7281eefa701ba8c5c_40.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_42.png)

```python
# 计算短期移动平均线
apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()
# 计算长期移动平均线
apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()
# 查看数据尾部，确认计算成功
print(apple_data.tail())
```

![](img/23c54c1ec82042d7281eefa701ba8c5c_44.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_46.png)

计算完成后，数据的前部会出现一些缺失值，这是因为最初的几十个数据点不足以构成完整的计算窗口，这是正常现象。

![](img/23c54c1ec82042d7281eefa701ba8c5c_48.png)

---

![](img/23c54c1ec82042d7281eefa701ba8c5c_50.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_52.png)

## 可视化移动平均线与交叉点

![](img/23c54c1ec82042d7281eefa701ba8c5c_54.png)

计算完指标后，我们可以通过绘图来直观地观察股价与两条均线的关系。

![](img/23c54c1ec82042d7281eefa701ba8c5c_56.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_58.png)

```python
# 绘制股价与移动平均线
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='Apple Stock Price', alpha=0.5)
plt.plot(apple_data['SMA_short'], label=f'Short SMA ({short_window} days)', color='red')
plt.plot(apple_data['SMA_long'], label=f'Long SMA ({long_window} days)', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/23c54c1ec82042d7281eefa701ba8c5c_60.png)

在生成的图表中，红色线代表短期均线，绿色线代表长期均线。我们需要关注两条线的交叉点：
*   **黄金交叉**：短期均线从下向上穿过长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿过长期均线，通常被视为**卖出信号**。

图表中可以清晰地看到几个这样的交叉点，它们标出了潜在的市场转折位置。

![](img/23c54c1ec82042d7281eefa701ba8c5c_62.png)

---

![](img/23c54c1ec82042d7281eefa701ba8c5c_64.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_66.png)

## 标记买卖信号位置

![](img/23c54c1ec82042d7281eefa701ba8c5c_68.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_70.png)

为了进行量化分析，我们需要在数据中明确标记出这些信号。首先，确保数据中没有缺失值干扰我们的判断。

![](img/23c54c1ec82042d7281eefa701ba8c5c_72.png)

```python
# 删除因计算移动平均线而产生的缺失值
apple_data.dropna(inplace=True)
```

然后，我们创建一个新的列 `Position` 作为标志位。当短期均线高于长期均线时，标记为1（持有或买入信号）；反之则标记为0（卖出或空仓信号）。

![](img/23c54c1ec82042d7281eefa701ba8c5c_74.png)

```python
# 创建买卖信号标志位
apple_data['Position'] = np.where(apple_data['SMA_short'] > apple_data['SMA_long'], 1, 0)
# 查看标记后的数据
print(apple_data.head())
```

![](img/23c54c1ec82042d7281eefa701ba8c5c_76.png)

我们也可以将这个标志位绘制在图表上，以便更直观地对比。

![](img/23c54c1ec82042d7281eefa701ba8c5c_78.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_80.png)

```python
# 绘制股价、均线及买卖信号区域
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

![](img/23c54c1ec82042d7281eefa701ba8c5c_82.png)

ax1.plot(apple_data['AAPL'], label='Price', color='blue', alpha=0.6)
ax1.plot(apple_data['SMA_short'], label=f'Short SMA', color='red')
ax1.plot(apple_data['SMA_long'], label=f'Long SMA', color='green')
ax1.set_ylabel('Price')
ax1.legend()
ax1.set_title('Apple Stock Price and Moving Averages')

ax2.fill_between(apple_data.index, 0, apple_data['Position'], color='grey', alpha=0.3, label='Buy Signal (Position=1)')
ax2.set_ylabel('Position')
ax2.set_xlabel('Date')
ax2.legend()
plt.show()
```

![](img/23c54c1ec82042d7281eefa701ba8c5c_84.png)

在上方的图表中，灰色区域表示 `Position=1` 的时期，即短期均线在长期均线之上，理论上应持有股票。

![](img/23c54c1ec82042d7281eefa701ba8c5c_86.png)

![](img/23c54c1ec82042d7281eefa701ba8c5c_88.png)

---

## 理解策略逻辑：为何能持续盈利？

最后，我们来探讨基于此策略的交易逻辑。如果不采取任何策略，单纯地“买入并持有”，投资者的收益将完全随股价波动。

然而，双均线策略提供了动态的买卖信号。其核心优势在于：
*   当出现**死亡交叉**（卖出信号）时，策略指示我们卖出股票，**锁定现有利润或减少亏损**。
*   当出现**黄金交叉**（买入信号）时，策略指示我们再次买入股票，**重新进入市场**。

这样，通过主动的“高卖低买”（相对于趋势转折点），策略旨在捕捉主要趋势，避免在长期下跌趋势中持有资产，从而有望在长期获得比简单持有更高的回报。当然，这依赖于信号的有效性和没有交易成本的理想假设。

![](img/23c54c1ec82042d7281eefa701ba8c5c_90.png)

---

![](img/23c54c1ec82042d7281eefa701ba8c5c_92.png)

本节课中我们一起学习了如何利用Python进行金融数据的可视化分析。我们从数据预处理开始，计算了短期和长期移动平均线，并通过图表直观地识别了黄金交叉和死亡交叉这两个关键的买卖信号。最后，我们创建了买卖信号标志位，并探讨了基于双均线交叉的交易策略的基本逻辑。这为后续构建和回测量化交易模型打下了坚实的基础。