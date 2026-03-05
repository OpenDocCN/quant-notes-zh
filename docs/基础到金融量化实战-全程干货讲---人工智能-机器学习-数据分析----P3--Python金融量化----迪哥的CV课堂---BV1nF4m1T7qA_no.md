# Python金融量化：3：时间序列分析 📈

![](img/cc67469b21919a7aaeb59757fd8c96ee_0.png)

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软股票数据为例，从数据读取、可视化到初步分析，一步步掌握处理时间序列数据的基本方法。

## 数据读取与初步探索

首先，我们需要读取股票数据。这里我们选择微软（MSFT）的股票历史数据作为分析对象。

```python
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt

# 读取微软股票数据
msft = yf.Ticker("MSFT")
hist = msft.history(period="max")
```

执行上述代码后，我们获得了微软股票的历史数据。数据中包含多个指标，例如开盘价、收盘价、最高价、最低价和交易量等。我们主要关注的是收盘价（`Close`），这是我们后续要预测的核心指标。

![](img/cc67469b21919a7aaeb59757fd8c96ee_2.png)

![](img/cc67469b21919a7aaeb59757fd8c96ee_4.png)

如果代码执行报错，通常是因为缺少必要的库（如`yfinance`, `pandas`, `matplotlib`）。请确保已通过`pip install`命令安装好这些库。

为了更清晰地理解数据读取过程，我们可以通过调试（debug）模式逐步查看。核心步骤是使用`yf.Ticker()`函数传入股票代码，然后调用`.history()`方法获取历史数据。数据以`DataFrame`格式返回，包含了从上市日（1986年3月13日）到当前日期的所有记录。

我们首先对数据进行一些基本处理：

```python
# 重置索引，将日期列变为普通列（如果需要）
df = hist.reset_index()

# 提取关键列：日期（D）和收盘价（Y）
D = df['Date']
Y = df['Close']

# 计算每日价格变动
df['Daily_Change'] = df['Close'] - df['Open']
```

接下来，我们可以计算数据的时间范围、最高/最低价格及其发生日期：

```python
start_date = D.min()
end_date = D.max()
max_price = Y.max()
min_price = Y.min()
max_price_date = D[Y.idxmax()]
min_price_date = D[Y.idxmin()]
```

至此，我们已经成功加载并初步探索了数据。数据包含了从`start_date`到`end_date`的股价信息，`Y`列即为我们关心的收盘价序列。

## 数据可视化

在获得数据后，第一步通常是将其可视化，以观察整体趋势和特征。

以下是绘制股票价格随时间变化的折线图：

```python
plt.figure(figsize=(12, 6))
plt.plot(D, Y, label='Close Price', color='red', linewidth=1)
plt.xlabel('Date')
plt.ylabel('Price (USD)')
plt.title('Microsoft Stock Price History')
plt.legend()
plt.grid(True)
plt.show()
```

从生成的图表可以看出，微软股价从1986年至今整体呈上升趋势，中间虽有波动（例如2000年左右的互联网泡沫时期），但长期表现强劲。

我们也可以绘制其他指标，例如每日价格变动和交易量：

```python
fig, ax1 = plt.subplots(figsize=(12, 6))

color = 'tab:red'
ax1.set_xlabel('Date')
ax1.set_ylabel('Daily Change', color=color)
ax1.plot(D, df['Daily_Change'], color=color, linewidth=1)
ax1.tick_params(axis='y', labelcolor=color)

ax2 = ax1.twinx()
color = 'tab:blue'
ax2.set_ylabel('Volume', color=color)
ax2.plot(D, df['Volume'], color=color, linewidth=1, alpha=0.5)
ax2.tick_params(axis='y', labelcolor=color)

plt.title('Daily Change and Trading Volume')
fig.tight_layout()
plt.show()
```

这张图用红色线表示每日价格变动，蓝色线表示交易量。可以观察到在某些日期（例如2016年中旬），交易量会突然激增，这可能与当时的市场事件有关。

通过封装好的绘图函数，我们可以灵活地指定起始日期、终止日期以及需要绘制的指标，方便进行不同时间段的对比分析。

![](img/cc67469b21919a7aaeb59757fd8c96ee_6.png)

## 模拟投资回报分析

![](img/cc67469b21919a7aaeb59757fd8c96ee_8.png)

一个有趣的应用是模拟在特定时间点买入并持有股票至另一时间点的投资回报。

以下函数计算并可视化假设的投资收益：

```python
def calculate_profit(start_date, end_date, shares=100):
    # 筛选指定时间段的数据
    mask = (D >= start_date) & (D <= end_date)
    period_data = df.loc[mask].copy()

    if period_data.empty:
        print("No data in the selected period.")
        return

    buy_price = period_data.iloc[0]['Open']
    sell_price = period_data.iloc[-1]['Close']

    profit_per_share = sell_price - buy_price
    total_profit = profit_per_share * shares

    print(f"Buy Date: {start_date.date()}, Buy Price: ${buy_price:.2f}")
    print(f"Sell Date: {end_date.date()}, Sell Price: ${sell_price:.2f}")
    print(f"Profit per share: ${profit_per_share:.2f}")
    print(f"Total Profit for {shares} shares: ${total_profit:.2f}")

    # 可视化
    plt.figure(figsize=(10, 5))
    plt.plot(period_data['Date'], period_data['Close'], label='Close Price', color='green')
    plt.axhline(y=buy_price, color='red', linestyle='--', label=f'Buy Price: ${buy_price:.2f}')
    plt.axhline(y=sell_price, color='blue', linestyle='--', label=f'Sell Price: ${sell_price:.2f}')
    plt.fill_between(period_data['Date'], buy_price, period_data['Close'],
                     where=(period_data['Close'] > buy_price),
                     facecolor='green', alpha=0.3, interpolate=True)
    plt.fill_between(period_data['Date'], buy_price, period_data['Close'],
                     where=(period_data['Close'] <= buy_price),
                     facecolor='red', alpha=0.3, interpolate=True)
    plt.xlabel('Date')
    plt.ylabel('Price (USD)')
    plt.title(f'Investment Simulation ({start_date.date()} to {end_date.date()})')
    plt.legend()
    plt.grid(True)
    plt.show()

![](img/cc67469b21919a7aaeb59757fd8c96ee_10.png)

# 示例：模拟从1999年到2002年的投资
calculate_profit(pd.Timestamp('1999-01-01'), pd.Timestamp('2002-01-01'), shares=100)
```

![](img/cc67469b21919a7aaeb59757fd8c96ee_12.png)

![](img/cc67469b21919a7aaeb59757fd8c96ee_14.png)

该函数计算了在`start_date`以开盘价买入，在`end_date`以收盘价卖出的收益。图表中用绿色填充表示盈利区间，红色填充表示亏损区间。例如，在1999年互联网泡沫前买入，2002年卖出，很可能面临亏损。这直观地展示了投资时机的重要性。

![](img/cc67469b21919a7aaeb59757fd8c96ee_16.png)

## 总结

![](img/cc67469b21919a7aaeb59757fd8c96ee_18.png)

![](img/cc67469b21919a7aaeb59757fd8c96ee_20.png)

本节课中我们一起学习了时间序列分析的基础操作：
1.  **数据读取**：使用`yfinance`库获取股票历史数据。
2.  **数据探索**：查看数据的基本信息、时间范围和关键统计值。
3.  **数据可视化**：绘制股价趋势图、多指标对比图，直观理解数据特征。
4.  **简单应用**：模拟特定时间段的投资，计算并可视化潜在收益。

![](img/cc67469b21919a7aaeb59757fd8c96ee_22.png)

![](img/cc67469b21919a7aaeb59757fd8c96ee_24.png)

通过这些步骤，我们掌握了处理时间序列数据的入门技能，并为后续更深入的模型（如Facebook Prophet）分析打下了基础。你可以尝试更换其他股票代码（如亚马逊‘AMZN’），或调整时间周期进行练习。