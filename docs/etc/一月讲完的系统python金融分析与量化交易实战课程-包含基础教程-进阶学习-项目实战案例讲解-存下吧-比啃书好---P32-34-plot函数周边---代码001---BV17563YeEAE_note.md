# 量化交易教程：P32：34 plot函数周边 - 代码001 📊

在本节课中，我们将深入学习Matplotlib库中`plot`函数的周边功能，包括如何在同一图表中绘制多条曲线，以及如何设置图表的标题、坐标轴标签、刻度和图例等元素，使图表更加清晰和专业。

上一节我们介绍了`plot`函数的基本用法，本节中我们来看看如何在同一张图上绘制多条曲线。

## 绘制多条曲线

在单个图表中绘制多条曲线非常简单，只需多次调用`plot`函数即可。每次调用都会将一条新的曲线添加到当前图表中。所有曲线将在调用`plt.show()`时一同显示。

![](img/23e960e682dad12f06db06bc916b4c03_1.png)

![](img/23e960e682dad12f06db06bc916b4c03_3.png)

以下是绘制多条曲线的示例代码：

![](img/23e960e682dad12f06db06bc916b4c03_5.png)

```python
import matplotlib.pyplot as plt

![](img/23e960e682dad12f06db06bc916b4c03_7.png)

![](img/23e960e682dad12f06db06bc916b4c03_9.png)

# 第一条曲线
plt.plot([1, 2, 3, 4], [1, 4, 9, 16], 'ro-', label='Line A')
# 第二条曲线
plt.plot([1, 2, 3, 4], [2, 5, 10, 17], 'bo--', label='Line B')

plt.show()
```

![](img/23e960e682dad12f06db06bc916b4c03_11.png)

![](img/23e960e682dad12f06db06bc916b4c03_13.png)

## 设置图表标题与坐标轴标签

一个完整的图表通常包含标题和坐标轴标签，用以说明图表内容和坐标轴的含义。

以下是设置标题和标签的函数：
*   `plt.title()`: 设置图表标题。
*   `plt.xlabel()`: 设置X轴标签。
*   `plt.ylabel()`: 设置Y轴标签。

这些函数需要在`plt.show()`之前调用。

```python
plt.title('Matplotlib Plot Test')
plt.xlabel('X Axis Label')
plt.ylabel('Y Axis Label')
plt.show()
```

![](img/23e960e682dad12f06db06bc916b4c03_15.png)

## 设置坐标轴范围

![](img/23e960e682dad12f06db06bc916b4c03_17.png)

默认情况下，Matplotlib会自动调整坐标轴范围以适应数据。但有时我们需要手动设置。

以下是设置坐标轴范围的函数：
*   `plt.xlim()`: 设置X轴显示范围，参数为`(xmin, xmax)`。
*   `plt.ylim()`: 设置Y轴显示范围，参数为`(ymin, ymax)`。

```python
# 设置X轴范围为0到5，Y轴范围为0到20
plt.xlim(0, 5)
plt.ylim(0, 20)
plt.show()
```

## 设置坐标轴刻度

我们可以自定义坐标轴上的刻度位置和标签，这在处理分类数据或特定间隔时非常有用。

以下是设置刻度的函数：
*   `plt.xticks()`: 设置X轴刻度。
*   `plt.yticks()`: 设置Y轴刻度。

该函数可以接受两个参数：第一个是刻度位置列表，第二个是可选的刻度标签列表。

![](img/23e960e682dad12f06db06bc916b4c03_19.png)

![](img/23e960e682dad12f06db06bc916b4c03_21.png)

```python
import numpy as np

# 设置X轴刻度位置为0, 2, 4, 6, 8, 10
plt.xticks(np.arange(0, 11, 2))
# 或者将数字刻度替换为文本标签
plt.xticks([0, 1, 2, 3, 4], ['A', 'B', 'C', 'D', 'E'])
plt.show()
```

## 添加图例

![](img/23e960e682dad12f06db06bc916b4c03_23.png)

当图表中有多条曲线时，需要图例来说明每条曲线代表什么。最推荐的方法是在调用`plot`函数时，通过`label`参数为每条曲线指定一个标签，然后调用`plt.legend()`自动生成图例。

![](img/23e960e682dad12f06db06bc916b4c03_25.png)

```python
# 绘制曲线时指定label
plt.plot([1, 2, 3, 4], [1, 4, 9, 16], label='Line A')
plt.plot([1, 2, 3, 4], [2, 5, 10, 17], label='Line B')

![](img/23e960e682dad12f06db06bc916b4c03_27.png)

# 显示图例
plt.legend()
plt.show()
```

本节课中我们一起学习了`plot`函数的周边设置功能。我们掌握了如何在同一图表中绘制多条曲线，以及如何通过`title`、`xlabel`、`ylabel`、`xlim`、`ylim`、`xticks`和`legend`等函数来完善图表的标题、标签、范围和图例。这些功能是制作清晰、专业图表的基础，在后续的量化分析和可视化中会经常用到。