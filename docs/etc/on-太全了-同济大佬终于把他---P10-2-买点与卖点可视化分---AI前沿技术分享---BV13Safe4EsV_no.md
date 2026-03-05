# 量化投资入门：P10：2-买点与卖点可视化分析 📈

![](img/58308fbbb42fc5e84cfc21153944b716_0.png)

![](img/58308fbbb42fc5e84cfc21153944b716_2.png)

在本节课中，我们将学习如何使用Python进行股票数据的可视化分析，并基于移动平均线（均线）策略来识别潜在的买入和卖出信号。我们将从数据导入和预处理开始，逐步计算短期与长期均线，并最终通过图表直观地展示买卖点。

![](img/58308fbbb42fc5e84cfc21153944b716_4.png)

---

![](img/58308fbbb42fc5e84cfc21153944b716_6.png)

## 第一步：导入工具包与读取数据

![](img/58308fbbb42fc5e84cfc21153944b716_8.png)

首先，我们需要导入必要的Python库并加载股票数据。

![](img/58308fbbb42fc5e84cfc21153944b716_10.png)

![](img/58308fbbb42fc5e84cfc21153944b716_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

接下来，读取包含股票历史价格的数据文件。我们将第一列指定为时间索引，并解析时间格式。

![](img/58308fbbb42fc5e84cfc21153944b716_14.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/58308fbbb42fc5e84cfc21153944b716_16.png)

![](img/58308fbbb42fc5e84cfc21153944b716_18.png)

数据加载完成后，我们选择苹果公司（AAPL）的股票价格作为分析示例。

![](img/58308fbbb42fc5e84cfc21153944b716_20.png)

```python
# 选择苹果公司的股票价格列
stock_data = data[['AAPL']]
```

![](img/58308fbbb42fc5e84cfc21153944b716_22.png)

![](img/58308fbbb42fc5e84cfc21153944b716_24.png)

---

## 第二步：数据预处理

![](img/58308fbbb42fc5e84cfc21153944b716_26.png)

在进行分析之前，必须处理数据中的缺失值，以确保后续计算的准确性。

![](img/58308fbbb42fc5e84cfc21153944b716_28.png)

```python
# 删除包含缺失值的行
stock_data = stock_data.dropna()
```

![](img/58308fbbb42fc5e84cfc21153944b716_30.png)

![](img/58308fbbb42fc5e84cfc21153944b716_32.png)

执行此操作后，我们获得了一个干净、只包含苹果股票价格的数据集。

---

![](img/58308fbbb42fc5e84cfc21153944b716_34.png)

## 第三步：计算移动平均线

移动平均线是技术分析中常用的指标。我们将计算短期和长期移动平均线。

首先，定义短期和长期的窗口大小。由于我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察趋势，我们设置较大的窗口值。

![](img/58308fbbb42fc5e84cfc21153944b716_36.png)

```python
short_window = 50   # 短期均线窗口
long_window = 200   # 长期均线窗口
```

![](img/58308fbbb42fc5e84cfc21153944b716_38.png)

接着，计算短期和长期的简单移动平均（SMA），并将结果添加到数据集中。

![](img/58308fbbb42fc5e84cfc21153944b716_40.png)

```python
# 计算短期移动平均
stock_data['SMA_short'] = stock_data['AAPL'].rolling(window=short_window).mean()

![](img/58308fbbb42fc5e84cfc21153944b716_42.png)

# 计算长期移动平均
stock_data['SMA_long'] = stock_data['AAPL'].rolling(window=long_window).mean()
```

![](img/58308fbbb42fc5e84cfc21153944b716_44.png)

![](img/58308fbbb42fc5e84cfc21153944b716_46.png)

计算完成后，数据的前几行由于窗口不足会显示为缺失值（NaN），这是正常现象。我们可以查看数据的尾部来确认计算成功。

![](img/58308fbbb42fc5e84cfc21153944b716_48.png)

---

![](img/58308fbbb42fc5e84cfc21153944b716_50.png)

## 第四步：可视化股价与均线

![](img/58308fbbb42fc5e84cfc21153944b716_52.png)

![](img/58308fbbb42fc5e84cfc21153944b716_54.png)

现在，我们可以将原始股价与计算出的两条移动平均线绘制在同一张图上进行观察。

![](img/58308fbbb42fc5e84cfc21153944b716_56.png)

```python
# 绘制图表
stock_data.plot(figsize=(14, 8))
plt.title('Apple Stock Price with Moving Averages')
plt.ylabel('Price')
plt.show()
```

![](img/58308fbbb42fc5e84cfc21153944b716_58.png)

在生成的图表中：
*   **原始股价线**（通常为蓝色或红色）代表每日收盘价。
*   **短期均线**（例如红色）对价格变化反应更灵敏。
*   **长期均线**（例如绿色）趋势更为平缓，代表长期趋势。

观察图表，我们可以发现短期均线与长期均线会产生交叉点。这些交叉点正是我们需要关注的关键信号。

---

![](img/58308fbbb42fc5e84cfc21153944b716_60.png)

## 第五步：识别买卖信号

![](img/58308fbbb42fc5e84cfc21153944b716_62.png)

根据双均线策略：
*   **黄金交叉**：当短期均线**向上**穿越长期均线时，被视为**买入信号**。
*   **死亡交叉**：当短期均线**向下**穿越长期均线时，被视为**卖出信号**。

![](img/58308fbbb42fc5e84cfc21153944b716_64.png)

![](img/58308fbbb42fc5e84cfc21153944b716_66.png)

为了量化这个关系，我们在数据中创建一个标志列，用于记录短期均线是否高于长期均线。

![](img/58308fbbb42fc5e84cfc21153944b716_68.png)

首先，需要删除在计算均线时产生的缺失值。

![](img/58308fbbb42fc5e84cfc21153944b716_70.png)

```python
stock_data = stock_data.dropna()
```

然后，创建标志位。`1` 表示短期均线高于长期均线（可能处于上升趋势或持有阶段），`0` 则表示相反。

![](img/58308fbbb42fc5e84cfc21153944b716_72.png)

```python
stock_data['Position'] = np.where(stock_data['SMA_short'] > stock_data['SMA_long'], 1, 0)
```

![](img/58308fbbb42fc5e84cfc21153944b716_74.png)

我们也可以将标志位绘制在图表上（使用右侧的次坐标轴），以便更直观地看到持仓状态的变化。

```python
fig, ax1 = plt.subplots(figsize=(14, 8))

![](img/58308fbbb42fc5e84cfc21153944b716_76.png)

![](img/58308fbbb42fc5e84cfc21153944b716_78.png)

ax1.plot(stock_data['AAPL'], label='Price', alpha=0.5)
ax1.plot(stock_data['SMA_short'], label=f'{short_window}-Day SMA')
ax1.plot(stock_data['SMA_long'], label=f'{long_window}-Day SMA')
ax1.set_ylabel('Price')

ax2 = ax1.twinx()
ax2.plot(stock_data['Position'], label='Position (Hold=1)', color='gray', linestyle='--', alpha=0.7)
ax2.set_ylabel('Position')
ax2.set_ylim(-0.5, 1.5)

ax1.legend(loc='upper left')
ax2.legend(loc='upper right')
plt.title('Apple Stock Price with Trading Signals')
plt.show()
```

![](img/58308fbbb42fc5e84cfc21153944b716_80.png)

---

![](img/58308fbbb42fc5e84cfc21153944b716_82.png)

## 第六步：理解策略逻辑

![](img/58308fbbb42fc5e84cfc21153944b716_84.png)

现在，我们来理解这个策略如何理论上实现盈利。

*   **被动持有策略**：假设在最早的时间点买入1美元该股票，并一直持有到最后。最终的收益完全取决于股价的净变动。
*   **双均线策略**：根据我们识别的信号进行操作。
    1.  当出现 **“黄金交叉”**（`Position` 从0变为1）时，**买入**或**持有**股票。
    2.  当出现 **“死亡交叉”**（`Position` 从1变为0）时，**卖出**股票或**空仓**等待。

这个策略的核心思想是 **“截断亏损，让利润奔跑”**。通过死亡交叉卖出，旨在避免大的下跌；通过黄金交叉买入，旨在抓住主要的上涨趋势。即使股价在一个长期区间内来回震荡，该策略也试图通过多次买卖在波动中获利，而不是被动承受下跌。

例如，在一次价格波动中：
*   股价从100元跌至80元，被动持有者亏损20元。
*   而双均线交易者在死亡交叉信号出现时（可能在95元）卖出，在后续的黄金交叉信号出现时（可能在85元）再买回。这样，他反而实现了**低买高卖**，获得了盈利。

---

## 总结

![](img/58308fbbb42fc5e84cfc21153944b716_86.png)

本节课中我们一起学习了量化分析中的一个基础但重要的环节：
1.  **数据准备**：导入数据、选择标的、处理缺失值。
2.  **指标计算**：计算了短期和长期简单移动平均线（SMA）。
3.  **可视化分析**：将股价与均线绘制在图表中，直观观察趋势。
4.  **信号生成**：基于双均线交叉原理，定义了“黄金交叉”（买点）和“死亡交叉”（卖点），并在数据中创建了交易信号标志位。
5.  **策略逻辑**：解释了如何利用这些买卖信号构建一个趋势跟踪交易策略，其目标是在市场波动中通过主动交易来获取超越被动持有的收益。

![](img/58308fbbb42fc5e84cfc21153944b716_88.png)

通过本课的学习，你已经掌握了使用Python进行基础技术指标分析和可视化，并理解了双均线策略的基本原理。这是构建更复杂量化交易模型的第一步。