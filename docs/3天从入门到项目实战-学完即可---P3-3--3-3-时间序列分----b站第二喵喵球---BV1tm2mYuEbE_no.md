# 量化交易：3.3：时间序列分析 📈

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软股票数据为例，从数据读取、可视化到简单分析，一步步构建一个基础的时间序列分析流程。

---

## 数据读取与初步探索

首先，我们需要读取股票数据。这里选择微软的股票进行分析。

```python
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt

# 读取微软股票数据
ticker = 'MSFT'
stock_data = yf.download(ticker)
```

![](img/4ae369b34ad5b8703ec662051ea9352b_1.png)

![](img/4ae369b34ad5b8703ec662051ea9352b_3.png)

![](img/4ae369b34ad5b8703ec662051ea9352b_5.png)

执行上述代码时，系统会从网络下载数据，首次运行可能稍慢。如果代码报错，通常是因为缺少 `yfinance`、`pandas` 或 `matplotlib` 库，需要先安装它们。

![](img/4ae369b34ad5b8703ec662051ea9352b_7.png)

数据读取后，我们查看其结构。数据包含多个指标，如日期、开盘价、收盘价等。我们主要关注收盘价，将其作为预测目标。

```python
# 重置索引，将日期列变为普通列
stock_data = stock_data.reset_index()

# 定义时间序列的日期（X）和目标值（Y）
ds = stock_data['Date']
y = stock_data['Close']

# 计算每日价格变动
stock_data['Daily_Change'] = stock_data['Close'] - stock_data['Open']
```

通过 `min()` 和 `max()` 函数，我们可以获取数据的时间范围、最高价、最低价及其对应的日期。

```python
start_date = ds.min()
end_date = ds.max()
max_price = y.max()
min_price = y.min()
```

---

## 数据可视化

在时间序列分析中，可视化是理解数据趋势的关键步骤。我们将绘制股票收盘价随时间变化的曲线。

以下是绘制基本趋势图的函数核心逻辑：

```python
def plot_basic(data, column='Close', start=None, end=None):
    # 设定时间范围
    if start is None:
        start = data['Date'].min()
    if end is None:
        end = data['Date'].max()

    # 筛选指定时间范围内的数据
    mask = (data['Date'] >= start) & (data['Date'] <= end)
    filtered_data = data.loc[mask]

    # 绘制图表
    plt.figure(figsize=(12, 6))
    plt.plot(filtered_data['Date'], filtered_data[column], color='red', linewidth=1, label=column)
    plt.xlabel('Date')
    plt.ylabel('Price')
    plt.title(f'{column} Price Over Time')
    plt.grid(True)
    plt.legend()
    plt.show()
```

调用该函数，我们可以观察微软股票从1986年到2018年的整体上涨趋势，期间虽有波动，但长期呈上升状态。

```python
plot_basic(stock_data)
```

我们也可以同时绘制多个指标，例如每日价格变动和交易量。

```python
# 绘制两个指标
plot_basic(stock_data, column=['Daily_Change', 'Volume'])
```

图表中，蓝色线代表交易量，红色线代表每日价格变动。通过对比，可以发现某些日期交易量突增，可能与特定市场事件相关。

---

## 模拟投资回报分析

为了增加趣味性，我们可以模拟一个简单的投资场景：计算在特定时间买入并持有股票至另一时间所能获得的收益。

![](img/4ae369b34ad5b8703ec662051ea9352b_9.png)

函数 `calculate_return` 的核心计算公式如下：

![](img/4ae369b34ad5b8703ec662051ea9352b_11.png)

![](img/4ae369b34ad5b8703ec662051ea9352b_13.png)

**收益 = (卖出价格 - 买入价格) * 持股数量**

![](img/4ae369b34ad5b8703ec662051ea9352b_15.png)

![](img/4ae369b34ad5b8703ec662051ea9352b_17.png)

```python
def calculate_return(data, start_date, end_date, shares=100):
    # 获取买入和卖出时的收盘价
    buy_price = data.loc[data['Date'] == start_date, 'Close'].values[0]
    sell_price = data.loc[data['Date'] == end_date, 'Close'].values[0]

    # 计算总收益
    total_return = (sell_price - buy_price) * shares
    return total_return
```

![](img/4ae369b34ad5b8703ec662051ea9352b_19.png)

例如，计算从1986年3月13日买入100股，持有至2018年1月24日卖出的收益：

```python
profit = calculate_return(stock_data, '1986-03-13', '2018-01-24', shares=100)
print(f"总收益: ${profit:.2f}")
```

![](img/4ae369b34ad5b8703ec662051ea9352b_21.png)

这个简单的分析表明，长期持有表现良好的股票可能带来可观回报。但同样，如果买入时机不佳（如在1999年高点买入），则可能面临亏损。

![](img/4ae369b34ad5b8703ec662051ea9352b_23.png)

![](img/4ae369b34ad5b8703ec662051ea9352b_25.png)

![](img/4ae369b34ad5b8703ec662051ea9352b_27.png)

---

![](img/4ae369b34ad5b8703ec662051ea9352b_29.png)

## 总结

![](img/4ae369b34ad5b8703ec662051ea9352b_31.png)

![](img/4ae369b34ad5b8703ec662051ea9352b_33.png)

![](img/4ae369b34ad5b8703ec662051ea9352b_35.png)

本节课中我们一起学习了时间序列分析的基础操作：
1.  **数据读取与探索**：使用 `yfinance` 获取股票数据，并提取关键指标。
2.  **数据可视化**：绘制时间序列图，直观观察数据趋势和特征。
3.  **简单分析应用**：通过模拟投资回报计算，将时间序列分析应用于实际场景。

![](img/4ae369b34ad5b8703ec662051ea9352b_37.png)

![](img/4ae369b34ad5b8703ec662051ea9352b_39.png)

这些步骤构成了时间序列分析的基石。在后续课程中，我们将在此基础上，深入探讨更复杂的预测模型和方法。