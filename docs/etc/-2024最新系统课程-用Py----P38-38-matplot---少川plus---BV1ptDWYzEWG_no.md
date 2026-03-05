# Python金融量化分析：P38：使用Matplotlib绘制K线图 📈

在本节课中，我们将学习如何使用Matplotlib库中的`finance`子模块来绘制金融分析中常用的K线图。我们将从导入必要的模块开始，逐步讲解如何准备数据、调用函数并最终生成K线图。

![](img/8b7fca65ee635567b0036901ac5cb671_1.png)

![](img/8b7fca65ee635567b0036901ac5cb671_3.png)

![](img/8b7fca65ee635567b0036901ac5cb671_4.png)

## 概述

上一节我们介绍了Matplotlib绘制折线图、柱状图和饼图的方法。本节中，我们来看看如何绘制金融领域特有的K线图。K线图由实体和上下影线组成，用于展示股票等金融产品的开盘价、收盘价、最高价和最低价。

![](img/8b7fca65ee635567b0036901ac5cb671_6.png)

## 导入必要模块

![](img/8b7fca65ee635567b0036901ac5cb671_8.png)

首先，我们需要导入Matplotlib的`finance`子模块和`dates`模块。`finance`模块提供了绘制金融图表的功能，而`dates`模块用于处理时间数据的转换。

```python
import matplotlib.pyplot as plt
import matplotlib.finance as fn
from matplotlib.dates import date2num
```

**注意**：导入`matplotlib.finance`时可能会收到一个警告，提示该子模块未来将被移除并重构。目前该模块仍可使用，可以暂时忽略此警告。

## 准备数据

![](img/8b7fca65ee635567b0036901ac5cb671_10.png)

K线图函数需要特定的数据格式。数据应是一个序列，包含时间、开盘价、收盘价、最高价和最低价。时间数据需要转换为浮点数格式的时间戳。

![](img/8b7fca65ee635567b0036901ac5cb671_12.png)

假设我们有一个名为`df`的DataFrame，其中包含`open`、`close`、`high`、`low`列，并且日期是索引。

![](img/8b7fca65ee635567b0036901ac5cb671_14.png)

![](img/8b7fca65ee635567b0036901ac5cb671_16.png)

以下是准备数据的步骤：

![](img/8b7fca65ee635567b0036901ac5cb671_18.png)

1.  将日期索引转换为Python的`datetime`对象列表。
2.  使用`date2num`函数将`datetime`对象列表转换为浮点数时间戳。
3.  将时间戳作为新列添加到DataFrame中。
4.  按`[时间, 开盘价, 收盘价, 最高价, 最低价]`的顺序提取数据并转换为数组。

![](img/8b7fca65ee635567b0036901ac5cb671_20.png)

以下是实现代码：

```python
# 假设df是一个包含‘open‘, ‘close‘, ‘high‘, ‘low‘列的DataFrame，日期是索引
# 1. 将索引转换为datetime对象
datetime_list = df.index.to_pydatetime()
# 2. 将datetime对象转换为浮点数时间戳，并添加为新列‘time‘
df[‘time‘] = date2num(datetime_list)
# 3. 按正确顺序提取数据并转换为数组
data_array = df[[‘time‘, ‘open‘, ‘close‘, ‘high‘, ‘low‘]].values
```

## 创建图形对象

与之前直接使用`plt.plot()`不同，绘制K线图需要先创建一个图形（`figure`）和坐标轴（`axes`）对象，并将坐标轴对象传递给绘图函数。

![](img/8b7fca65ee635567b0036901ac5cb671_22.png)

![](img/8b7fca65ee635567b0036901ac5cb671_23.png)

```python
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)  # 创建一个1行1列第1个子图
```

![](img/8b7fca65ee635567b0036901ac5cb671_25.png)

![](img/8b7fca65ee635567b0036901ac5cb671_27.png)

## 绘制K线图

`matplotlib.finance`模块提供了两个主要的K线图函数：`candlestick_ochl`和`candlestick_ohlc`。它们的区别在于数据列的顺序：
*   `candlestick_ochl`: 顺序为 **开盘价(Open)**，**收盘价(Close)**，**最高价(High)**，**最低价(Low)**。
*   `candlestick_ohlc`: 顺序为 **开盘价(Open)**，**最高价(High)**，**最低价(Low)**，**收盘价(Close)**。

我们需要根据准备的数据顺序选择合适的函数。我们之前准备的数据顺序是`[时间, 开盘价, 收盘价, 最高价, 最低价]`，因此使用`candlestick_ochl`函数。

![](img/8b7fca65ee635567b0036901ac5cb671_29.png)

![](img/8b7fca65ee635567b0036901ac5cb671_31.png)

以下是绘图代码：

```python
# 使用candlestick_ochl函数绘图
fn.candlestick_ochl(ax=ax,
                     quotes=data_array,
                     width=0.6,  # 设置K线宽度
                     colorup=‘r‘,  # 设置阳线（收盘价>=开盘价）颜色为红色
                     colordown=‘g‘)  # 设置阴线颜色为绿色

![](img/8b7fca65ee635567b0036901ac5cb671_33.png)

# 添加网格
plt.grid(True)
# 显示图形
plt.show()
```

![](img/8b7fca65ee635567b0036901ac5cb671_35.png)

![](img/8b7fca65ee635567b0036901ac5cb671_37.png)

**函数参数说明**：
*   `ax`: 之前创建的坐标轴对象，用于指定绘图位置。
*   `quotes`: 包含时间、开盘、收盘、最高、最低价的数据序列（数组）。
*   `width`: K线实体的宽度。
*   `colorup`: 阳线（收盘价大于等于开盘价）的实体颜色。
*   `colordown`: 阴线（收盘价小于开盘价）的实体颜色。

![](img/8b7fca65ee635567b0036901ac5cb671_39.png)

![](img/8b7fca65ee635567b0036901ac5cb671_41.png)

![](img/8b7fca65ee635567b0036901ac5cb671_43.png)

## 优化显示

![](img/8b7fca65ee635567b0036901ac5cb671_44.png)

![](img/8b7fca65ee635567b0036901ac5cb671_46.png)

![](img/8b7fca65ee635567b0036901ac5cb671_48.png)

![](img/8b7fca65ee635567b0036901ac5cb671_49.png)

如果数据量过大，图形渲染会变慢且不清晰。为了提高效率和可读性，可以只选取部分数据进行绘制。

![](img/8b7fca65ee635567b0036901ac5cb671_50.png)

例如，只绘制前100行的数据：

![](img/8b7fca65ee635567b0036901ac5cb671_52.png)

```python
# 选取DataFrame的前100行
df_small = df.iloc[:100]
# 然后使用df_small重复上述数据准备和绘图步骤
```

![](img/8b7fca65ee635567b0036901ac5cb671_54.png)

![](img/8b7fca65ee635567b0036901ac5cb671_56.png)

## 总结

![](img/8b7fca65ee635567b0036901ac5cb671_58.png)

本节课中我们一起学习了如何使用Matplotlib绘制K线图。核心步骤包括：导入`finance`和`dates`模块、将日期数据转换为浮点数时间戳、创建图形和坐标轴对象、调用`candlestick_ochl`或`candlestick_ohlc`函数并传入处理好的数据。通过本课的学习，你已经掌握了将金融时间序列数据可视化为标准K线图的方法，这是进行股票技术分析的基础。接下来，我们将结合这些可视化工具进行更深入的金融数据分析实战。