# Python金融量化：P34：matplotlib K线图 📈

![](img/65e32f16d051c9654bc1f465a5b87257_1.png)

在本节课中，我们将学习如何使用matplotlib库中的finance子模块来绘制金融分析中常用的K线图。K线图是展示股票开盘价、收盘价、最高价和最低价的重要工具。

![](img/65e32f16d051c9654bc1f465a5b87257_3.png)

![](img/65e32f16d051c9654bc1f465a5b87257_4.png)

## 概述

上一节我们介绍了matplotlib绘制折线图、柱状图和饼图的方法。本节中，我们来看看如何绘制K线图。K线图由实体和上下影线组成，实体部分表示开盘价与收盘价之间的价格区间，影线则表示最高价和最低价。

![](img/65e32f16d051c9654bc1f465a5b87257_6.png)

![](img/65e32f16d051c9654bc1f465a5b87257_8.png)

## 导入必要模块

首先，我们需要导入matplotlib的finance子模块以及日期处理模块。

```python
import matplotlib.pyplot as plt
import matplotlib.finance as fn
from matplotlib.dates import date2num
```

![](img/65e32f16d051c9654bc1f465a5b87257_10.png)

**注意**：`matplotlib.finance`模块未来可能会被移除或重构，但目前仍可使用。如果导入时出现警告，可以暂时忽略。

## 准备数据

![](img/65e32f16d051c9654bc1f465a5b87257_12.png)

![](img/65e32f16d051c9654bc1f465a5b87257_14.png)

绘制K线图需要包含时间、开盘价、收盘价、最高价和最低价的数据。假设我们有一个名为`df`的DataFrame，其索引为日期，并包含`open`、`close`、`high`、`low`等列。

![](img/65e32f16d051c9654bc1f465a5b87257_16.png)

![](img/65e32f16d051c9654bc1f465a5b87257_18.png)

我们需要新增一列，将日期索引转换为`date2num`函数所需的浮点数时间戳格式。

![](img/65e32f16d051c9654bc1f465a5b87257_20.png)

```python
# 将日期索引转换为Python datetime对象，再转换为matplotlib可识别的浮点数
df['time'] = date2num(df.index.to_pydatetime())
```

## 创建绘图对象

与之前直接使用`plt.plot()`不同，绘制K线图需要先创建一个图形（figure）和坐标轴（axes）对象。

```python
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)  # 创建1行1列的第1个子图
```

![](img/65e32f16d051c9654bc1f465a5b87257_22.png)

![](img/65e32f16d051c9654bc1f465a5b87257_23.png)

![](img/65e32f16d051c9654bc1f465a5b87257_25.png)

## 绘制K线图

![](img/65e32f16d051c9654bc1f465a5b87257_27.png)

`matplotlib.finance`模块提供了`candlestick_ochl`和`candlestick_ohlc`两个函数，区别在于数据列的顺序：
*   `ochl`: 顺序为开盘价(open)、收盘价(close)、最高价(high)、最低价(low)
*   `ohlc`: 顺序为开盘价(open)、最高价(high)、最低价(low)、收盘价(close)

我们需要将数据按要求的顺序提取为数组，并传入函数。

![](img/65e32f16d051c9654bc1f465a5b87257_29.png)

![](img/65e32f16d051c9654bc1f465a5b87257_31.png)

```python
# 确保数据列顺序为：time, open, close, high, low
data = df[['time', 'open', 'close', 'high', 'low']].values

# 使用candlestick_ochl函数绘图
fn.candlestick_ochl(ax, data, width=0.6, colorup='r', colordown='g')

![](img/65e32f16d051c9654bc1f465a5b87257_33.png)

![](img/65e32f16d051c9654bc1f465a5b87257_35.png)

# 添加网格并显示图形
plt.grid(True)
plt.show()
```

![](img/65e32f16d051c9654bc1f465a5b87257_37.png)

![](img/65e32f16d051c9654bc1f465a5b87257_39.png)

![](img/65e32f16d051c9654bc1f465a5b87257_41.png)

**参数说明**：
*   `ax`: 之前创建的坐标轴对象。
*   `data`: 包含时间、开盘、收盘、最高、最低价的数据数组。
*   `width`: K线实体的宽度。
*   `colorup`: 收盘价大于等于开盘价（阳线）时的颜色，例如红色`'r'`。
*   `colordown`: 收盘价小于开盘价（阴线）时的颜色，例如绿色`'g'`。

![](img/65e32f16d051c9654bc1f465a5b87257_43.png)

![](img/65e32f16d051c9654bc1f465a5b87257_45.png)

![](img/65e32f16d051c9654bc1f465a5b87257_46.png)

![](img/65e32f16d051c9654bc1f465a5b87257_48.png)

## 优化显示

![](img/65e32f16d051c9654bc1f465a5b87257_50.png)

![](img/65e32f16d051c9654bc1f465a5b87257_51.png)

![](img/65e32f16d051c9654bc1f465a5b87257_52.png)

如果数据量过大导致图形显示缓慢或不清，可以截取部分数据进行绘制。

![](img/65e32f16d051c9654bc1f465a5b87257_54.png)

```python
# 例如，只绘制前100行数据
df_small = df.iloc[:100]
# ... 重复上述数据转换和绘图步骤
```

![](img/65e32f16d051c9654bc1f465a5b87257_56.png)

## 总结

![](img/65e32f16d051c9654bc1f465a5b87257_58.png)

![](img/65e32f16d051c9654bc1f465a5b87257_60.png)

本节课中我们一起学习了如何使用matplotlib绘制K线图。核心步骤包括：导入finance模块、准备并转换时间数据、创建坐标轴对象、调用`candlestick_ochl`或`candlestick_ohlc`函数并传入正确顺序的数据数组。K线图是金融量化分析中可视化价格走势的基础工具。接下来，我们将结合实例，运用所学图表进行基础的金融分析。