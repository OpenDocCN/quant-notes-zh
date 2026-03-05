# Python金融分析与量化交易实战教程：P3：2-时间序列分析

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软股票数据为例，演示数据读取、可视化以及初步分析的过程。

## 数据读取与初步观察

首先，我们需要读取股票数据。这里选择微软的股票进行分析。

```python
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt

# 读取微软股票数据
msft = yf.Ticker("MSFT")
hist = msft.history(period="max")
```

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_1.png)

执行上述代码后，我们获得了股票的历史数据。数据中包含多个指标，例如日期、开盘价、收盘价等。我们主要关注的是收盘价，这是后续预测的目标变量。

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_3.png)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_5.png)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_7.png)

为了确保代码正常运行，需要提前安装 `yfinance`、`pandas` 和 `matplotlib` 库。如果代码报错，请检查这三个库是否已正确安装。

## 数据探索与处理

上一节我们读取了数据，本节中我们来看看数据的具体内容和结构。

以下是数据初步处理的步骤：

1.  **重置索引**：将日期列设置为索引，便于时间序列分析。
2.  **提取关键指标**：明确时间列和目标预测列。
3.  **计算衍生指标**：例如每日价格变动。
4.  **确定时间范围**：找出数据集的起始和结束日期。

```python
# 重置索引，将日期列设为索引
df = hist.reset_index()

# 指定时间列和目标列
df['ds'] = df['Date']  # 时间列
df['y'] = df['Close']  # 目标预测列（收盘价）

# 计算每日价格变动
df['daily_change'] = df['Close'] - df['Open']

# 确定数据的时间范围
start_date = df['ds'].min()
end_date = df['ds'].max()
print(f"数据时间范围：从 {start_date} 到 {end_date}")
```

通过以上操作，我们得到了一个结构清晰的 `DataFrame`，包含了时间、目标值以及一些基础统计信息。

## 数据可视化分析

在掌握了数据的基本情况后，接下来我们通过可视化来直观地观察股票价格的走势。

以下是绘制股票价格趋势图的步骤：

1.  **基础趋势图**：绘制整个时间范围内的收盘价走势。
2.  **自定义区间图**：可以指定起始和结束日期，查看特定时间段内的走势。
3.  **多指标对比图**：例如同时绘制价格和交易量。

```python
# 绘制整个时间段的收盘价趋势图
plt.figure(figsize=(12, 6))
plt.plot(df['ds'], df['y'], color='red', linewidth=1, label='Close Price')
plt.xlabel('Date')
plt.ylabel('Price (USD)')
plt.title('Microsoft Stock Price Trend (1986 - Present)')
plt.grid(True)
plt.legend()
plt.show()
```

从生成的图表可以看出，微软股票价格在长期内呈现整体上升趋势，但中间也存在明显的波动和回调期。

我们也可以绘制特定时间段或多个指标的对比图：

```python
# 示例：绘制2001年至2018年的价格与交易量对比图
mask = (df['ds'] >= '2001-01-01') & (df['ds'] <= '2018-01-01')
df_subset = df.loc[mask]

fig, ax1 = plt.subplots(figsize=(12, 6))

color = 'tab:red'
ax1.set_xlabel('Date')
ax1.set_ylabel('Close Price', color=color)
ax1.plot(df_subset['ds'], df_subset['y'], color=color, linewidth=1)
ax1.tick_params(axis='y', labelcolor=color)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_9.png)

ax2 = ax1.twinx()  # 共享x轴
color = 'tab:blue'
ax2.set_ylabel('Volume', color=color)
ax2.plot(df_subset['ds'], df_subset['Volume'], color=color, linewidth=1, alpha=0.5)
ax2.tick_params(axis='y', labelcolor=color)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_11.png)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_13.png)

plt.title('Microsoft Stock Price and Volume (2001-2018)')
fig.tight_layout()
plt.show()
```

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_15.png)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_17.png)

## 模拟投资收益计算

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_19.png)

为了增加趣味性，我们可以编写一个简单的函数来模拟长期持有股票的投资收益。

该函数的核心逻辑是：`收益 = (卖出价格 - 买入价格) * 持股数量`。

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_21.png)

```python
def calculate_profit(data, start_date, end_date, shares=100):
    """
    计算在指定时间段内买入并持有股票的理论收益。
    
    参数:
    data: 包含日期和收盘价的DataFrame
    start_date: 买入日期 (str 或 datetime)
    end_date: 卖出日期 (str 或 datetime)
    shares: 买入股数 (int)
    
    返回:
    profit: 总收益 (float)
    """
    # 确保日期为datetime格式
    start_date = pd.to_datetime(start_date)
    end_date = pd.to_datetime(end_date)
    
    # 获取买入价和卖出价
    buy_price = data.loc[data['ds'] == start_date, 'y'].values
    sell_price = data.loc[data['ds'] == end_date, 'y'].values
    
    if len(buy_price) == 0 or len(sell_price) == 0:
        return "指定日期不在数据范围内。"
    
    buy_price = buy_price[0]
    sell_price = sell_price[0]
    
    # 计算收益
    profit = (sell_price - buy_price) * shares
    return profit

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_23.png)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_25.png)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_27.png)

# 示例：计算从1986年买入到2018年卖出的收益
profit_example = calculate_profit(df, '1986-03-13', '2018-01-24', shares=100)
print(f"模拟收益: ${profit_example:.2f}")
```

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_29.png)

通过调整买入和卖出日期，我们可以观察到不同投资策略带来的结果差异，例如在股价高点买入可能面临亏损风险。

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_31.png)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_33.png)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_35.png)

## 总结

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_37.png)

![](img/a8addfaaac5533895ae1ae0e9ccce2a4_39.png)

本节课中我们一起学习了时间序列分析的初始步骤。我们以微软股票数据为例，完成了从数据读取、探索性处理到可视化分析的完整流程。关键点包括使用 `yfinance` 获取金融数据，利用 `pandas` 进行数据清洗和转换，并通过 `matplotlib` 绘制趋势图进行直观分析。最后，我们还通过一个简单的模拟计算，了解了如何评估投资回报。这些基础操作为后续更深入的时间序列建模和预测奠定了坚实的基础。