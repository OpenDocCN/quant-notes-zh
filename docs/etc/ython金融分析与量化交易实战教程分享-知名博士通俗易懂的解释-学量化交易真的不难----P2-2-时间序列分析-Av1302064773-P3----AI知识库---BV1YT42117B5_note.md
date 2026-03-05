# 量化交易完全可自学教程：P2：时间序列分析

## 概述
在本节课中，我们将学习时间序列分析的基础操作。我们将以微软（MSFT）的股票数据为例，演示如何获取数据、查看数据结构、进行数据可视化，并计算简单的投资回报。通过本节内容，你将掌握处理时间序列数据的初步技能。

## 数据读取与初步观察
首先，我们需要读取股票数据。这里我们选择微软（MSFT）的股票，获取其截至今日的历史数据。

以下是读取数据的代码：
```python
# 假设使用 yfinance 库获取数据
import yfinance as yf
msft = yf.Ticker("MSFT")
data = msft.history(period="max")
```

执行上述代码后，我们查看数据的结构。数据包含多个指标，如日期（Date）、开盘价（Open）、收盘价（Close）等。在本教程中，我们主要关注收盘价（Close），这是我们后续要预测的目标变量。

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_1.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_3.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_5.png)

首次执行代码时可能会稍慢，因为需要加载相关库。如果代码报错，通常是因为未安装所需的库（如 `yfinance`, `pandas`, `matplotlib`），需要先安装它们。

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_7.png)

## 数据探索与处理
数据读取完成后，我们进行初步探索。首先，重置索引以便于处理日期，并提取关键指标。

以下是数据处理步骤：
```python
# 重置索引，将日期列变为普通列
data_reset = data.reset_index()

# 提取日期和收盘价
D = data_reset['Date']
Y = data_reset['Close']

# 计算每日价格变化（收盘价-开盘价）
data_reset['daily_change'] = data_reset['Close'] - data_reset['Open']
```

接下来，我们确定数据的时间范围。由于日期已是时间格式，我们可以直接使用 `min()` 和 `max()` 函数。
```python
start_date = D.min()
end_date = D.max()
```

同时，我们可以找出历史最高价、最低价及其发生的日期。
```python
max_price = Y.max()
min_price = Y.min()
max_price_date = D[Y.idxmax()]
min_price_date = D[Y.idxmin()]
```

至此，我们已经完成了数据的加载和基本信息的提取。数据时间范围从1986年3月13日到运行代码的当天。

## 数据可视化
在获得时间序列数据后，通常的第一步是将其可视化，以观察整体趋势和模式。

以下是绘制股票收盘价趋势图的代码示例：
```python
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 6))
plt.plot(D, Y, label='Close Price', color='red', linewidth=1)
plt.xlabel('Date')
plt.ylabel('Price (USD)')
plt.title('Microsoft Stock Price Trend')
plt.legend()
plt.grid(True)
plt.show()
```

该图表显示了微软股票从1986年至今的价格走势。整体呈现上升趋势，中间存在一些波动。图表中的最高点通常是最近一个交易日的数据。

我们也可以绘制特定时间区间或多个指标。例如，查看2001年至2018年的每日价格变化和交易量。
```python
# 筛选特定时间区间
mask = (D >= '2001-01-01') & (D <= '2018-01-01')
D_subset = D[mask]
Y_subset = Y[mask]
volume_subset = data_reset['Volume'][mask]

fig, ax1 = plt.subplots(figsize=(12, 6))

color = 'tab:red'
ax1.set_xlabel('Date')
ax1.set_ylabel('Daily Change', color=color)
ax1.plot(D_subset, Y_subset.diff(), color=color, linewidth=1) # 计算每日变化
ax1.tick_params(axis='y', labelcolor=color)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_9.png)

ax2 = ax1.twinx()
color = 'tab:blue'
ax2.set_ylabel('Volume', color=color)
ax2.plot(D_subset, volume_subset, color=color, linewidth=1, alpha=0.5)
ax2.tick_params(axis='y', labelcolor=color)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_11.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_13.png)

plt.title('Daily Change and Trading Volume (2001-2018)')
fig.tight_layout()
plt.show()
```

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_15.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_17.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_19.png)

这张图可以同时观察价格波动（红色线）和交易量（蓝色区域），例如在2016年中旬某日出现了交易量突增的情况。

## 简单投资回报模拟
为了增加趣味性，我们可以模拟一个简单的“买入并持有”策略，计算在特定时间段内购买股票可能获得的收益。

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_21.png)

以下是一个计算收益的函数示例：
```python
def calculate_profit(data, start_date, end_date, shares=100):
    # 筛选时间段
    mask = (data['Date'] >= start_date) & (data['Date'] <= end_date)
    period_data = data.loc[mask].copy()

    if period_data.empty:
        return 0

    buy_price = period_data.iloc[0]['Open']
    sell_price = period_data.iloc[-1]['Close']

    profit_per_share = sell_price - buy_price
    total_profit = profit_per_share * shares

    return total_profit

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_23.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_25.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_27.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_29.png)

# 示例：计算从1986年买入持有至2018年的收益
profit = calculate_profit(data_reset, '1986-03-13', '2018-01-24', shares=100)
print(f"模拟收益: ${profit:.2f}")
```

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_31.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_33.png)

该函数计算了在起始日期以开盘价买入，在结束日期以收盘价卖出所获得的利润。例如，在1986年买入100股并持有至2018年，可能会获得可观的收益。然而，如果买入时机不佳（如在1999年高点买入），则可能面临亏损。这个简单的模拟揭示了投资时机的重要性。

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_35.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_37.png)

![](img/08cc47ba1713de92c8d0fe6ff3fa4a81_39.png)

## 总结
本节课我们一起学习了时间序列分析的入门操作。我们从获取微软股票数据开始，逐步完成了数据读取、结构探索、关键信息提取以及数据可视化。最后，我们还进行了一个简单的投资回报模拟。这些步骤是进行更复杂时间序列分析和量化交易策略研究的基础。在接下来的课程中，我们将深入探讨时间序列的预测模型。