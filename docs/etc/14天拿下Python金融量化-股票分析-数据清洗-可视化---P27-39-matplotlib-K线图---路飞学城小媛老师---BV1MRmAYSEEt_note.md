# Python金融量化：P27：matplotlib K线图 📈

在本节课中，我们将学习如何使用matplotlib库中的finance子模块来绘制金融分析中常用的K线图。我们将从导入必要的模块开始，逐步讲解数据准备和绘图步骤，最终生成一个清晰的K线图。

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_1.png)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_3.png)

---

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_4.png)

## 概述

上一节我们介绍了matplotlib绘制折线图、柱状图和饼图的方法。本节中，我们来看看如何绘制金融领域特有的K线图。K线图是展示股票开盘价、收盘价、最高价和最低价的重要工具。

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_6.png)

## 导入必要模块

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_8.png)

首先，我们需要导入matplotlib的finance子模块以及日期处理模块。

```python
import matplotlib.pyplot as plt
import matplotlib.finance as fn
from matplotlib.dates import date2num
```

**注意**：导入`matplotlib.finance`时可能会收到一个警告，提示该子模块未来将被移除并重构。目前该模块仍可使用，可以忽略此警告。

## 准备数据

K线图需要时间序列以及对应的开盘价、收盘价、最高价和最低价数据。假设我们有一个名为`df`的DataFrame，其索引为日期，并包含`open`、`close`、`high`、`low`这几列。

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_10.png)

我们需要新增一列，将日期索引转换为`date2num`函数所需的小数时间戳格式。

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_12.png)

以下是数据准备的步骤：

1.  将DataFrame的日期索引转换为Python的datetime对象列表。
2.  使用`date2num`函数将该列表转换为浮点数时间戳。
3.  将时间戳作为新列添加到DataFrame中。

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_14.png)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_16.png)

```python
# 假设df是一个包含‘open‘, ‘close‘, ‘high‘, ‘low‘列的DataFrame，索引为日期
df[‘time‘] = date2num(df.index.to_pydatetime())
```

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_18.png)

## 理解candlestick函数

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_20.png)

`matplotlib.finance`模块提供了`candlestick_ochl`和`candlestick_ohlc`两个函数来绘制K线图。它们的区别在于输入数据的列顺序：

*   `candlestick_ochl`: 要求数据顺序为 **开盘价(Open), 收盘价(Close), 最高价(High), 最低价(Low)**。
*   `candlestick_ohlc`: 要求数据顺序为 **开盘价(Open), 最高价(High), 最低价(Low), 收盘价(Close)**。

我们使用`candlestick_ochl`函数。它的主要参数如下：
*   `ax`: 用于绘图的坐标轴对象。
*   `quotes`: 一个序列（如列表或数组），其中每个元素是一个包含时间、开盘、收盘、最高、最低价格的序列。
*   `width`: K线实体的宽度。
*   `colorup`: 当收盘价 >= 开盘价（阳线）时实体的颜色。
*   `colordown`: 当收盘价 < 开盘价（阴线）时实体的颜色。

## 创建图形与坐标轴

在绘图前，需要先创建一个图形（Figure）和一个坐标轴（Axes）对象。

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_22.png)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_23.png)

```python
fig = plt.figure(figsize=(10, 6))
ax = fig.add_subplot(1, 1, 1)  # 1行1列的第1个子图
```

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_25.png)

## 提取并整理数据

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_27.png)

我们需要从DataFrame中提取出符合函数要求顺序的数据，并转换为数组。

以下是关键步骤：

1.  使用花式索引确保列的顺序为：`[‘time‘, ‘open‘, ‘close‘, ‘high‘, ‘low‘]`。
2.  使用`.values`属性将DataFrame切片转换为NumPy数组。

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_29.png)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_31.png)

```python
# 按所需顺序提取列并转换为数组
data_array = df[[‘time‘, ‘open‘, ‘close‘, ‘high‘, ‘low‘]].values
```

## 绘制K线图

现在，我们可以将准备好的坐标轴对象和数据数组传入`candlestick_ochl`函数进行绘图。

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_33.png)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_35.png)

```python
fn.candlestick_ochl(ax=ax,
                     quotes=data_array,
                     width=0.6,
                     colorup=‘red‘,   # 阳线颜色，例如红色
                     colordown=‘green‘) # 阴线颜色，例如绿色

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_37.png)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_39.png)

# 添加网格以便观察
ax.grid(True)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_41.png)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_43.png)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_44.png)

# 显示图形
plt.show()
```

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_46.png)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_48.png)

## 优化显示（可选）

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_49.png)

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_50.png)

如果数据量过大导致图形显示缓慢或不清，可以截取部分数据进行绘制。

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_52.png)

```python
# 例如，只绘制前100天的数据
df_small = df.iloc[:100]
# 然后对df_small重复上述数据准备和绘图步骤
```

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_54.png)

---

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_56.png)

## 总结

![](img/2ccb1b5ce88f4737f8165bd9fc48ee3f_58.png)

本节课中我们一起学习了如何使用matplotlib绘制K线图。核心步骤包括：导入`finance`模块、准备包含时间戳的数据、创建图形坐标轴、按正确顺序整理数据数组，最后调用`candlestick_ochl`函数完成绘图。掌握K线图的绘制是进行金融数据可视化分析的基础技能。