# 量化交易教程：3.3：时间序列分析 📈

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软股票数据为例，演示数据读取、可视化以及初步分析的过程。我们将使用`yfinance`等库来获取和处理金融时间序列数据。

---

## 数据读取与初步观察

首先，我们需要读取股票数据。这里我们选择微软的股票进行分析。

```python
import yfinance as yf
import pandas as pd

# 读取微软股票数据
stock_symbol = 'MSFT'
data = yf.download(stock_symbol)
```

执行上述代码后，我们获得了微软股票的历史数据。数据包含多个列，例如日期、开盘价、最高价、最低价、收盘价和交易量等。我们主要关注的是收盘价，即`Close`列。

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_1.png)

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_2.png)

```python
# 查看数据的前几行
print(data.head())
```

数据读取完成后，我们可以提取出时间序列和要预测的目标值。

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_4.png)

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_5.png)

```python
# 提取日期和收盘价
ds = data.index  # 时间序列
y = data['Close'].values  # 收盘价序列
```

接下来，我们计算一些基本统计信息，如数据的时间范围、最高价、最低价及其对应的日期。

```python
# 计算起始和结束日期
start_date = ds.min()
end_date = ds.max()

# 计算最高价和最低价及其日期
max_price = y.max()
min_price = y.min()
max_price_date = ds[y.argmax()]
min_price_date = ds[y.argmin()]
```

---

## 数据可视化

在获得数据后，通常第一步是将其可视化，以观察其趋势和模式。

以下是绘制股票收盘价随时间变化的代码：

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 6))
plt.plot(ds, y, color='red', linewidth=2, label='Close Price')
plt.xlabel('Date')
plt.ylabel('Price (USD)')
plt.title(f'{stock_symbol} Stock Price Over Time')
plt.grid(True)
plt.legend()
plt.show()
```

通过图表，我们可以观察到微软股票从1986年到2018年的整体上升趋势，期间虽有波动，但长期来看价格是增长的。

我们还可以同时绘制多个指标，例如每日价格变动和交易量。

```python
# 计算每日价格变动
data['Daily_Change'] = data['Close'] - data['Open']

# 绘制价格变动和交易量
fig, ax1 = plt.subplots(figsize=(12, 6))

color = 'tab:red'
ax1.set_xlabel('Date')
ax1.set_ylabel('Daily Price Change', color=color)
ax1.plot(data.index, data['Daily_Change'], color=color, linewidth=2)
ax1.tick_params(axis='y', labelcolor=color)

ax2 = ax1.twinx()
color = 'tab:blue'
ax2.set_ylabel('Volume', color=color)
ax2.plot(data.index, data['Volume'], color=color, linewidth=2, alpha=0.5)
ax2.tick_params(axis='y', labelcolor=color)

plt.title(f'{stock_symbol} - Daily Change and Trading Volume')
plt.show()
```

此图表帮助我们理解价格变动与交易量之间的关系。

---

## 模拟投资回报分析

为了更直观地理解时间序列数据在金融中的应用，我们可以模拟一个简单的“买入并持有”投资策略，计算在特定时间段内的收益。

以下函数计算了在指定开始日期买入、在结束日期卖出一定数量股票后的收益：

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_7.png)

```python
def calculate_profit(data, start_date, end_date, shares=100):
    # 筛选指定时间段的数据
    mask = (data.index >= start_date) & (data.index <= end_date)
    period_data = data.loc[mask]

    if period_data.empty:
        return 0

    # 获取买入价和卖出价
    buy_price = period_data.iloc[0]['Close']
    sell_price = period_data.iloc[-1]['Close']

    # 计算总收益
    profit = (sell_price - buy_price) * shares
    return profit

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_9.png)

# 示例：计算从1986年到2018年的收益
profit = calculate_profit(data, start_date, end_date, shares=100)
print(f"Profit from {start_date.date()} to {end_date.date()}: ${profit:.2f}")
```

通过改变`start_date`和`end_date`，我们可以分析不同时期投资的盈亏情况，例如在股价高点买入、低点卖出可能导致亏损。

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_11.png)

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_12.png)

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_13.png)

---

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_14.png)

## 时间序列的分解与特征提取

在深入分析之前，理解时间序列的组成部分（趋势、季节性、残差）非常重要。虽然本节未直接使用分解模型，但这是后续建模的基础概念。

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_16.png)

一个时间序列 \( Y_t \) 可以分解为：
\[ Y_t = T_t + S_t + R_t \]
其中：
- \( T_t \) 是趋势成分。
- \( S_t \) 是季节性成分。
- \( R_t \) 是残差或随机成分。

在后续课程中，我们将使用如Facebook Prophet等工具来拟合和预测这些成分。

---

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_18.png)

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_19.png)

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_20.png)

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_21.png)

## 总结

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_22.png)

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_23.png)

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_24.png)

本节课中我们一起学习了时间序列分析的初步步骤：
1.  **数据读取**：使用`yfinance`库获取股票历史数据。
2.  **数据观察与处理**：提取关键指标（如收盘价），计算基本统计信息（最高价、最低价、时间范围）。
3.  **数据可视化**：绘制股票价格走势图，并同时观察价格变动与交易量等多项指标。
4.  **简单应用**：通过模拟“买入并持有”策略，计算特定时间段的投资回报，直观感受时间序列数据在金融分析中的应用。

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_26.png)

![](img/c5ca60ae4c99274ea1fd22a19dfdb0e1_27.png)

这些步骤构成了时间序列分析的基础，为我们后续使用更复杂的模型（如ARIMA、Prophet）进行预测做好了准备。在下一节中，我们将开始介绍如何利用Facebook Prophet框架来构建预测模型。