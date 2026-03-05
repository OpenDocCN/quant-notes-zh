# 人生苦短，我学量化！：P72：时间序列分析实战 - 数据获取与可视化 📈

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软股票数据为例，完成数据获取、初步探索和可视化。我们将使用`yfinance`等库来获取金融数据，并通过自定义的绘图函数来观察股票走势。

## 概述

我们将从获取微软（MSFT）的历史股票数据开始，然后对数据进行基本的探索性分析，包括查看数据结构、提取关键指标，并绘制股票价格随时间变化的趋势图。最后，我们会演示一个简单的模拟投资回报计算。

---

### 第一步：读取数据

首先，我们需要读取股票数据。这里我们选择微软（MSFT）的股票，获取其截至今日的历史数据。

以下是读取数据并查看其结构的代码：

![](img/7a39d5191802b1af8db3e0aac0a63c88_1.png)

```python
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt

![](img/7a39d5191802b1af8db3e0aac0a63c88_3.png)

# 指定股票代码
stock_symbol = 'MSFT'
# 获取历史数据
stock_data = yf.download(stock_symbol)
# 查看数据前几行
print(stock_data.head())
```

执行上述代码后，我们会得到一个`DataFrame`，其中包含日期（`Date`）、开盘价（`Open`）、最高价（`High`）、最低价（`Low`）、收盘价（`Close`）、调整后收盘价（`Adj Close`）和成交量（`Volume`）等列。在本教程中，我们主要关注收盘价（`Close`）这一指标。

**注意**：首次运行代码时可能会稍慢，因为需要下载和加载相关库。如果代码报错，请确保已正确安装`yfinance`、`pandas`和`matplotlib`库。

---

### 第二步：数据探索与预处理

在获取数据后，我们需要进行一些基本的探索和预处理，以便后续分析。

以下是数据探索与预处理的步骤：

1.  **重置索引**：将日期列设置为索引，方便时间序列操作。
2.  **提取关键指标**：定义时间（`D`）和目标预测变量（`Y`，即收盘价）。
3.  **计算日变化**：计算收盘价与开盘价之间的差异，作为`daily_change`指标。
4.  **确定时间范围**：找出数据中的最早日期和最晚日期。
5.  **计算极值**：找出历史最高价、最低价及其发生的日期。

```python
# 重置索引，将‘Date’列设为索引
stock_data = stock_data.reset_index()

# 定义时间列和目标列
D = pd.to_datetime(stock_data['Date'])
Y = stock_data['Close']

# 计算日变化
stock_data['daily_change'] = stock_data['Close'] - stock_data['Open']

# 确定时间范围
start_date = D.min()
end_date = D.max()

# 计算价格极值
max_price = Y.max()
min_price = Y.min()
max_price_date = D[Y.idxmax()]
min_price_date = D[Y.idxmin()]

print(f"数据时间范围: {start_date} 到 {end_date}")
print(f"历史最高价: {max_price} (日期: {max_price_date})")
print(f"历史最低价: {min_price} (日期: {min_price_date})")
```

通过以上步骤，我们得到了数据的基本概况，为后续可视化做好了准备。

---

### 第三步：数据可视化

在时间序列分析中，可视化是理解数据趋势和模式的关键步骤。我们将绘制微软股票收盘价随时间变化的走势图。

以下是绘制基本趋势图的代码：

```python
def plot_stock_data(data, start_date=None, end_date=None, columns=['Close']):
    """
    绘制股票数据指定列的趋势图。
    
    参数:
    data: 包含股票数据的DataFrame。
    start_date: 绘图起始日期。
    end_date: 绘图结束日期。
    columns: 需要绘制的列名列表。
    """
    # 设置日期范围
    if start_date is None:
        start_date = data['Date'].min()
    if end_date is None:
        end_date = data['Date'].max()
    
    # 筛选指定日期范围内的数据
    mask = (data['Date'] >= start_date) & (data['Date'] <= end_date)
    plot_data = data.loc[mask]
    
    # 设置绘图样式
    plt.style.use('seaborn-darkgrid')
    
    # 绘制指定列
    colors = ['red', 'blue', 'green', 'orange']  # 预定义颜色
    for i, col in enumerate(columns):
        plt.plot(plot_data['Date'], plot_data[col], color=colors[i % len(colors)], linewidth=2, label=col, alpha=0.8)
    
    # 添加图表元素
    plt.xlabel('Date')
    plt.ylabel('Price (USD)')
    plt.title(f'Stock Price Trend ({start_date.date()} to {end_date.date()})')
    plt.legend()
    plt.grid(True)
    plt.show()

# 绘制微软股票收盘价趋势图
plot_stock_data(stock_data, columns=['Close'])
```

运行代码后，我们将看到微软股票从1986年3月13日到2018年1月24日的收盘价走势图。整体趋势是上升的，期间虽有波动，但价格在长期内显著增长。

---

### 第四步：多指标可视化与模拟投资

除了观察单一指标，我们还可以同时可视化多个指标，例如将每日价格变化与成交量结合起来观察。

![](img/7a39d5191802b1af8db3e0aac0a63c88_5.png)

以下是绘制多指标图的示例：

![](img/7a39d5191802b1af8db3e0aac0a63c88_7.png)

```python
# 绘制日变化和成交量
plot_stock_data(stock_data, start_date='2016-01-01', end_date='2018-01-24', columns=['daily_change', 'Volume'])
```

![](img/7a39d5191802b1af8db3e0aac0a63c88_9.png)

![](img/7a39d5191802b1af8db3e0aac0a63c88_11.png)

此图可以揭示特定时间段内（如2016年中旬）交易活动与价格波动的关系。

![](img/7a39d5191802b1af8db3e0aac0a63c88_13.png)

此外，我们可以进行一个简单的模拟投资回报计算，假设在某个时间点买入，在另一个时间点卖出，计算其收益。

![](img/7a39d5191802b1af8db3e0aac0a63c88_15.png)

以下是模拟投资回报的函数：

```python
def calculate_return(data, buy_date, sell_date, shares=100):
    """
    计算在指定日期买入和卖出股票的收益。
    
    参数:
    data: 包含股票数据的DataFrame。
    buy_date: 买入日期。
    sell_date: 卖出日期。
    shares: 购买的股数。
    
    返回:
    总收益。
    """
    buy_price = data.loc[data['Date'] == buy_date, 'Close'].values[0]
    sell_price = data.loc[data['Date'] == sell_date, 'Close'].values[0]
    
    profit_per_share = sell_price - buy_price
    total_profit = profit_per_share * shares
    
    print(f"买入价 (日期 {buy_date}): ${buy_price:.2f}")
    print(f"卖出价 (日期 {sell_date}): ${sell_price:.2f}")
    print(f"每股收益: ${profit_per_share:.2f}")
    print(f"购买 {shares} 股的总收益: ${total_profit:.2f}")
    
    return total_profit

# 示例：计算从1986年买入到2018年卖出的收益
calculate_return(stock_data, buy_date='1986-03-13', sell_date='2018-01-24', shares=100)
```

![](img/7a39d5191802b1af8db3e0aac0a63c88_17.png)

![](img/7a39d5191802b1af8db3e0aac0a63c88_19.png)

通过改变`buy_date`和`sell_date`，我们可以观察不同投资时间窗口下的收益情况。例如，在1999年高点买入并在2002年低点卖出，可能会导致亏损。

![](img/7a39d5191802b1af8db3e0aac0a63c88_21.png)

![](img/7a39d5191802b1af8db3e0aac0a63c88_23.png)

---

![](img/7a39d5191802b1af8db3e0aac0a63c88_25.png)

## 总结

![](img/7a39d5191802b1af8db3e0aac0a63c88_27.png)

本节课中，我们一起学习了时间序列分析在金融数据上的初步应用。我们完成了以下步骤：

![](img/7a39d5191802b1af8db3e0aac0a63c88_29.png)

![](img/7a39d5191802b1af8db3e0aac0a63c88_31.png)

1.  **数据获取**：使用`yfinance`库下载微软股票的历史数据。
2.  **数据探索**：查看了数据结构，提取了关键时间序列指标（如收盘价），并计算了基本的统计信息。
3.  **数据可视化**：绘制了股票价格的长期趋势图，并演示了如何同时可视化多个指标（如价格变化与成交量）。
4.  **模拟分析**：实现了一个简单的函数来模拟特定时间段的投资回报，直观展示了择时的重要性。

![](img/7a39d5191802b1af8db3e0aac0a63c88_33.png)

![](img/7a39d5191802b1af8db3e0aac0a63c88_35.png)

这些步骤构成了时间序列分析的基础工作流程。在接下来的课程中，我们将在此基础上，使用更复杂的模型（如ARIMA、Prophet）进行股票价格的预测。