# 人工智能数据挖掘实战：P3：时间序列分析 📈

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软股票数据为例，从数据读取、可视化到初步分析，带你一步步上手。

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

![](img/74d2e1b902b689106e6f3495c2f13952_1.png)

执行上述代码后，我们获得了股票的历史数据。数据包含多个指标，如日期（Date）、开盘价（Open）、收盘价（Close）等。我们主要关注的是收盘价（Close），这是我们后续要预测的目标变量。

![](img/74d2e1b902b689106e6f3495c2f13952_3.png)

![](img/74d2e1b902b689106e6f3495c2f13952_5.png)

![](img/74d2e1b902b689106e6f3495c2f13952_7.png)

为了确认数据已正确加载，我们可以查看数据的前几行。

```python
print(hist.head())
```

如果代码执行没有报错，说明所需的库（如yfinance, pandas, matplotlib）已正确安装。若报错，请先安装这些库。

## 数据探索与处理

上一节我们成功读取了数据，本节中我们来看看如何对数据进行初步的探索和处理。

数据读取后，我们进行以下操作：
1.  重置索引，使日期成为一列数据。
2.  提取出时间序列（ds）和目标变量（y），即日期和收盘价。
3.  计算每日价格变动（daily_change）等衍生指标。

以下是关键步骤的代码：

```python
# 重置索引，使‘Date’成为列
df = hist.reset_index()

# 指定时间序列和目标变量
df['ds'] = pd.to_datetime(df['Date'])
df['y'] = df['Close']

# 计算每日价格变动（收盘价-开盘价）
df['daily_change'] = df['Close'] - df['Open']

# 查看数据的时间范围
start_date = df['ds'].min()
end_date = df['ds'].max()
print(f"数据时间范围：从 {start_date} 到 {end_date}")
```

通过`min()`和`max()`函数，我们可以轻松获取数据的时间跨度。同时，我们也能计算出历史最高价、最低价及其发生的日期。

## 时间序列可视化

在掌握了数据的基本情况后，可视化是理解时间序列趋势的关键步骤。我们将绘制微软股票收盘价随时间变化的走势图。

我们使用一个封装好的绘图函数，它可以灵活地绘制指定时间范围和指标。

```python
def plot_stock_data(data, start_date=None, end_date=None, columns=['Close']):
    """
    绘制股票数据图
    data: 包含‘ds’和指标列的DataFrame
    start_date: 起始日期
    end_date: 结束日期
    columns: 需要绘制的指标列名列表
    """
    # 过滤时间范围
    if start_date is None:
        start_date = data['ds'].min()
    if end_date is None:
        end_date = data['ds'].max()

    mask = (data['ds'] >= start_date) & (data['ds'] <= end_date)
    plot_data = data.loc[mask]

    # 绘图
    plt.figure(figsize=(14, 7))
    for col in columns:
        plt.plot(plot_data['ds'], plot_data[col], label=col, linewidth=2)
    plt.xlabel('Date')
    plt.ylabel('Price (USD)')
    plt.title(f'Stock Price Trend ({start_date.date()} to {end_date.date()})')
    plt.legend()
    plt.grid(True)
    plt.show()

# 绘制整个时间段的收盘价
plot_stock_data(df, columns=['Close'])
```

运行代码后，我们可以看到微软股票从1986年至今的整体上升趋势。图中也能观察到一些历史上的波动期。

## 多指标对比与交互分析

单一的收盘价图表展示了长期趋势。为了进行更深入的分析，我们可以同时对比多个指标，例如收盘价和交易量。

![](img/74d2e1b902b689106e6f3495c2f13952_9.png)

以下是同时绘制收盘价与每日交易量的示例：

![](img/74d2e1b902b689106e6f3495c2f13952_11.png)

![](img/74d2e1b902b689106e6f3495c2f13952_13.png)

```python
# 绘制2001年之后的收盘价与交易量（需先确保数据中有‘Volume’列）
plot_stock_data(df, start_date=pd.Timestamp('2001-01-01'), columns=['Close', 'Volume'])
```

![](img/74d2e1b902b689106e6f3495c2f13952_15.png)

![](img/74d2e1b902b689106e6f3495c2f13952_17.png)

这张图可以帮助我们分析价格大幅波动时，交易量是否同步发生显著变化，从而洞察市场情绪。

![](img/74d2e1b902b689106e6f3495c2f13952_19.png)

此外，我们可以进行一个简单的投资回报模拟分析。假设在某个时间点买入，在另一个时间点卖出，计算其收益情况。

```python
def calculate_return(data, buy_date, sell_date, shares=100):
    """
    计算指定买卖日期和股数下的收益
    """
    buy_price = data.loc[data['ds'] == buy_date, 'Close'].values[0]
    sell_price = data.loc[data['ds'] == sell_date, 'Close'].values[0]

    profit = (sell_price - buy_price) * shares
    return_percent = (sell_price / buy_price - 1) * 100

    print(f"买入日期: {buy_date.date()}, 价格: ${buy_price:.2f}")
    print(f"卖出日期: {sell_date.date()}, 价格: ${sell_price:.2f}")
    print(f"持有 {shares} 股的收益: ${profit:.2f}")
    print(f"收益率: {return_percent:.2f}%")
    return profit

![](img/74d2e1b902b689106e6f3495c2f13952_21.png)

# 示例：计算从1986年买入持有至2018年的收益
profit = calculate_return(df,
                          buy_date=df['ds'].min(),
                          sell_date=df['ds'].max(),
                          shares=100)
```

![](img/74d2e1b902b689106e6f3495c2f13952_23.png)

![](img/74d2e1b902b689106e6f3495c2f13952_25.png)

![](img/74d2e1b902b689106e6f3495c2f13952_27.png)

通过改变`buy_date`和`sell_date`，我们可以模拟在不同市场阶段投资的盈亏情况，直观地理解时间点和持有周期对投资结果的影响。

![](img/74d2e1b902b689106e6f3495c2f13952_29.png)

## 总结

![](img/74d2e1b902b689106e6f3495c2f13952_31.png)

![](img/74d2e1b902b689106e6f3495c2f13952_33.png)

![](img/74d2e1b902b689106e6f3495c2f13952_35.png)

本节课中我们一起学习了时间序列分析的初始步骤。
1.  我们使用`yfinance`库读取了微软股票的历史数据。
2.  我们对数据进行了初步处理，提取了时间序列和目标变量。
3.  我们通过绘制股票价格走势图，直观地观察了其长期趋势和短期波动。
4.  我们进行了多指标对比和简单的投资回报模拟分析，加深了对时间序列数据在金融领域应用的理解。

![](img/74d2e1b902b689106e6f3495c2f13952_37.png)

![](img/74d2e1b902b689106e6f3495c2f13952_39.png)

这些操作为后续更复杂的时间序列预测模型（如使用Facebook Prophet框架）奠定了坚实的基础。在接下来的课程中，我们将利用处理好的数据，开始构建预测模型。