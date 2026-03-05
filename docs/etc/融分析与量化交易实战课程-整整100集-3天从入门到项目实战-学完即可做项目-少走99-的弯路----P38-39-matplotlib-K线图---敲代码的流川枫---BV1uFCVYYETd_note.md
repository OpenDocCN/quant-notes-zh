# 量化交易：P38：Matplotlib K线图绘制教程

![](img/f8c82f8b0552238711c90a67a82ae831_1.png)

![](img/f8c82f8b0552238711c90a67a82ae831_3.png)

![](img/f8c82f8b0552238711c90a67a82ae831_4.png)

## 概述
在本节课中，我们将学习如何使用Matplotlib库绘制金融分析中至关重要的K线图。K线图是展示股票等金融产品开盘价、收盘价、最高价和最低价的核心工具。我们将从K线图的基本概念讲起，逐步深入到使用Matplotlib的`finance`子模块进行实际绘制。

## K线图基础概念
上一节我们介绍了折线图、柱状图和饼图的绘制方法。本节中，我们来看看专门用于金融数据可视化的K线图。

![](img/f8c82f8b0552238711c90a67a82ae831_6.png)

![](img/f8c82f8b0552238711c90a67a82ae831_8.png)

K线图由“实体”和“影线”两部分组成。
*   **实体**：表示开盘价与收盘价之间的价格区间。
*   **影线**：实体上下延伸的细线，分别代表最高价和最低价。

在Matplotlib中，K线图对应的英文是`candlestick`（蜡烛图）。

## 准备工作与`finance`模块
在开始绘制前，我们需要导入必要的模块。Matplotlib提供了一个名为`finance`的子模块，其中包含多种金融图表绘制函数。

![](img/f8c82f8b0552238711c90a67a82ae831_10.png)

**核心代码：导入模块**
```python
import matplotlib.pyplot as plt
import matplotlib.finance as fn
import matplotlib.dates as mdates
```
**注意**：`matplotlib.finance`模块未来可能会被移除或重构，但目前仍可使用。如果导入时出现警告，可以暂时忽略。

![](img/f8c82f8b0552238711c90a67a82ae831_12.png)

![](img/f8c82f8b0552238711c90a67a82ae831_14.png)

## 核心函数：`candlestick_ochl`
我们将使用`candlestick_ochl`函数来绘制K线图。该函数名称中的`OCHL`代表了所需数据的列顺序：Open（开盘价）、Close（收盘价）、High（最高价）、Low（最低价）。函数还有一个变体叫`candlestick_ohlc`，区别在于列的顺序是Open, High, Low, Close。

![](img/f8c82f8b0552238711c90a67a82ae831_16.png)

![](img/f8c82f8b0552238711c90a67a82ae831_18.png)

以下是该函数的主要参数说明：
*   `ax`：一个`Axes`对象，用于指定在哪个子图上进行绘制。
*   `quotes`：一个二维序列（如列表或数组），每一行包含时间戳、开盘价、收盘价、最高价、最低价。
*   `width`：设置K线实体的宽度。
*   `colorup`：设置阳线（收盘价 >= 开盘价）的颜色。
*   `colordown`：设置阴线（收盘价 < 开盘价）的颜色。

![](img/f8c82f8b0552238711c90a67a82ae831_20.png)

## 数据准备：时间戳转换
`candlestick_ochl`函数要求时间数据必须是浮点数格式的时间戳，而不是Python的`datetime`对象。因此，我们需要使用`matplotlib.dates.date2num`函数进行转换。

假设我们有一个名为`df`的Pandas DataFrame，其索引是日期，并包含`open`， `close`， `high`， `low`等列。

![](img/f8c82f8b0552238711c90a67a82ae831_22.png)

**核心代码：数据转换**
```python
# 将日期索引转换为Python datetime对象列表
dates = df.index.to_pydatetime()
# 使用date2num将datetime对象转换为浮点数时间戳
time_stamps = mdates.date2num(dates)
# 将时间戳添加为新列
df[‘time’] = time_stamps
```

![](img/f8c82f8b0552238711c90a67a82ae831_23.png)

![](img/f8c82f8b0552238711c90a67a82ae831_25.png)

![](img/f8c82f8b0552238711c90a67a82ae831_27.png)

## 完整绘图步骤
以下是使用Matplotlib绘制K线图的完整流程。

首先，创建图形和坐标轴对象。
```python
fig = plt.figure(figsize=(12, 6))
ax = fig.add_subplot(1, 1, 1)
```

![](img/f8c82f8b0552238711c90a67a82ae831_29.png)

![](img/f8c82f8b0552238711c90a67a82ae831_31.png)

接着，准备符合`candlestick_ochl`函数要求的数据序列。必须确保列的顺序为：`time`， `open`， `close`， `high`， `low`。

![](img/f8c82f8b0552238711c90a67a82ae831_33.png)

**核心代码：准备数据序列**
```python
# 使用花式索引确保列顺序正确，并转换为数值数组
data_for_plot = df[[‘time’， ‘open’， ‘close’， ‘high’， ‘low’]].values
```

![](img/f8c82f8b0552238711c90a67a82ae831_35.png)

![](img/f8c82f8b0552238711c90a67a82ae831_37.png)

![](img/f8c82f8b0552238711c90a67a82ae831_39.png)

然后，调用`candlestick_ochl`函数进行绘制。
```python
fn.candlestick_ochl(ax=ax，
                     quotes=data_for_plot，
                     width=0.6，
                     colorup=‘red’，   # 阳线为红色
                     colordown=‘green’) # 阴线为绿色
```

![](img/f8c82f8b0552238711c90a67a82ae831_41.png)

![](img/f8c82f8b0552238711c90a67a82ae831_43.png)

![](img/f8c82f8b0552238711c90a67a82ae831_45.png)

![](img/f8c82f8b0552238711c90a67a82ae831_46.png)

![](img/f8c82f8b0552238711c90a67a82ae831_48.png)

最后，可以添加网格并显示图形。
```python
plt.grid(True)
plt.show()
```

![](img/f8c82f8b0552238711c90a67a82ae831_50.png)

![](img/f8c82f8b0552238711c90a67a82ae831_51.png)

![](img/f8c82f8b0552238711c90a67a82ae831_52.png)

## 性能优化与局部显示
如果数据量很大（例如多年的日线数据），绘图和显示可能会比较慢。为了提高效率，可以只绘制数据的一个子集。

![](img/f8c82f8b0552238711c90a67a82ae831_54.png)

![](img/f8c82f8b0552238711c90a67a82ae831_56.png)

以下是截取前100行数据进行绘制的示例。
```python
# 截取DataFrame的前100行
df_subset = df.iloc[:100]
# ... 然后对df_subset重复上述数据转换和绘图步骤 ...
```

![](img/f8c82f8b0552238711c90a67a82ae831_58.png)

![](img/f8c82f8b0552238711c90a67a82ae831_60.png)

## 总结
本节课中我们一起学习了如何使用Matplotlib绘制金融K线图。我们首先回顾了K线图的基本构成，然后介绍了`matplotlib.finance`子模块及其核心函数`candlestick_ochl`。关键步骤包括将日期数据转换为浮点时间戳、正确排列数据列顺序以及调用绘图函数。掌握K线图的绘制是进行金融数据可视化分析的重要基础。在接下来的课程中，我们将结合这些可视化工具进行更深入的金融分析实战。