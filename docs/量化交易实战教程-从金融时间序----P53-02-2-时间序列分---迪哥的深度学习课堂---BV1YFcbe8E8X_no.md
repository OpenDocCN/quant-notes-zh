# Python金融分析与量化交易实战：P53：02-2-时间序列分析 📈

在本节课中，我们将学习如何使用Python进行金融时间序列分析。我们将以微软（MSFT）的股票数据为例，从数据读取、可视化到简单的收益计算，逐步讲解时间序列分析的基础操作。

## 数据读取与初步探索

首先，我们需要读取股票数据。这里我们选择微软（MSFT）的股票历史数据。

```python
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt

# 读取微软股票数据
ticker = ‘MSFT’
data = yf.download(ticker)
```

![](img/d4f67f72d3475cb68e98d0743a624a46_1.png)

执行上述代码后，我们会得到一个包含日期（`Date`）、开盘价（`Open`）、最高价（`High`）、最低价（`Low`）、收盘价（`Close`）、调整后收盘价（`Adj Close`）和成交量（`Volume`）等指标的`DataFrame`。我们主要关注`Date`和`Close`（收盘价）这两个字段。

![](img/d4f67f72d3475cb68e98d0743a624a46_3.png)

![](img/d4f67f72d3475cb68e98d0743a624a46_5.png)

![](img/d4f67f72d3475cb68e98d0743a624a46_7.png)

如果代码执行报错，通常是因为`yfinance`、`pandas`或`matplotlib`库没有安装好，需要先安装这些库。

数据读取后，我们可以进行一些基本的数据整理和探索。

```python
# 重置索引，将日期列变为普通列（如果日期是索引的话）
data = data.reset_index()

# 指定时间序列的日期列和目标预测列
ds = data[‘Date’]
y = data[‘Close’]

# 计算每日价格变化
data[‘Daily_Change’] = data[‘Close’] - data[‘Open’]

# 查看数据的基本时间范围
start_date = data[‘Date’].min()
end_date = data[‘Date’].max()
print(f“数据起始日期：{start_date}”)
print(f“数据结束日期：{end_date}”)
```

通过以上步骤，我们获得了数据的时间范围、收盘价序列以及每日价格变化等基础信息。

## 数据可视化

在获得数据后，可视化是理解时间序列趋势的关键步骤。我们将绘制微软股票的历史收盘价走势图。

以下是绘制基本走势图的函数示例：

```python
def plot_stock_data(data, start_date=None, end_date=None, columns=‘Close’):
    “””
    绘制股票数据图
    data: 包含股票数据的DataFrame
    start_date: 起始日期（可选）
    end_date: 结束日期（可选）
    columns: 要绘制的列名，可以是单个字符串或多个字符串的列表
    “””
    # 如果没有指定日期范围，则使用全部数据
    if start_date is None:
        start_date = data[‘Date’].min()
    if end_date is None:
        end_date = data[‘Date’].max()

    # 将日期字符串转换为datetime格式
    start_date = pd.to_datetime(start_date)
    end_date = pd.to_datetime(end_date)

    # 筛选指定日期范围内的数据
    mask = (data[‘Date’] >= start_date) & (data[‘Date’] <= end_date)
    plot_data = data.loc[mask]

    plt.figure(figsize=(12, 6))
    # 支持绘制单列或多列数据
    if isinstance(columns, str):
        columns = [columns]
    for col in columns:
        plt.plot(plot_data[‘Date’], plot_data[col], label=col, linewidth=2)

    plt.title(f‘Stock Price Trend ({start_date.date()} to {end_date.date()})’)
    plt.xlabel(‘Date’)
    plt.ylabel(‘Price’)
    plt.legend()
    plt.grid(True)
    plt.show()

# 绘制微软股票收盘价全时段走势图
plot_stock_data(data, columns=‘Close’)
```

从绘制的图中，我们可以看到微软股票从1986年至今的整体上升趋势，期间虽有波动，但长期看是增长的。

我们也可以指定特定时间段或同时绘制多个指标（如收盘价和成交量）进行对比分析。

```python
# 绘制2001年至2018年的收盘价和成交量
plot_stock_data(data, start_date=‘2001-01-01’, end_date=‘2018-01-01’, columns=[‘Close’, ‘Volume’])
```

通过对比图，可以观察价格变动与交易量之间的关系。

## 简单收益计算模拟

为了更直观地理解投资，我们可以模拟一个简单的“买入并持有”策略，计算在特定时间段内购买一定数量股票的收益。

以下是计算收益并绘图的函数：

![](img/d4f67f72d3475cb68e98d0743a624a46_9.png)

```python
def calculate_hold_return(data, start_date, end_date, shares=100):
    “””
    计算买入持有策略的收益
    data: 股票数据
    start_date: 买入日期
    end_date: 卖出日期
    shares: 购买的股数
    “””
    # 确保日期为datetime格式
    start_date = pd.to_datetime(start_date)
    end_date = pd.to_datetime(end_date)

    # 获取买入价和卖出价（收盘价）
    buy_price = data.loc[data[‘Date’] == start_date, ‘Close’].values
    sell_price = data.loc[data[‘Date’] == end_date, ‘Close’].values

    if len(buy_price) == 0 or len(sell_price) == 0:
        print(“指定的日期不在数据范围内。”)
        return

    buy_price = buy_price[0]
    sell_price = sell_price[0]

    # 计算收益
    profit_per_share = sell_price - buy_price
    total_profit = profit_per_share * shares

    # 打印结果
    print(f“买入日期：{start_date.date()}， 价格：${buy_price:.2f}”)
    print(f“卖出日期：{end_date.date()}， 价格：${sell_price:.2f}”)
    print(f“每股收益：${profit_per_share:.2f}”)
    print(f“购买{shares}股的总收益：${total_profit:.2f}”)

    # 可视化该时间段内的股价走势
    plot_stock_data(data, start_date=start_date, end_date=end_date, columns=‘Close’)
    # 在图上标记买入和卖出点
    plt.plot(start_date, buy_price, ‘g^’, markersize=10, label=‘Buy’)
    plt.plot(end_date, sell_price, ‘rv’, markersize=10, label=‘Sell’)
    plt.legend()
    plt.show()

![](img/d4f67f72d3475cb68e98d0743a624a46_11.png)

![](img/d4f67f72d3475cb68e98d0743a624a46_13.png)

# 示例：计算从1986年买入到2018年卖出的收益
calculate_hold_return(data, start_date=‘1986-03-13’, end_date=‘2018-01-24’, shares=100)
```

![](img/d4f67f72d3475cb68e98d0743a624a46_15.png)

![](img/d4f67f72d3475cb68e98d0743a624a46_17.png)

通过这个函数，我们可以输入任意买入和卖出日期，快速计算模拟收益。例如，在1986年微软上市初期买入并长期持有，将会获得非常可观的回报。反之，如果在某些股价高点买入并在低点卖出，则可能亏损。

![](img/d4f67f72d3475cb68e98d0743a624a46_19.png)

这个简单的模拟有助于理解时间序列分析在金融决策中的基础应用。

## 总结

![](img/d4f67f72d3475cb68e98d0743a624a46_21.png)

本节课中我们一起学习了金融时间序列分析的入门操作。

![](img/d4f67f72d3475cb68e98d0743a624a46_23.png)

![](img/d4f67f72d3475cb68e98d0743a624a46_25.png)

![](img/d4f67f72d3475cb68e98d0743a624a46_27.png)

我们首先使用`yfinance`库读取了微软的股票历史数据，并进行了初步的数据整理，明确了分析的目标列（日期和收盘价）。

![](img/d4f67f72d3475cb68e98d0743a624a46_29.png)

接着，我们通过自定义绘图函数，实现了对股票价格走势的可视化，包括全时段趋势观察和特定时间段的多指标对比分析。

![](img/d4f67f72d3475cb68e98d0743a624a46_31.png)

![](img/d4f67f72d3475cb68e98d0743a624a46_33.png)

最后，我们通过一个“买入并持有”策略的收益计算模拟，将时间序列数据与实际的投资决策联系起来，直观展示了数据分析的应用价值。

![](img/d4f67f72d3475cb68e98d0743a624a46_35.png)

![](img/d4f67f72d3475cb68e98d0743a624a46_37.png)

![](img/d4f67f72d3475cb68e98d0743a624a46_39.png)

这些基础操作为后续更深入的时间序列预测和量化交易策略构建打下了坚实的基础。在接下来的课程中，我们将在此基础上，介绍更复杂的分析模型和预测方法。