# Python金融分析量化交易：P3：2-时间序列分析 📈

在本节课中，我们将学习如何对股票价格数据进行时间序列分析。我们将以微软（MSFT）的股票数据为例，从数据读取、可视化到初步分析，一步步了解时间序列数据处理的基本流程。

## 数据读取与初步观察

首先，我们需要读取股票数据。这里我们选择微软（MSFT）的股票，获取其历史价格数据。

```python
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt

# 读取微软股票数据
msft = yf.Ticker("MSFT")
hist = msft.history(period="max")
```

![](img/16eb52bc05e9504b62d6608881aba121_1.png)

执行上述代码后，我们获得了股票的历史数据。接下来，我们观察一下数据的结构。

![](img/16eb52bc05e9504b62d6608881aba121_3.png)

![](img/16eb52bc05e9504b62d6608881aba121_5.png)

```python
print(hist.head())
print(hist.columns)
```

![](img/16eb52bc05e9504b62d6608881aba121_7.png)

数据包含多个列，如开盘价（Open）、最高价（High）、最低价（Low）、收盘价（Close）和交易量（Volume）等。在本教程中，我们主要关注**收盘价（Close）**，将其作为我们要分析和预测的目标变量 `y`。

## 数据探索与预处理

在开始分析前，我们需要对数据进行一些基本的探索和整理。

```python
# 重置索引，将日期列变为普通列，方便操作
df = hist.reset_index()

# 指定日期列和目标列
df['Date'] = pd.to_datetime(df['Date'])
y = df['Close']

# 计算每日价格变化
df['Daily_Change'] = df['Close'] - df['Open']

# 确定数据的时间范围
start_date = df['Date'].min()
end_date = df['Date'].max()
print(f"数据起始日期: {start_date}")
print(f"数据结束日期: {end_date}")

# 计算一些关键价格点
max_price = y.max()
min_price = y.min()
max_price_date = df.loc[y.idxmax(), 'Date']
min_price_date = df.loc[y.idxmin(), 'Date']
print(f"历史最高价: {max_price} (日期: {max_price_date})")
print(f"历史最低价: {min_price} (日期: {min_price_date})")
```

通过以上步骤，我们明确了数据的时间跨度、关键价格及其发生日期，为后续分析奠定了基础。

## 时间序列可视化

可视化是理解时间序列趋势的重要手段。我们将绘制微软股票收盘价随时间变化的曲线。

```python
def plot_stock_data(data, column='Close', start=None, end=None):
    """
    绘制股票指定列的时间序列图
    :param data: 包含日期和价格的数据框
    :param column: 要绘制的列名，例如 'Close'
    :param start: 起始日期（字符串或datetime）
    :param end: 结束日期（字符串或datetime）
    """
    # 筛选时间区间
    if start is not None:
        start = pd.to_datetime(start)
        data = data[data['Date'] >= start]
    if end is not None:
        end = pd.to_datetime(end)
        data = data[data['Date'] <= end]

    plt.figure(figsize=(12, 6))
    plt.plot(data['Date'], data[column], color='red', linewidth=1, label=column, alpha=0.8)
    plt.title(f'Microsoft Stock Price ({column})')
    plt.xlabel('Date')
    plt.ylabel('Price (USD)')
    plt.grid(True)
    plt.legend()
    plt.show()

# 绘制整个时间段的收盘价
plot_stock_data(df, column='Close')
```

从图中可以观察到，微软股价从1986年至今整体呈上升趋势，期间虽有波动（例如2000年左右的互联网泡沫时期），但长期表现强劲。

上一节我们介绍了如何绘制单一指标的趋势图，本节中我们来看看如何同时对比多个指标。

## 多指标对比分析

除了收盘价，交易量（Volume）和每日价格变动（Daily_Change）也是重要的分析维度。以下是同时绘制这两个指标的方法。

```python
# 绘制交易量和每日价格变动的对比图（以百分比变化形式）
def plot_multiple_series(data, columns, start='2001-01-01', end='2018-01-01', plot_type='pct'):
    """
    绘制多个时间序列的对比图
    :param data: 数据框
    :param columns: 要绘制的列名列表
    :param start: 起始日期
    :param end: 结束日期
    :param plot_type: 绘图类型，'pct'表示百分比变化
    """
    start = pd.to_datetime(start)
    end = pd.to_datetime(end)
    filtered_data = data[(data['Date'] >= start) & (data['Date'] <= end)]

    plt.figure(figsize=(14, 7))
    colors = ['blue', 'red']
    for idx, col in enumerate(columns):
        if plot_type == 'pct' and col != 'Volume':
            # 计算百分比变化（假设第一日为基准）
            series = (filtered_data[col] / filtered_data[col].iloc[0] - 1) * 100
            label = f'{col} % Change'
        else:
            series = filtered_data[col]
            label = col
        plt.plot(filtered_data['Date'], series, color=colors[idx], linewidth=1, label=label, alpha=0.7)

    plt.title('Stock Indicators Comparison')
    plt.xlabel('Date')
    plt.ylabel('Value / Percentage Change')
    plt.grid(True)
    plt.legend()
    plt.show()

# 示例：绘制交易量和收盘价百分比变化
plot_multiple_series(df, columns=['Volume', 'Close'], start='2016-01-01', end='2018-01-01')
```

![](img/16eb52bc05e9504b62d6608881aba121_9.png)

通过对比图，我们可以分析交易活跃度（Volume）与价格变动（Close % Change）之间的关系，例如在特定日期交易量激增是否伴随大幅价格波动。

![](img/16eb52bc05e9504b62d6608881aba121_11.png)

![](img/16eb52bc05e9504b62d6608881aba121_13.png)

## 模拟投资回报分析

![](img/16eb52bc05e9504b62d6608881aba121_15.png)

![](img/16eb52bc05e9504b62d6608881aba121_17.png)

一个有趣的应用是模拟历史投资。我们可以编写一个函数，计算在特定时间点买入并在另一时间点卖出所能获得的收益。

![](img/16eb52bc05e9504b62d6608881aba121_19.png)

```python
def calculate_investment_return(data, start_date, end_date, shares=100):
    """
    计算在指定期间购买股票的理论收益
    :param data: 股票数据
    :param start_date: 买入日期
    :param end_date: 卖出日期
    :param shares: 购买的股数
    :return: 总收益金额
    """
    buy_data = data[data['Date'] == pd.to_datetime(start_date)]
    sell_data = data[data['Date'] == pd.to_datetime(end_date)]

    if buy_data.empty or sell_data.empty:
        print("指定的日期不在数据范围内。")
        return None

    buy_price = buy_data['Close'].values[0]
    sell_price = sell_data['Close'].values[0]

    profit_per_share = sell_price - buy_price
    total_profit = profit_per_share * shares

    # 可视化
    plt.figure(figsize=(10, 5))
    plt.plot(data['Date'], data['Close'], color='grey', alpha=0.5, label='Price Trend')
    plt.scatter([buy_data['Date'], sell_data['Date']],
                [buy_price, sell_price],
                color=['green', 'red'], s=100, zorder=5)
    plt.annotate(f'Buy: ${buy_price:.2f}', (buy_data['Date'], buy_price), textcoords="offset points", xytext=(0,10), ha='center')
    plt.annotate(f'Sell: ${sell_price:.2f}', (sell_data['Date'], sell_price), textcoords="offset points", xytext=(0,-15), ha='center')
    plt.title(f'Investment Simulation\nTotal Profit for {shares} shares: ${total_profit:.2f}')
    plt.xlabel('Date')
    plt.ylabel('Price (USD)')
    plt.grid(True)
    plt.legend()
    plt.show()

    return total_profit

# 示例1：长期投资（1986年买入，2018年卖出）
profit1 = calculate_investment_return(df, start_date='1986-03-13', end_date='2018-01-24', shares=100)
print(f"模拟收益: ${profit1:.2f}")

![](img/16eb52bc05e9504b62d6608881aba121_21.png)

# 示例2：短期投资（在波动期间）
profit2 = calculate_investment_return(df, start_date='1999-12-01', end_date='2002-01-01', shares=100)
print(f"模拟收益: ${profit2:.2f}")
```

![](img/16eb52bc05e9504b62d6608881aba121_23.png)

![](img/16eb52bc05e9504b62d6608881aba121_25.png)

![](img/16eb52bc05e9504b62d6608881aba121_27.png)

这个模拟清晰地展示了选择不同投资时机对最终回报的巨大影响，突显了时间序列分析在金融决策中的价值。

![](img/16eb52bc05e9504b62d6608881aba121_29.png)

## 总结

![](img/16eb52bc05e9504b62d6608881aba121_31.png)

![](img/16eb52bc05e9504b62d6608881aba121_33.png)

![](img/16eb52bc05e9504b62d6608881aba121_35.png)

本节课中我们一起学习了时间序列分析的基础操作。我们从读取和观察微软股票数据开始，学习了如何对数据进行预处理和关键指标提取。接着，我们通过绘制图表直观展示了股价的长期趋势和短期波动。最后，我们通过一个模拟投资回报的函数，将分析结果应用于一个简单的决策场景，体会了时间序列数据的实际意义。

![](img/16eb52bc05e9504b62d6608881aba121_37.png)

![](img/16eb52bc05e9504b62d6608881aba121_39.png)

这些步骤构成了金融时间序列分析的基石，为我们后续建立更复杂的预测模型（如使用Facebook Prophet等框架）做好了准备。