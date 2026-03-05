# Python金融量化投资分析与股票交易：P33：plot函数周边 📊

在本节课中，我们将深入学习Matplotlib库中`plot`函数的周边功能。我们将学习如何在一个图表中绘制多条曲线，以及如何为图表添加标题、坐标轴标签、设置坐标轴范围和刻度，并最终添加图例，使图表更加专业和清晰。

上一节我们介绍了`plot`函数的基本用法，本节中我们来看看如何绘制多条曲线以及美化图表。

## 绘制多条曲线

在单个图表中绘制多条曲线非常简单，只需多次调用`plot`函数即可。Matplotlib库会累积所有调用过的`plot`命令，直到调用`show()`函数时，将所有曲线绘制在同一张图上。

![](img/1f62633916ebe856caf94c199fc7e138_1.png)

![](img/1f62633916ebe856caf94c199fc7e138_3.png)

以下是绘制多条曲线的示例代码：

![](img/1f62633916ebe856caf94c199fc7e138_5.png)

```python
import matplotlib.pyplot as plt

![](img/1f62633916ebe856caf94c199fc7e138_7.png)

![](img/1f62633916ebe856caf94c199fc7e138_9.png)

# 绘制第一条曲线
plt.plot([1, 2, 3, 4], [1, 4, 9, 16], 'ro-', label='Line A')
# 绘制第二条曲线
plt.plot([1, 2, 3, 4], [2, 5, 10, 17], 'bo--', label='Line B')

![](img/1f62633916ebe856caf94c199fc7e138_11.png)

plt.show()
```

![](img/1f62633916ebe856caf94c199fc7e138_13.png)

## 设置图表标题与坐标轴标签

一个完整的图表通常包含标题、X轴标签和Y轴标签。Matplotlib提供了相应的方法来设置这些元素。

以下是设置标题和标签的函数：
*   `plt.title()`: 设置图表标题。
*   `plt.xlabel()`: 设置X轴标签。
*   `plt.ylabel()`: 设置Y轴标签。

这些函数需要在调用`plt.show()`之前执行。

```python
plt.title('Matplotlib Plot Test')
plt.xlabel('X Label')
plt.ylabel('Y Label')
plt.show()
```

![](img/1f62633916ebe856caf94c199fc7e138_15.png)

![](img/1f62633916ebe856caf94c199fc7e138_17.png)

## 设置坐标轴范围

默认情况下，Matplotlib会自动调整坐标轴范围以适应数据。但有时我们需要手动设置范围，这可以通过`xlim()`和`ylim()`函数实现。

以下是设置坐标轴范围的函数：
*   `plt.xlim([xmin, xmax])`: 设置X轴显示范围。
*   `plt.ylim([ymin, ymax])`: 设置Y轴显示范围。

```python
# 设置X轴范围为0到5，Y轴范围为0到10
plt.xlim([0, 5])
plt.ylim([0, 10])
plt.show()
```

## 设置坐标轴刻度

我们可以自定义坐标轴上的刻度位置和标签，这对于展示分类数据（如人名、国家名）特别有用。

以下是设置刻度的函数：
*   `plt.xticks(ticks, labels)`: 设置X轴刻度位置和标签。
*   `plt.yticks(ticks, labels)`: 设置Y轴刻度位置和标签。

`ticks`参数是一个列表，指定刻度出现的位置。`labels`参数是一个可选的列表，指定对应位置的标签文本。

![](img/1f62633916ebe856caf94c199fc7e138_19.png)

![](img/1f62633916ebe856caf94c199fc7e138_21.png)

```python
import numpy as np

# 设置X轴刻度为0, 2, 4, 6, 8, 10
plt.xticks(np.arange(0, 11, 2))
# 或者将数字刻度替换为文本标签
plt.xticks([1, 2, 3, 4], ['A', 'B', 'C', 'D'])
plt.show()
```

## 添加图例

![](img/1f62633916ebe856caf94c199fc7e138_23.png)

当图表中有多条曲线时，需要添加图例来说明每条曲线代表什么。最推荐的方法是在调用`plot`函数时，通过`label`参数为每条曲线指定一个标签，然后调用`plt.legend()`函数显示图例。

![](img/1f62633916ebe856caf94c199fc7e138_25.png)

以下是添加图例的步骤：
1.  在`plot()`函数调用中添加`label`参数。
2.  调用`plt.legend()`函数。

![](img/1f62633916ebe856caf94c199fc7e138_27.png)

```python
plt.plot([1, 2, 3, 4], [1, 4, 9, 16], 'ro-', label='Line A')
plt.plot([1, 2, 3, 4], [2, 5, 10, 17], 'bo--', label='Line B')
plt.legend()
plt.show()
```

本节课中我们一起学习了`plot`函数的周边功能。我们掌握了如何在一个图表中绘制多条曲线，以及如何通过设置标题、坐标轴标签、范围和刻度来完善图表，最后还学习了如何添加图例来区分不同的数据系列。这些功能是制作清晰、专业图表的基础，在后续的金融数据可视化中会经常用到。