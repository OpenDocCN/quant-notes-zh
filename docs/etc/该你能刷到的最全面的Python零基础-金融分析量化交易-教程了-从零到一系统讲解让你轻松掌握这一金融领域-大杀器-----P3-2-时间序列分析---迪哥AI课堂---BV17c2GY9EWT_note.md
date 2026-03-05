# Python金融分析量化交易：P3：2-时间序列分析 📈

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软股票数据为例，从数据读取、可视化到初步分析，系统地讲解处理时间序列数据的基本流程。

---

## 数据读取与初步观察

首先，我们需要读取股票数据。这里选择微软（MSFT）的股票历史数据作为示例。

```python
# 示例代码：读取微软股票数据
import yfinance as yf
msft = yf.Ticker("MSFT")
hist = msft.history(period="max")
print(hist.head())
```

执行上述代码后，我们会得到一个包含日期（`Date`）和多个股价指标（如开盘价、收盘价等）的`DataFrame`。我们主要关注的是收盘价（`Close`），它将是后续预测的目标变量。

![](img/bcd78ca0beea422f95d70e75498c55a0_1.png)

![](img/bcd78ca0beea422f95d70e75498c55a0_3.png)

![](img/bcd78ca0beea422f95d70e75498c55a0_5.png)

在首次运行代码时，可能会因为需要加载相关库而稍慢。如果代码报错，通常是因为未安装`yfinance`、`pandas`等必要库，需要先通过`pip install`命令安装它们。

![](img/bcd78ca0beea422f95d70e75498c55a0_7.png)

---

## 数据探索与处理

上一节我们读取了原始数据，本节中我们来看看如何对数据进行初步的探索和处理。

数据读取后，我们进行以下操作：
1.  将日期列设置为索引（如果尚未设置）。
2.  提取出我们需要的时间序列`ds`（日期）和目标序列`y`（收盘价）。
3.  计算每日价格变动等衍生指标。

以下是关键步骤的代码说明：

```python
# 重置索引，确保‘Date’成为一列
df = hist.reset_index()
# 指定日期列和目标列
df['ds'] = pd.to_datetime(df['Date'])
df['y'] = df['Close']
# 计算每日价格变动
df['daily_change'] = df['Close'] - df['Open']
```

通过`df['ds'].min()`和`df['ds'].max()`可以轻松获取数据的时间范围。同时，我们可以找出历史最高价、最低价及其发生的日期。

---

## 时间序列可视化

当我们拿到一个时间序列后，第一步通常是将其可视化，以观察整体趋势、周期性和异常点。

我们使用封装好的绘图函数来绘制微软股票收盘价随时间变化的曲线。

```python
# 调用绘图函数绘制收盘价趋势图
plot_stock_trend(df, column='Close')
```

生成的图表显示了微软股票从1986年至2018年初的整体走势。尽管中间存在波动（例如2000年左右的互联网泡沫时期），但长期趋势是显著上升的。图表中还会标记出历史最高点、最低点以及最新价格。

**绘图函数内部逻辑简述**：
该函数核心是使用`matplotlib`绘制折线图。它允许用户指定起始日期、结束日期以及需要绘制的一个或多个指标（如`Close`, `Volume`）。其流程包括：
1.  根据输入的日期范围筛选数据。
2.  循环遍历每个需要绘制的指标，分别画线。
3.  添加网格、坐标轴标签和标题，使图表更清晰。

---

## 多指标对比分析

除了观察单一价格趋势，对比分析多个指标能提供更多信息。例如，我们可以将每日价格变动与交易量放在同一图中观察。

以下是同时绘制价格变动和交易量的示例：

```python
# 绘制两个指标：每日价格变动和交易量
plot_stock_trend(df, columns=['daily_change', 'Volume'], start_date='2016-01-01', pro_type='pct')
```

在这个图中，蓝色线条代表交易量，红色线条代表每日价格变动百分比。通过对比可以发现，在交易量突然激增的日子，价格往往也伴随着较大波动。这种可视化有助于发现市场中的关键事件点。

![](img/bcd78ca0beea422f95d70e75498c55a0_9.png)

---

![](img/bcd78ca0beea422f95d70e75498c55a0_11.png)

![](img/bcd78ca0beea422f95d70e75498c55a0_13.png)

## 模拟投资回报分析

![](img/bcd78ca0beea422f95d70e75498c55a0_15.png)

![](img/bcd78ca0beea422f95d70e75498c55a0_17.png)

一个有趣的应用是模拟投资回报。我们可以定义一个函数，计算在特定时间点买入并在另一时间点卖出一定数量股票后的盈利情况。

![](img/bcd78ca0beea422f95d70e75498c55a0_19.png)

函数`calculate_profit`的核心计算公式如下：

**收益 = (卖出价格 - 买入价格) × 持股数量**

![](img/bcd78ca0beea422f95d70e75498c55a0_21.png)

```python
def calculate_profit(df, start_date, end_date, shares=100):
    buy_price = df.loc[df['ds'] == start_date, 'Close'].values[0]
    sell_price = df.loc[df['ds'] == end_date, 'Close'].values[0]
    profit = (sell_price - buy_price) * shares
    return profit

![](img/bcd78ca0beea422f95d70e75498c55a0_23.png)

![](img/bcd78ca0beea422f95d70e75498c55a0_25.png)

![](img/bcd78ca0beea422f95d70e75498c55a0_27.png)

# 示例：计算1986年买入，2018年卖出100股的收益
profit = calculate_profit(df, '1986-03-13', '2018-01-24', 100)
print(f"模拟收益：${profit:.2f}")
```

![](img/bcd78ca0beea422f95d70e75498c55a0_29.png)

通过改变`start_date`和`end_date`，我们可以分析不同投资区间的结果。例如，在1999年互联网泡沫高点买入，而在2002年低点卖出，则会模拟出亏损的场景，这直观地展示了择时的重要性。

![](img/bcd78ca0beea422f95d70e75498c55a0_31.png)

![](img/bcd78ca0beea422f95d70e75498c55a0_33.png)

![](img/bcd78ca0beea422f95d70e75498c55a0_35.png)

---

![](img/bcd78ca0beea422f95d70e75498c55a0_37.png)

![](img/bcd78ca0beea422f95d70e75498c55a0_39.png)

本节课中我们一起学习了时间序列分析的基础操作：从使用`yfinance`获取金融数据，到进行数据清洗和指标计算；从使用绘图函数可视化单一及多个时间序列趋势，到进行简单的模拟投资回报分析。这些步骤构成了金融时间序列分析的初步框架，为后续更深入的建模和预测打下了坚实的基础。