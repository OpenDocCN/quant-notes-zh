# 时间序列分析：P3-2：数据读取与初步探索 📈

在本节课中，我们将学习如何读取股票数据，并进行初步的可视化分析，以了解时间序列数据的基本形态。

## 数据读取与查看

首先，我们需要读取数据。这里我们选择微软（MSFT）的股票数据，获取其历史记录。

```python
# 示例代码：读取股票数据
import yfinance as yf
stock = yf.Ticker("MSFT")
data = stock.history(period="max")
```

![](img/94f3e2493d9cbf1ea263f77e94788e84_1.png)

![](img/94f3e2493d9cbf1ea263f77e94788e84_3.png)

执行上述代码后，我们可以查看数据的结构。数据包含多个指标，例如日期（`Date`）、开盘价（`Open`）、收盘价（`Close`）等。我们主要关注的是收盘价（`Close`），这是我们后续要预测的目标变量。

初次执行代码时可能会稍慢，因为需要加载相关库。如果代码报错，通常是因为未安装所需的库（如 `yfinance`, `pandas`, `matplotlib`），需要先安装它们。

## 数据预处理

读取数据后，我们需要进行一些预处理。首先，重置索引以确保日期列是独立的。

```python
data = data.reset_index()
```

接下来，我们提取关键列：日期（`ds`）和目标变量（`y`，即收盘价）。同时，可以计算一些衍生指标，例如每日价格变化（`change`）。

```python
data['ds'] = data['Date']
data['y'] = data['Close']
data['change'] = data['Close'] - data['Open']
```

我们还可以确定数据的时间范围，即最早和最晚的日期。

```python
start_date = data['ds'].min()
end_date = data['ds'].max()
```

根据这些日期，可以计算出起始价格、最近价格、最高/最低价格及其发生的日期。

## 数据可视化

在获得数据后，通常第一步是将其可视化，以观察整体趋势和模式。

以下是绘制股票价格趋势图的基本步骤：

1.  指定要绘制的指标（例如收盘价 `Close`）。
2.  可选地指定绘图的时间范围（`start_date`, `end_date`）。
3.  使用绘图库（如 `matplotlib`）绘制折线图。

```python
import matplotlib.pyplot as plt
plt.figure(figsize=(12, 6))
plt.plot(data['ds'], data['Close'], label='Close Price', color='red')
plt.xlabel('Date')
plt.ylabel('Price (USD)')
plt.title('Microsoft Stock Price Trend')
plt.legend()
plt.grid(True)
plt.show()
```

通过图表，我们可以看到微软股票从1986年到2018年初的整体上升趋势，期间虽有波动，但长期看是增长的。

## 多指标与自定义分析

![](img/94f3e2493d9cbf1ea263f77e94788e84_5.png)

![](img/94f3e2493d9cbf1ea263f77e94788e84_7.png)

除了绘制单一指标，我们还可以同时绘制多个指标进行比较，例如将收盘价和交易量（`Volume`）画在一起。

![](img/94f3e2493d9cbf1ea263f77e94788e84_9.png)

![](img/94f3e2493d9cbf1ea263f77e94788e84_11.png)

![](img/94f3e2493d9cbf1ea263f77e94788e84_13.png)

```python
fig, ax1 = plt.subplots(figsize=(12, 6))
color = 'tab:red'
ax1.set_xlabel('Date')
ax1.set_ylabel('Close Price', color=color)
ax1.plot(data['ds'], data['Close'], color=color, label='Close Price')
ax1.tick_params(axis='y', labelcolor=color)
ax2 = ax1.twinx()
color = 'tab:blue'
ax2.set_ylabel('Volume', color=color)
ax2.plot(data['ds'], data['Volume'], color=color, label='Volume', alpha=0.5)
ax2.tick_params(axis='y', labelcolor=color)
fig.tight_layout()
plt.title('Close Price and Trading Volume')
plt.show()
```

![](img/94f3e2493d9cbf1ea263f77e94788e84_15.png)

此外，我们可以编写一个简单的函数来模拟投资回报。例如，计算在特定日期买入一定数量股票，并在另一日期卖出后的盈利情况。

```python
def calculate_profit(data, start_date, end_date, shares=100):
    buy_price = data.loc[data['ds'] == start_date, 'Close'].values[0]
    sell_price = data.loc[data['ds'] == end_date, 'Close'].values[0]
    profit = (sell_price - buy_price) * shares
    return profit
```

![](img/94f3e2493d9cbf1ea263f77e94788e84_17.png)

![](img/94f3e2493d9cbf1ea263f77e94788e84_19.png)

这个函数可以帮助我们直观地理解在不同时间点投资的可能结果。

![](img/94f3e2493d9cbf1ea263f77e94788e84_21.png)

![](img/94f3e2493d9cbf1ea263f77e94788e84_23.png)

![](img/94f3e2493d9cbf1ea263f77e94788e84_25.png)

## 总结

![](img/94f3e2493d9cbf1ea263f77e94788e84_27.png)

![](img/94f3e2493d9cbf1ea263f77e94788e84_29.png)

本节课中我们一起学习了时间序列分析的初始步骤：
1.  我们学习了如何使用 `yfinance` 库读取股票数据。
2.  我们进行了基本的数据预处理，包括提取日期、目标变量和计算衍生指标。
3.  我们掌握了将时间序列数据可视化的方法，包括绘制单一趋势图和多个指标的对比图。
4.  最后，我们通过一个简单的投资回报计算示例，加深了对数据应用的理解。

![](img/94f3e2493d9cbf1ea263f77e94788e84_31.png)

![](img/94f3e2493d9cbf1ea263f77e94788e84_33.png)

![](img/94f3e2493d9cbf1ea263f77e94788e84_35.png)

这些步骤是分析任何时间序列数据的基础，为我们后续建立预测模型做好了准备。