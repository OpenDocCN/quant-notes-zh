# Python金融量化投资分析与股票交易：P38：Matplotlib K线图 📊

![](img/7dbbe6da107498f9b4b805d938d65b0f_1.png)

在本节课中，我们将学习如何使用Matplotlib库绘制金融分析中至关重要的K线图。K线图是展示股票开盘价、收盘价、最高价和最低价的核心工具。

![](img/7dbbe6da107498f9b4b805d938d65b0f_3.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_4.png)

## 概述

上一节我们介绍了Matplotlib绘制折线图、柱状图和饼图的方法。本节中，我们来看看如何绘制金融领域专用的K线图。K线图由实体和上下影线组成，能直观反映价格波动。

![](img/7dbbe6da107498f9b4b805d938d65b0f_6.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_8.png)

## 导入必要模块

首先，我们需要导入Matplotlib的金融子模块。虽然该模块未来可能被重构，但目前功能完整可用。

```python
import matplotlib.pyplot as plt
import matplotlib.finance as fn
import matplotlib.dates as mdates
```

## 准备数据

![](img/7dbbe6da107498f9b4b805d938d65b0f_10.png)

绘制K线图需要时间序列以及开盘价、收盘价、最高价、最低价数据。假设我们已有一个名为`df`的DataFrame，其索引为日期，并包含`open`、`close`、`high`、`low`列。

![](img/7dbbe6da107498f9b4b805d938d65b0f_12.png)

K线图函数要求时间数据为浮点数时间戳格式。因此，我们需要将日期索引转换为所需格式。

![](img/7dbbe6da107498f9b4b805d938d65b0f_14.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_16.png)

以下是转换步骤：

![](img/7dbbe6da107498f9b4b805d938d65b0f_18.png)

```python
# 将日期索引转换为Python datetime对象，再转换为matplotlib所需的浮点数时间戳
df['time'] = mdates.date2num(df.index.to_pydatetime())
```

![](img/7dbbe6da107498f9b4b805d938d65b0f_20.png)

## 创建K线图

Matplotlib的`finance`模块提供了`candlestick_ochl`和`candlestick_ohlc`两个函数，区别在于数据列的排列顺序。我们使用`candlestick_ochl`，它要求的列顺序为：开盘价(open)、收盘价(close)、最高价(high)、最低价(low)。

以下是绘图的核心步骤：

![](img/7dbbe6da107498f9b4b805d938d65b0f_22.png)

1.  创建图形和坐标轴对象。
2.  准备符合顺序要求的数据数组。
3.  调用`candlestick_ochl`函数绘图。

![](img/7dbbe6da107498f9b4b805d938d65b0f_23.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_25.png)

```python
# 1. 创建图形和坐标轴
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)

![](img/7dbbe6da107498f9b4b805d938d65b0f_27.png)

# 2. 按顺序准备数据数组：time, open, close, high, low
data_for_plot = df[['time', 'open', 'close', 'high', 'low']].values

# 3. 绘制K线图
fn.candlestick_ochl(ax, data_for_plot, width=0.6, colorup='r', colordown='g')

![](img/7dbbe6da107498f9b4b805d938d65b0f_29.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_31.png)

# 添加网格并显示图形
plt.grid(True)
plt.show()
```

## 参数说明

![](img/7dbbe6da107498f9b4b805d938d65b0f_33.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_35.png)

以下是`candlestick_ochl`函数主要参数的简要说明：

![](img/7dbbe6da107498f9b4b805d938d65b0f_37.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_39.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_41.png)

*   `ax`: 用于绘图的坐标轴对象。
*   `quotes`: 数据序列，通常是一个N×5的数组，列顺序为：时间、开盘价、收盘价、最高价、最低价。
*   `width`: K线实体的宽度。
*   `colorup`: 当收盘价大于等于开盘价时（阳线），实体的颜色。
*   `colordown`: 当收盘价小于开盘价时（阴线），实体的颜色。

![](img/7dbbe6da107498f9b4b805d938d65b0f_43.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_44.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_46.png)

## 性能优化提示

![](img/7dbbe6da107498f9b4b805d938d65b0f_48.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_49.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_50.png)

如果数据量很大，绘图和显示可能会变慢。为了提高效率，可以先对数据进行切片，只绘制特定时间段的数据。

![](img/7dbbe6da107498f9b4b805d938d65b0f_52.png)

```python
# 例如，只绘制前100行数据
df_slice = df.iloc[:100]
# ... 然后使用df_slice进行上述绘图步骤
```

![](img/7dbbe6da107498f9b4b805d938d65b0f_54.png)

![](img/7dbbe6da107498f9b4b805d938d65b0f_56.png)

## 总结

![](img/7dbbe6da107498f9b4b805d938d65b0f_58.png)

本节课中我们一起学习了如何使用Matplotlib绘制K线图。我们掌握了数据格式的转换方法、`candlestick_ochl`函数的使用，以及如何优化大量数据的绘图性能。K线图是技术分析的基础，熟练掌握其绘制方法对后续的金融数据分析至关重要。