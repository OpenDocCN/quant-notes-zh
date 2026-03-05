# Python金融量化交易：3.3.2：时间序列分析 📈

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软（MSFT）的股票数据为例，从数据读取、可视化到简单分析，一步步带你入门。

## 数据读取与初步观察

首先，我们需要读取股票数据。这里我们选择微软（MSFT）的股票历史数据作为分析对象。

以下是读取数据的关键步骤：

```python
import yfinance as yf
import pandas as pd

![](img/5455b2bc12eeda295c224a86465295c9_1.png)

# 读取微软股票数据
msft = yf.Ticker("MSFT")
hist = msft.history(period="max")
```

![](img/5455b2bc12eeda295c224a86465295c9_3.png)

![](img/5455b2bc12eeda295c224a86465295c9_5.png)

执行上述代码后，我们获得了一个包含日期（`Date`）、开盘价（`Open`）、收盘价（`Close`）等指标的数据集。我们主要关注的是收盘价（`Close`），这是我们后续要预测的目标变量。

![](img/5455b2bc12eeda295c224a86465295c9_7.png)

初次运行代码时可能会稍慢，因为需要加载相关库。如果代码报错，通常是因为 `yfinance`、`pandas` 等库没有正确安装，需要先安装这些依赖。

数据读取完成后，我们可以查看数据的基本结构：

```python
print(hist.head())
```

## 数据探索与预处理

上一节我们成功读取了数据，本节中我们来看看如何对数据进行初步的探索和整理。

数据读取后，我们进行以下操作：
1.  重置索引，使日期成为一列数据。
2.  提取我们关心的核心指标，如日期（`ds`）和收盘价（`y`）。
3.  计算一些衍生指标，如每日价格变化（`daily_change`）。

以下是关键代码片段：

```python
# 重置索引，使‘Date’成为列
df = hist.reset_index()
# 指定日期列和目标预测列（收盘价）
df['ds'] = pd.to_datetime(df['Date'])
df['y'] = df['Close']
# 计算每日价格变化
df['daily_change'] = df['Close'] - df['Open']
```

我们可以轻松计算出数据的时间范围、历史最高/最低价及其发生的日期：

```python
start_date = df['ds'].min()
end_date = df['ds'].max()
max_price = df['y'].max()
min_price = df['y'].min()
```

## 数据可视化分析

掌握了数据的基本情况后，可视化能帮助我们更直观地理解趋势。本节我们将学习如何绘制股票价格的时间序列图。

我们定义一个绘图函数，可以灵活地指定绘图的时间范围和指标。

```python
import matplotlib.pyplot as plt

def plot_stock_data(data, start_date=None, end_date=None, columns=['Close']):
    # 过滤指定日期范围的数据
    mask = (data['ds'] >= start_date) & (data['ds'] <= end_date)
    filtered_data = data.loc[mask] if start_date and end_date else data

    plt.figure(figsize=(12, 6))
    for col in columns:
        plt.plot(filtered_data['ds'], filtered_data[col], label=col)
    plt.xlabel('Date')
    plt.ylabel('Price')
    plt.title('Stock Price Trend')
    plt.legend()
    plt.grid(True)
    plt.show()
```

使用该函数，我们可以绘制微软股票从1986年至今的整体收盘价趋势图：

```python
plot_stock_data(df, columns=['Close'])
```

从图中可以看出，微软股价整体呈上升趋势，但在某些时期（如2000年左右）存在明显的波动或下跌。

我们还可以同时绘制多个指标进行对比，例如同时观察收盘价和交易量（`Volume`）：

![](img/5455b2bc12eeda295c224a86465295c9_9.png)

```python
plot_stock_data(df, start_date='2016-01-01', end_date='2018-01-01', columns=['Close', 'Volume'])
```

![](img/5455b2bc12eeda295c224a86465295c9_11.png)

![](img/5455b2bc12eeda295c224a86465295c9_13.png)

## 模拟投资回报计算

![](img/5455b2bc12eeda295c224a86465295c9_15.png)

![](img/5455b2bc12eeda295c224a86465295c9_17.png)

除了观察趋势，我们还可以进行简单的模拟投资分析。本节我们通过一个函数来计算在特定时间段内买入并持有股票的理论收益。

![](img/5455b2bc12eeda295c224a86465295c9_19.png)

我们定义一个 `calculate_return` 函数来模拟这个过程：

```python
def calculate_return(data, start_date, end_date, shares=100):
    # 获取买入和卖出时的价格
    buy_price = data.loc[data['ds'] == start_date, 'Close'].values[0]
    sell_price = data.loc[data['ds'] == end_date, 'Close'].values[0]

    # 计算总收益
    total_return = (sell_price - buy_price) * shares
    return total_return

![](img/5455b2bc12eeda295c224a86465295c9_21.png)

# 示例：计算从1986-03-13买入到2018-01-24卖出的收益
profit = calculate_return(df, '1986-03-13', '2018-01-24', shares=100)
print(f"理论收益为：${profit:.2f}")
```

![](img/5455b2bc12eeda295c224a86465295c9_23.png)

![](img/5455b2bc12eeda295c224a86465295c9_25.png)

![](img/5455b2bc12eeda295c224a86465295c9_27.png)

这个简单的计算表明，如果在1986年微软上市初期买入并长期持有，将获得可观的回报。然而，如果买入和卖出的时间点选择不当（例如在股价高点买入，低点卖出），则可能导致亏损。我们可以通过改变 `start_date` 和 `end_date` 参数来验证不同投资策略的结果。

![](img/5455b2bc12eeda295c224a86465295c9_29.png)

## 总结

![](img/5455b2bc12eeda295c224a86465295c9_31.png)

![](img/5455b2bc12eeda295c224a86465295c9_33.png)

![](img/5455b2bc12eeda295c224a86465295c9_35.png)

本节课中我们一起学习了时间序列分析的基础操作：
1.  **数据获取**：使用 `yfinance` 库读取指定股票的历史数据。
2.  **数据整理**：对数据进行清洗，提取日期和目标变量，并计算衍生指标。
3.  **趋势可视化**：通过绘制时间序列图，直观展示股票价格的历史走势和关键指标。
4.  **简单分析**：通过模拟计算不同时期的买入持有收益，理解时间点选择在投资中的重要性。

![](img/5455b2bc12eeda295c224a86465295c9_37.png)

![](img/5455b2bc12eeda295c224a86465295c9_39.png)

这些步骤构成了时间序列分析的初步框架，为后续更深入的建模和预测奠定了基础。