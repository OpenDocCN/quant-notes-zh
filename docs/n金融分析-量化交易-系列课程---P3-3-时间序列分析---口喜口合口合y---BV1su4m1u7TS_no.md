# Python金融分析+量化交易：P3：时间序列分析 📈

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软股票数据为例，从数据读取、可视化到简单分析，逐步讲解核心步骤。课程内容简单直白，适合初学者入门。

## 数据读取与初步观察

首先，我们需要读取股票数据。这里我们选择微软（MSFT）的股票历史数据。

```python
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt

# 读取微软股票数据
msft = yf.Ticker("MSFT")
hist = msft.history(period="max")
```

执行上述代码后，我们获得了股票的历史数据。如果代码报错，通常是因为 `yfinance`、`pandas` 或 `matplotlib` 库未安装，需要先安装这些库。

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_1.png)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_3.png)

数据读取完成后，我们查看一下数据的结构。

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_5.png)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_7.png)

```python
print(hist.head())
```

数据包含多个指标，如日期（Date）、开盘价（Open）、收盘价（Close）等。在本教程中，我们主要关注收盘价（Close），这是我们后续要预测的目标变量。

## 数据探索与预处理

上一节我们读取了数据，本节中我们来看看如何对数据进行探索和预处理。

首先，我们重置索引，并将日期列转换为标准的日期时间格式，便于后续处理。

```python
# 重置索引，将日期列变为普通列
df = hist.reset_index()
# 确保日期列为datetime格式
df['Date'] = pd.to_datetime(df['Date'])
```

接下来，我们提取出核心的预测变量（日期 `D`）和目标变量（收盘价 `Y`）。

```python
D = df['Date']
Y = df['Close']
```

此外，我们还可以计算一些衍生指标，例如每日价格变化（`daily_change`）。

```python
df['daily_change'] = df['Close'] - df['Open']
```

然后，我们确定数据的时间范围、起始价格和最新价格。

```python
start_date = df['Date'].min()
end_date = df['Date'].max()
start_price = df.loc[df['Date'] == start_date, 'Close'].values[0]
latest_price = df.loc[df['Date'] == end_date, 'Close'].values[0]
```

我们也可以找出历史最高价和最低价及其发生的日期。

```python
max_price = df['Close'].max()
min_price = df['Close'].min()
max_price_date = df.loc[df['Close'] == max_price, 'Date'].values[0]
min_price_date = df.loc[df['Close'] == min_price, 'Date'].values[0]
```

## 时间序列可视化

数据准备就绪后，最直观的方法是将其可视化。以下是绘制股票收盘价随时间变化的趋势图。

```python
plt.figure(figsize=(12, 6))
plt.plot(df['Date'], df['Close'], color='red', linewidth=1, label='Close Price')
plt.xlabel('Date')
plt.ylabel('Price (USD)')
plt.title('Microsoft Stock Price Over Time')
plt.grid(True)
plt.legend()
plt.show()
```

从图中可以看出，微软股票从1986年至今，整体呈上升趋势，中间虽有波动，但长期看好。

我们也可以绘制指定时间区间或多个指标的图。例如，查看2001年至2018年的每日价格变化和交易量。

```python
# 筛选指定日期范围
mask = (df['Date'] >= '2001-01-01') & (df['Date'] <= '2018-01-01')
df_subset = df.loc[mask]

fig, ax1 = plt.subplots(figsize=(12, 6))

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_9.png)

color = 'tab:red'
ax1.set_xlabel('Date')
ax1.set_ylabel('Daily Change', color=color)
ax1.plot(df_subset['Date'], df_subset['daily_change'], color=color, linewidth=1)
ax1.tick_params(axis='y', labelcolor=color)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_11.png)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_13.png)

ax2 = ax1.twinx()  # 共享X轴
color = 'tab:blue'
ax2.set_ylabel('Volume', color=color)
ax2.plot(df_subset['Date'], df_subset['Volume'], color=color, linewidth=1, alpha=0.5)
ax2.tick_params(axis='y', labelcolor=color)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_15.png)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_17.png)

fig.tight_layout()
plt.title('Daily Change and Volume (2001-2018)')
plt.show()
```

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_19.png)

## 模拟投资回报分析

为了增加趣味性，我们可以模拟一个简单的投资场景：计算在特定时间买入并持有股票至另一时间所能获得的收益。

定义一个函数 `calculate_return` 来计算收益。

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_21.png)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_23.png)

```python
def calculate_return(start_date, end_date, shares=100):
    """
    计算在start_date买入，end_date卖出指定数量股票的收益。
    """
    start_price = df.loc[df['Date'] == start_date, 'Close'].values[0]
    end_price = df.loc[df['Date'] == end_date, 'Close'].values[0]

    profit_per_share = end_price - start_price
    total_profit = profit_per_share * shares

    return total_profit

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_25.png)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_27.png)

# 示例：计算从1986-03-13买入100股，持有到2018-01-24的收益
profit = calculate_return(pd.Timestamp('1986-03-13'), pd.Timestamp('2018-01-24'), 100)
print(f"总收益: ${profit:.2f}")
```

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_29.png)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_31.png)

通过改变买入和卖出日期，我们可以观察不同投资策略的结果。例如，在1999年高点买入，在2002年低点卖出，则可能导致亏损。

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_33.png)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_35.png)

## 总结

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_37.png)

![](img/746ca04d82ea7bb05e0bc9889cbcf6f3_39.png)

本节课中我们一起学习了时间序列分析的基础操作。我们从读取微软股票数据开始，进行了数据探索和预处理，提取了关键变量。然后，我们通过绘制趋势图直观展示了股价的长期变化，并分析了特定时间段内价格与交易量的关系。最后，我们通过一个简单的模拟投资函数，理解了不同买卖时机对投资回报的影响。这些步骤构成了时间序列分析的入门基础，为后续更复杂的模型预测做好了准备。