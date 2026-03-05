# 机器学习与量化交易：3：时间序列分析 📈

在本节课中，我们将学习如何对股票价格数据进行时间序列分析。我们将从读取数据开始，逐步进行数据探索、可视化，并介绍一个用于计算股票持有收益的简单工具。整个过程将使用Python及其相关库来完成。

## 数据读取与初步探索

首先，我们需要读取股票数据。这里我们选择微软（MSFT）的股票历史数据作为示例。

```python
import pandas as pd
import yfinance as yf
import matplotlib.pyplot as plt

# 读取微软股票数据
stock_symbol = 'MSFT'
data = yf.download(stock_symbol)
```

执行上述代码后，我们会获得一个包含日期（`Date`）和多个价格指标（如开盘价、收盘价等）的`DataFrame`。我们主要关注的是收盘价（`Close`），这是我们后续要预测的目标变量。

![](img/3a80f335520d94479901bbea9fc0f1cc_1.png)

为了确保代码正常运行，需要提前安装`pandas`、`yfinance`和`matplotlib`库。如果代码报错，请检查这些库是否已正确安装。

![](img/3a80f335520d94479901bbea9fc0f1cc_3.png)

![](img/3a80f335520d94479901bbea9fc0f1cc_5.png)

数据读取完成后，我们可以查看数据的基本结构和前几行。

![](img/3a80f335520d94479901bbea9fc0f1cc_7.png)

```python
print(data.head())
```

接下来，我们对数据进行一些基本处理。将日期索引重置为普通列，并提取出我们关心的日期（`ds`）和收盘价（`y`）序列。

```python
# 重置索引，使日期成为一列
data = data.reset_index()
# 指定日期列和目标列
ds = data['Date']
y = data['Close']
```

同时，我们还可以计算一些衍生指标，例如每日价格变化（`daily_change`），即收盘价与开盘价的差值。

```python
data['daily_change'] = data['Close'] - data['Open']
```

我们也可以计算出整个时间序列的起始日期、结束日期、最高价、最低价及其对应的日期。

```python
start_date = ds.min()
end_date = ds.max()
max_price = y.max()
min_price = y.min()
max_price_date = ds[y.idxmax()]
min_price_date = ds[y.idxmin()]
```

## 数据可视化

在获得数据后，一个重要的步骤是将其可视化，以直观地观察其趋势和模式。

我们首先绘制微软股票从1986年至今的整体收盘价走势图。

```python
plt.figure(figsize=(14, 7))
plt.plot(ds, y, label='Close Price', color='red', linewidth=1)
plt.xlabel('Date')
plt.ylabel('Price (USD)')
plt.title(f'{stock_symbol} Stock Price History')
plt.grid(True)
plt.legend()
plt.show()
```

从图中可以看到，微软股价整体呈上升趋势，但在某些时期（如2000年左右）存在明显的下跌，这反映了历史上的市场波动。

上一节我们介绍了如何绘制单一指标（收盘价）的走势图。本节中我们来看看如何同时绘制多个指标，并进行更灵活的时间段选择。

例如，我们可以指定查看2001年至2018年的数据，并同时绘制每日价格变化和交易量。

```python
# 指定起始和结束日期
start = '2001-01-01'
end = '2018-01-01'

# 筛选数据
mask = (ds >= start) & (ds <= end)
filtered_data = data.loc[mask]

# 创建包含两个子图的图形
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10))

# 在第一个子图上绘制每日价格变化
ax1.plot(filtered_data['Date'], filtered_data['daily_change'], color='red', label='Daily Price Change')
ax1.set_ylabel('Price Change (USD)')
ax1.legend()
ax1.grid(True)

# 在第二个子图上绘制交易量
ax2.plot(filtered_data['Date'], filtered_data['Volume'], color='blue', label='Trading Volume')
ax2.set_xlabel('Date')
ax2.set_ylabel('Volume')
ax2.legend()
ax2.grid(True)

plt.suptitle(f'{stock_symbol} Stock: Daily Change & Volume ({start} to {end})')
plt.show()
```

通过这样的对比图，我们可以分析价格剧烈波动时（例如2016年中旬）交易量是否也同步发生了显著变化。

## 模拟股票持有收益

![](img/3a80f335520d94479901bbea9fc0f1cc_9.png)

除了分析趋势，我们还可以模拟一个简单的投资策略：在某个时间点买入股票，在另一个时间点卖出，计算其收益。

![](img/3a80f335520d94479901bbea9fc0f1cc_11.png)

![](img/3a80f335520d94479901bbea9fc0f1cc_13.png)

以下是一个计算持有期收益的函数`calculate_hold_return`。

![](img/3a80f335520d94479901bbea9fc0f1cc_15.png)

![](img/3a80f335520d94479901bbea9fc0f1cc_17.png)

```python
def calculate_hold_return(data, start_date, end_date, shares=100):
    """
    计算在指定期间持有股票的收益。
    参数:
        data: 包含‘Date’和‘Close’列的DataFrame。
        start_date: 买入日期 (字符串，格式‘YYYY-MM-DD’)。
        end_date: 卖出日期 (字符串，格式‘YYYY-MM-DD’)。
        shares: 购买的股数，默认为100。
    返回:
        总收益（浮点数）。
    """
    # 将字符串日期转换为datetime格式
    start_date = pd.to_datetime(start_date)
    end_date = pd.to_datetime(end_date)

    # 获取买入价和卖出价
    buy_price = data.loc[data['Date'] == start_date, 'Close'].values
    sell_price = data.loc[data['Date'] == end_date, 'Close'].values

    if len(buy_price) == 0 or len(sell_price) == 0:
        raise ValueError("指定的日期不在数据范围内。")

    buy_price = buy_price[0]
    sell_price = sell_price[0]

    # 计算总收益
    total_return = (sell_price - buy_price) * shares
    return total_return
```

![](img/3a80f335520d94479901bbea9fc0f1cc_19.png)

使用这个函数，我们可以轻松计算不同投资区间的收益。例如，计算从1986年买入并持有至2018年的收益。

```python
# 假设买入100股
profit = calculate_hold_return(data, '1986-03-13', '2018-01-24', shares=100)
print(f"总收益: ${profit:.2f}")
```

我们也可以尝试不同的买入和卖出日期组合，例如在1999年（股价高点附近）买入，在2002年（股价低点附近）卖出，这时计算结果很可能为负，表示亏损。

![](img/3a80f335520d94479901bbea9fc0f1cc_21.png)

![](img/3a80f335520d94479901bbea9fc0f1cc_23.png)

```python
profit2 = calculate_hold_return(data, '1999-12-01', '2002-01-01', shares=100)
print(f"总收益: ${profit2:.2f}")
```

![](img/3a80f335520d94479901bbea9fc0f1cc_25.png)

![](img/3a80f335520d94479901bbea9fc0f1cc_27.png)

这个简单的模拟揭示了择时在投资中的重要性。

![](img/3a80f335520d94479901bbea9fc0f1cc_29.png)

![](img/3a80f335520d94479901bbea9fc0f1cc_31.png)

## 总结

![](img/3a80f335520d94479901bbea9fc0f1cc_33.png)

![](img/3a80f335520d94479901bbea9fc0f1cc_35.png)

本节课中我们一起学习了时间序列分析在股票数据上的初步应用。我们首先学习了如何使用`yfinance`库读取股票数据，并进行了基本的数据探索。接着，我们使用`matplotlib`对股票价格进行了可视化，观察其长期趋势和短期波动。最后，我们编写了一个函数来模拟计算固定持有期的股票投资回报，直观地展示了不同投资时机可能带来的结果。

![](img/3a80f335520d94479901bbea9fc0f1cc_37.png)

![](img/3a80f335520d94479901bbea9fc0f1cc_39.png)

这些步骤构成了时间序列分析的基础工作流：获取数据、理解数据、可视化数据并基于数据进行简单的计算与模拟，为后续更复杂的分析和建模（如预测未来价格）打下基础。