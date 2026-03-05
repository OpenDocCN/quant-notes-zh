# Python量化交易入门：1：使用八行代码计算移动平均线

在本教程中，我们将学习如何使用Python快速计算股票的移动平均线。移动平均线是量化交易中最基础、最常用的技术指标之一，用于平滑价格数据并识别趋势。我们将使用`pandas`库来处理数据，整个过程仅需八行核心代码。

## 数据准备与读取

首先，我们需要获取股票的价格数据。这里我们使用`pandas`库的`DataReader`功能从雅虎财经获取历史数据。你需要确保已经安装了`pandas`和`pandas-datareader`库。

以下是获取数据的代码步骤：

```python
import pandas as pd
import pandas_datareader.data as web
import datetime

# 定义时间范围
start = datetime.datetime(2023, 1, 1)
end = datetime.datetime(2023, 12, 31)

# 获取苹果公司（AAPL）的股票数据
df = web.DataReader('AAPL', 'yahoo', start, end)
```

这段代码会获取苹果公司在2023年全年的每日开盘价、最高价、最低价、收盘价和成交量数据，并存储在一个名为`df`的`DataFrame`中。

## 计算移动平均线

上一节我们获取了股票价格数据，本节中我们来看看如何计算移动平均线。移动平均线是对过去一段时间内收盘价的平均值计算，常用的周期有5日、10日、20日、60日等。

计算移动平均线的核心方法是使用`pandas` `DataFrame`的`.rolling()`和`.mean()`方法。

以下是计算5日和20日简单移动平均线的代码：

```python
# 计算5日简单移动平均线
df['MA5'] = df['Close'].rolling(window=5).mean()

![](img/e716183991e4a09b9688513ceeb64f89_0.png)

# 计算20日简单移动平均线
df['MA20'] = df['Close'].rolling(window=20).mean()
```

在这段代码中：
*   `df[‘Close’]` 选取了收盘价序列。
*   `.rolling(window=5)` 创建了一个窗口大小为5的滚动窗口对象。
*   `.mean()` 对这个滚动窗口内的值计算平均值。

## 结果展示与解读

计算完成后，我们可以查看结果，并理解其含义。移动平均线的主要作用是平滑短期波动，凸显长期趋势。当短期均线（如MA5）上穿长期均线（如MA20）时，常被视为买入信号；反之，下穿时则被视为卖出信号。

以下是查看数据框尾部和绘制图表的方法：

```python
# 查看包含移动平均线的数据框最后几行
print(df[['Close', 'MA5', 'MA20']].tail())

# 可视化收盘价与移动平均线
import matplotlib.pyplot as plt

plt.figure(figsize=(12,6))
plt.plot(df['Close'], label='Close Price', alpha=0.5)
plt.plot(df['MA5'], label='5-Day MA')
plt.plot(df['MA20'], label='20-Day MA')
plt.title('AAPL Stock Price with Moving Averages')
plt.legend()
plt.show()
```

![](img/e716183991e4a09b9688513ceeb64f89_2.png)

运行上述代码，你将看到图表中收盘价曲线与两条更为平滑的移动平均线。通过观察它们的相对位置和交叉情况，可以对市场趋势有一个初步判断。

## 核心代码汇总

为了方便回顾，以下是本教程中用于计算移动平均线的八行核心代码：

```python
import pandas as pd
import pandas_datareader.data as web
import datetime

start = datetime.datetime(2023, 1, 1)
end = datetime.datetime(2023, 12, 31)
df = web.DataReader('AAPL', 'yahoo', start, end)

df['MA5'] = df['Close'].rolling(window=5).mean()
df['MA20'] = df['Close'].rolling(window=20).mean()
```

## 总结

本节课中我们一起学习了量化交易的基础知识。我们从获取股票历史数据开始，然后使用`pandas`库的`.rolling().mean()`方法计算了5日和20日的简单移动平均线，最后通过可视化的方式展示了结果。这八行代码构成了技术分析的一个基本起点，你可以尝试修改代码中的股票代码、时间周期和均线窗口参数，来探索不同的数据和分析结果。