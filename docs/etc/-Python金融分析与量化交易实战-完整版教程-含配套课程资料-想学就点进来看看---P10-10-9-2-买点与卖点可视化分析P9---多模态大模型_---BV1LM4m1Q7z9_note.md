# Python金融分析与量化交易实战：P10：10.9.2：买点与卖点可视化分析 📈

![](img/a7d25af4177624a6666abf48550a507e_0.png)

![](img/a7d25af4177624a6666abf48550a507e_2.png)

![](img/a7d25af4177624a6666abf48550a507e_4.png)

在本节课中，我们将学习如何利用双移动平均线策略，对股票价格数据进行可视化分析，并识别潜在的买入和卖出信号。我们将从数据预处理开始，逐步计算短期和长期移动平均线，最终通过图表清晰地展示交易信号。

![](img/a7d25af4177624a6666abf48550a507e_6.png)

![](img/a7d25af4177624a6666abf48550a507e_8.png)

## 数据准备与预处理

![](img/a7d25af4177624a6666abf48550a507e_10.png)

![](img/a7d25af4177624a6666abf48550a507e_12.png)

首先，我们需要导入必要的工具包并加载数据。以下是实现这一步骤的代码：

![](img/a7d25af4177624a6666abf48550a507e_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/a7d25af4177624a6666abf48550a507e_16.png)

![](img/a7d25af4177624a6666abf48550a507e_18.png)

![](img/a7d25af4177624a6666abf48550a507e_20.png)

# 读取数据，指定第一列为时间索引
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/a7d25af4177624a6666abf48550a507e_22.png)

数据加载完成后，我们选择苹果公司的股价作为分析示例，并处理数据中可能存在的缺失值，以确保后续计算的准确性。

![](img/a7d25af4177624a6666abf48550a507e_24.png)

![](img/a7d25af4177624a6666abf48550a507e_26.png)

```python
# 选择苹果公司的股价列
apple_data = data[['AAPL']].copy()
# 删除缺失值
apple_data.dropna(inplace=True)
```

![](img/a7d25af4177624a6666abf48550a507e_28.png)

## 计算移动平均线

![](img/a7d25af4177624a6666abf48550a507e_30.png)

![](img/a7d25af4177624a6666abf48550a507e_32.png)

上一节我们完成了数据的基础准备，本节中我们来看看如何计算关键的短期和长期移动平均线。移动平均线是技术分析中常用的趋势指标。

![](img/a7d25af4177624a6666abf48550a507e_34.png)

首先，我们需要定义短期和长期的窗口大小。考虑到我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察趋势，我们将窗口设置得稍大一些。

```python
# 定义短期和长期窗口大小
short_window = 40
long_window = 100
```

![](img/a7d25af4177624a6666abf48550a507e_36.png)

接下来，我们使用`rolling`方法计算移动平均线，并将结果添加到数据框中。

![](img/a7d25af4177624a6666abf48550a507e_38.png)

```python
# 计算短期移动平均线
apple_data['short_ma'] = apple_data['AAPL'].rolling(window=short_window).mean()
# 计算长期移动平均线
apple_data['long_ma'] = apple_data['AAPL'].rolling(window=long_window).mean()
```

![](img/a7d25af4177624a6666abf48550a507e_40.png)

![](img/a7d25af4177624a6666abf48550a507e_42.png)

计算完成后，我们可以查看数据的尾部，确认移动平均线已成功计算。由于移动平均计算需要足够的窗口数据，前几行会出现`NaN`值，这是正常现象。

![](img/a7d25af4177624a6666abf48550a507e_44.png)

![](img/a7d25af4177624a6666abf48550a507e_46.png)

![](img/a7d25af4177624a6666abf48550a507e_48.png)

## 可视化移动平均线与交叉点

![](img/a7d25af4177624a6666abf48550a507e_50.png)

![](img/a7d25af4177624a6666abf48550a507e_52.png)

计算完移动平均线后，我们可以通过绘图来直观地观察股价与两条均线的走势关系，并识别它们的交叉点。

![](img/a7d25af4177624a6666abf48550a507e_54.png)

以下是绘制图表的代码：

![](img/a7d25af4177624a6666abf48550a507e_56.png)

![](img/a7d25af4177624a6666abf48550a507e_58.png)

![](img/a7d25af4177624a6666abf48550a507e_60.png)

```python
# 绘制股价与移动平均线
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='Apple Price', alpha=0.5)
plt.plot(apple_data['short_ma'], label=f'Short MA ({short_window} days)', color='red')
plt.plot(apple_data['long_ma'], label=f'Long MA ({long_window} days)', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

在生成的图表中，红色线代表短期移动平均线，绿色线代表长期移动平均线。我们需要关注两条线的交叉点：
*   **黄金交叉**：当短期均线从下向上穿越长期均线时，通常被视为**买入信号**。
*   **死亡交叉**：当短期均线从上向下穿越长期均线时，通常被视为**卖出信号**。

![](img/a7d25af4177624a6666abf48550a507e_62.png)

## 标记信号与策略回测基础

![](img/a7d25af4177624a6666abf48550a507e_64.png)

![](img/a7d25af4177624a6666abf48550a507e_66.png)

在图表中识别交叉点后，我们需要在数据中系统地标记这些信号，为后续的策略回测做准备。

![](img/a7d25af4177624a6666abf48550a507e_68.png)

![](img/a7d25af4177624a6666abf48550a507e_70.png)

首先，为了确保数据的一致性，我们需要删除在计算移动平均线时产生的缺失值。

![](img/a7d25af4177624a6666abf48550a507e_72.png)

```python
# 删除因计算移动平均线而产生的缺失值
apple_data.dropna(inplace=True)
```

![](img/a7d25af4177624a6666abf48550a507e_74.png)

接下来，我们创建一个新的列`position`来标记信号。当短期均线大于长期均线时，标记为1（代表持有或买入信号）；否则标记为0（代表不持有或卖出信号）。

![](img/a7d25af4177624a6666abf48550a507e_76.png)

```python
# 标记信号：短期均线 > 长期均线 时为1，否则为0
apple_data['position'] = np.where(apple_data['short_ma'] > apple_data['long_ma'], 1, 0)
```

![](img/a7d25af4177624a6666abf48550a507e_78.png)

![](img/a7d25af4177624a6666abf48550a507e_80.png)

我们也可以将信号绘制在图表上，以便更直观地观察策略的触发点。

```python
# 可视化信号
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

![](img/a7d25af4177624a6666abf48550a507e_82.png)

ax1.plot(apple_data['AAPL'], label='Price')
ax1.plot(apple_data['short_ma'], label='Short MA')
ax1.plot(apple_data['long_ma'], label='Long MA')
ax1.set_ylabel('Price')
ax1.legend()
ax1.set_title('Price and Moving Averages')

![](img/a7d25af4177624a6666abf48550a507e_84.png)

![](img/a7d25af4177624a6666abf48550a507e_86.png)

ax2.plot(apple_data['position'], label='Trading Signal', color='orange', drawstyle='steps-post')
ax2.set_ylabel('Signal')
ax2.set_xlabel('Date')
ax2.legend()
ax2.set_title('Generated Trading Signals (1=Buy/Hold, 0=Sell)')
plt.show()
```

## 策略逻辑与初步分析

标记了买卖信号后，我们来理解双均线策略的核心逻辑。其基本思想是顺势而为：
*   当短期趋势强于长期趋势（`position=1`）时，认为上涨概率大，选择**买入或持有**。
*   当短期趋势弱于长期趋势（`position=0`）时，认为下跌概率大，选择**卖出或空仓**。

这种策略旨在通过捕捉趋势的转变，在股价上涨时持有，在下跌开始时离场，从而规避部分下跌风险，提升整体收益。与单纯买入并持有的策略相比，它试图通过主动交易来获得更好的风险调整后收益。

![](img/a7d25af4177624a6666abf48550a507e_88.png)

![](img/a7d25af4177624a6666abf48550a507e_90.png)

---

![](img/a7d25af4177624a6666abf48550a507e_92.png)

本节课中我们一起学习了双移动平均线策略的完整实现流程。我们从数据导入和预处理开始，逐步计算了短期与长期移动平均线，并通过可视化图表清晰地识别了“黄金交叉”和“死亡交叉”这两种关键的买卖信号。最后，我们在数据中系统性地标记了这些信号，并初步探讨了该策略“顺势而为”的交易逻辑，为后续进行详细的策略回测和收益分析打下了坚实的基础。