# Python金融分析与量化交易实战：P35：时间序列分析 📈

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软股票数据为例，从数据读取、可视化到初步分析，一步步构建一个基础的金融数据分析流程。

## 数据读取与初步观察

首先，我们需要读取股票数据。这里我们选择微软的股票进行分析。

```python
import yfinance as yf
import pandas as pd

# 读取微软股票数据
ticker = 'MSFT'
stock_data = yf.download(ticker)
```

执行上述代码后，我们获得了股票的历史数据。数据包含多个指标，如开盘价、收盘价、最高价、最低价和交易量等。我们主要关注的是`Close`（收盘价）这一列。

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_1.png)

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_3.png)

```python
# 查看数据的前几行
print(stock_data.head())
```

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_5.png)

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_7.png)

如果代码执行报错，通常是因为缺少必要的库（如`yfinance`, `pandas`）。请确保已安装这些库。

## 数据预处理与关键指标提取

上一节我们读取了原始数据，本节中我们来看看如何提取关键信息并进行预处理。

以下是数据预处理的关键步骤：
1.  **重置索引**：将日期列设置为索引，便于时间序列分析。
2.  **提取目标变量**：明确我们要预测的指标，即收盘价`Y`。
3.  **计算衍生指标**：例如计算每日价格变动`daily_change`。
4.  **确定时间范围**：找出数据集的起始日期和结束日期。

```python
# 重置索引，将日期列作为索引
df = stock_data.reset_index()

# 提取日期和目标变量（收盘价）
df['ds'] = df['Date']
df['y'] = df['Close']

# 计算每日价格变动（收盘价-开盘价）
df['daily_change'] = df['Close'] - df['Open']

# 确定数据的时间范围
start_date = df['ds'].min()
end_date = df['ds'].max()
print(f"数据时间范围：从 {start_date} 到 {end_date}")
```

通过这些步骤，我们得到了一个结构清晰、包含核心指标的数据集，为后续分析打下基础。

## 数据可视化分析

在准备好数据后，一个重要的步骤是将其可视化，以直观地观察趋势和模式。

我们首先绘制微软股票从上市至今的收盘价走势图。

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 6))
plt.plot(df['ds'], df['y'], label='Close Price', color='red', linewidth=1)
plt.xlabel('Date')
plt.ylabel('Price (USD)')
plt.title(f'{ticker} Stock Price History')
plt.legend()
plt.grid(True)
plt.show()
```

该图表显示了微软股价长期向上的整体趋势，期间也包含一些波动和回调。

此外，我们可以指定时间段或同时绘制多个指标进行比较。例如，同时观察价格变动和交易量。

```python
# 绘制特定时间段（如2000年至今）的价格与交易量
start = '2000-01-01'
mask = (df['ds'] >= start) & (df['ds'] <= end_date)
df_subset = df.loc[mask]

fig, ax1 = plt.subplots(figsize=(12,6))

color = 'tab:red'
ax1.set_xlabel('Date')
ax1.set_ylabel('Price Change', color=color)
ax1.plot(df_subset['ds'], df_subset['daily_change'], color=color, linewidth=1)
ax1.tick_params(axis='y', labelcolor=color)

ax2 = ax1.twinx()  # 共享x轴
color = 'tab:blue'
ax2.set_ylabel('Volume', color=color)
ax2.plot(df_subset['ds'], df_subset['Volume'], color=color, alpha=0.3, linewidth=1)
ax2.tick_params(axis='y', labelcolor=color)

plt.title(f'{ticker} - Daily Change vs. Volume (Since {start})')
fig.tight_layout()
plt.show()
```

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_9.png)

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_11.png)

通过可视化，我们可以快速发现异常点，例如交易量突然激增的日子，这可能对应着重要的市场事件。

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_13.png)

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_15.png)

## 简单的投资回报模拟

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_17.png)

为了更深入地理解数据，我们可以进行一个简单的模拟：如果我们在某个时间点买入并在另一个时间点卖出，收益会如何？

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_19.png)

以下是一个计算并可视化持有期收益的函数。

```python
def calculate_hold_return(df, start_date, end_date, shares=100):
    """
    计算在指定期间持有股票的收益。
    参数:
        df: 包含日期(ds)和收盘价(y)的DataFrame
        start_date: 买入日期 (str 或 datetime)
        end_date: 卖出日期 (str 或 datetime)
        shares: 持有的股数
    返回:
        打印收益信息并绘制图表
    """
    # 确保日期为datetime格式
    start_date = pd.to_datetime(start_date)
    end_date = pd.to_datetime(end_date)

    # 获取买入价和卖出价
    buy_price = df.loc[df['ds'] == start_date, 'y'].values
    sell_price = df.loc[df['ds'] == end_date, 'y'].values

    if len(buy_price) == 0 or len(sell_price) == 0:
        print("错误：指定的日期不在数据范围内。")
        return

    buy_price = buy_price[0]
    sell_price = sell_price[0]

    # 计算总收益
    total_return = (sell_price - buy_price) * shares
    return_pct = ((sell_price / buy_price) - 1) * 100

    print(f"买入日期: {start_date.date()}, 价格: ${buy_price:.2f}")
    print(f"卖出日期: {end_date.date()}, 价格: ${sell_price:.2f}")
    print(f"持有 {shares} 股的总收益: ${total_return:.2f}")
    print(f"收益率: {return_pct:.2f}%")

    # 可视化
    plt.figure(figsize=(10,5))
    # 绘制整个时期的股价
    plt.plot(df['ds'], df['y'], label='Stock Price', color='grey', alpha=0.5)
    # 高亮持有期
    hold_mask = (df['ds'] >= start_date) & (df['ds'] <= end_date)
    plt.plot(df.loc[hold_mask, 'ds'], df.loc[hold_mask, 'y'], color='green', linewidth=2, label='Holding Period')
    # 标记买卖点
    plt.scatter([start_date, end_date], [buy_price, sell_price], color=['red', 'blue'], s=100, zorder=5)
    plt.annotate(f'Buy\n${buy_price:.2f}', (start_date, buy_price), textcoords="offset points", xytext=(-15,10), ha='center')
    plt.annotate(f'Sell\n${sell_price:.2f}', (end_date, sell_price), textcoords="offset points", xytext=(15,-10), ha='center')

    plt.xlabel('Date')
    plt.ylabel('Price (USD)')
    plt.title(f'Hold Analysis for {ticker}')
    plt.legend()
    plt.grid(True)
    plt.show()

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_21.png)

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_23.png)

# 示例：模拟从1999年买入，2002年卖出
calculate_hold_return(df, '1999-01-01', '2002-01-01', shares=100)
```

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_25.png)

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_27.png)

这个模拟清晰地展示了投资时机的重要性。在长期上涨趋势中，不同时间点入场可能带来完全不同的结果，甚至可能亏损。

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_29.png)

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_31.png)

## 总结

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_33.png)

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_35.png)

本节课中我们一起学习了时间序列分析的初步实战步骤：
1.  **数据获取与读取**：使用`yfinance`库获取股票历史数据。
2.  **数据预处理**：提取关键日期和价格指标，为分析做准备。
3.  **数据可视化**：通过绘制价格走势图、多指标对比图，直观理解数据趋势和特征。
4.  **简单分析模拟**：通过一个持有期收益计算函数，模拟不同投资策略的结果，强调了时机在金融分析中的关键作用。

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_37.png)

![](img/fe7aeeec608cb0dd4ac12c0d7e50b619_39.png)

这些步骤构成了金融时间序列分析的基础框架。在后续课程中，我们将在此基础上，引入更复杂的模型（如Facebook Prophet）进行股票价格的预测。