# 时间序列分析：3.3.2：股票数据获取与初步分析 📈

在本节课中，我们将学习如何获取股票数据，并进行初步的可视化分析。我们将以微软（MSFT）的股票为例，演示从数据读取到绘制趋势图的完整流程。

## 数据读取与查看

首先，我们需要读取股票数据。这里我们使用 `yfinance` 库来获取微软（MSFT）的历史股价数据。

```python
import yfinance as yf
import pandas as pd

# 读取微软股票数据
stock_symbol = 'MSFT'
data = yf.download(stock_symbol)
```

执行上述代码后，我们会得到一个包含多个指标的 `DataFrame`。数据中包含了日期（`Date`）、开盘价（`Open`）、收盘价（`Close`）等。我们主要关注的是收盘价（`Close`），这是我们后续要预测的目标变量。

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_1.png)

为了确保代码正常运行，请检查是否已正确安装 `yfinance`、`pandas` 和 `matplotlib` 库。如果代码报错，通常是因为缺少这些库，需要先安装它们。

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_3.png)

## 数据预处理

读取数据后，我们需要进行一些预处理，以便后续分析。

```python
# 重置索引，将日期列变为普通列
data = data.reset_index()

# 提取关键列：日期（DS）和目标变量（Y）
data['DS'] = pd.to_datetime(data['Date'])
data['Y'] = data['Close']

# 计算每日价格变化
data['daily_change'] = data['Close'] - data['Open']
```

通过以上步骤，我们得到了一个清晰的数据集，其中 `DS` 是日期，`Y` 是收盘价。我们还计算了每日的价格变化（收盘价减去开盘价）。

## 数据统计信息

接下来，我们可以计算一些基本的统计信息，例如数据的时间范围、最高价、最低价等。

```python
# 计算起始和结束日期
start_date = data['DS'].min()
end_date = data['DS'].max()

# 计算最高价和最低价
max_price = data['Y'].max()
min_price = data['Y'].min()

# 找到最高价和最低价对应的日期
max_price_date = data.loc[data['Y'] == max_price, 'DS'].values[0]
min_price_date = data.loc[data['Y'] == min_price, 'DS'].values[0]
```

这些统计信息有助于我们了解数据的整体情况，例如股票的历史表现和波动范围。

## 数据可视化

在时间序列分析中，可视化是非常重要的一步。通过绘制股票价格随时间变化的趋势图，我们可以直观地观察其走势。

以下是绘制股票价格趋势图的代码：

```python
import matplotlib.pyplot as plt

# 绘制收盘价趋势图
plt.figure(figsize=(12, 6))
plt.plot(data['DS'], data['Y'], color='red', linewidth=2, label='Close Price')
plt.xlabel('Date')
plt.ylabel('Price')
plt.title('Microsoft Stock Price Trend (1986-2018)')
plt.grid(True)
plt.legend()
plt.show()
```

通过这张图，我们可以看到微软股票从1986年到2018年的整体趋势。虽然中间有波动，但整体呈上升趋势。

## 多指标可视化

除了收盘价，我们还可以同时可视化多个指标，例如交易量（`Volume`）和每日价格变化（`daily_change`）。

```python
# 绘制交易量和每日价格变化
fig, ax1 = plt.subplots(figsize=(12, 6))

# 绘制每日价格变化
ax1.plot(data['DS'], data['daily_change'], color='red', linewidth=2, label='Daily Change')
ax1.set_xlabel('Date')
ax1.set_ylabel('Daily Change', color='red')
ax1.tick_params(axis='y', labelcolor='red')

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_5.png)

# 创建第二个Y轴，绘制交易量
ax2 = ax1.twinx()
ax2.plot(data['DS'], data['Volume'], color='blue', linewidth=2, label='Volume')
ax2.set_ylabel('Volume', color='blue')
ax2.tick_params(axis='y', labelcolor='blue')

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_7.png)

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_9.png)

plt.title('Microsoft Stock: Daily Change and Volume')
plt.show()
```

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_11.png)

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_13.png)

通过这张图，我们可以同时观察股票的价格变化和交易量。例如，在某些日期，交易量会突然增加，这可能与市场事件相关。

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_15.png)

## 模拟投资收益

为了增加趣味性，我们可以模拟一个简单的投资场景：假设在某个起始日期买入股票，在终止日期卖出，计算收益情况。

```python
def calculate_profit(data, start_date, end_date, shares=100):
    # 筛选指定日期范围内的数据
    mask = (data['DS'] >= start_date) & (data['DS'] <= end_date)
    filtered_data = data.loc[mask].copy()
    
    # 计算买入价和卖出价
    buy_price = filtered_data.iloc[0]['Close']
    sell_price = filtered_data.iloc[-1]['Close']
    
    # 计算总收益
    profit = (sell_price - buy_price) * shares
    return profit

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_17.png)

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_19.png)

# 示例：计算从1986年到2018年的收益
profit = calculate_profit(data, start_date, end_date, shares=100)
print(f"总收益: ${profit:.2f}")
```

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_21.png)

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_23.png)

这个函数可以帮助我们快速计算在不同时间段内投资的收益情况。例如，如果在1986年买入微软股票并持有到2018年，收益会非常可观。

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_25.png)

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_27.png)

## 总结

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_29.png)

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_31.png)

本节课中，我们一起学习了如何获取股票数据、进行预处理、计算统计信息以及绘制可视化图表。通过以上步骤，我们可以对时间序列数据有一个初步的了解，并为后续的预测分析打下基础。

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_33.png)

![](img/2eb30f8c9cdc0b3203a61ba75f69c1ef_35.png)

在下一节中，我们将进一步探讨如何利用这些数据进行时间序列预测。