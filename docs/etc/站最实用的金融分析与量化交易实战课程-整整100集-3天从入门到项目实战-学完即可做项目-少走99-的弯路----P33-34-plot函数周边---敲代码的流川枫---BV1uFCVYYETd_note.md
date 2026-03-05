# 量化交易实战课程：P33：plot函数周边 📊

在本节课中，我们将深入学习Matplotlib库中`plot`函数的周边功能，包括如何在同一图表中绘制多条曲线，以及如何设置图表的标题、坐标轴标签、刻度和图例等元素，使图表更加清晰和专业。

上一节我们介绍了`plot`函数的基本用法，本节中我们来看看如何在同一图表中绘制多条曲线。

## 绘制多条曲线

![](img/1006484581a32e19c34db9bc6f441add_1.png)

在单个图表中绘制多条曲线，只需多次调用`plot`函数即可。Matplotlib库会将所有在调用`plt.show()`之前执行的`plot`函数绘制到同一个图表中。

![](img/1006484581a32e19c34db9bc6f441add_3.png)

以下是绘制多条曲线的示例代码：
```python
import matplotlib.pyplot as plt

![](img/1006484581a32e19c34db9bc6f441add_5.png)

![](img/1006484581a32e19c34db9bc6f441add_7.png)

# 绘制第一条曲线
plt.plot([1, 2, 3, 4], [1, 4, 9, 16], 'o-', label='Line A')
# 绘制第二条曲线
plt.plot([1, 2, 3, 4], [2, 5, 10, 17], 'o-', label='Line B')

![](img/1006484581a32e19c34db9bc6f441add_9.png)

plt.show()
```
执行上述代码，图表中将显示两条曲线。每增加一个`plot`调用，图表中就会增加一条对应的曲线。

![](img/1006484581a32e19c34db9bc6f441add_11.png)

![](img/1006484581a32e19c34db9bc6f441add_13.png)

## 设置图表标题与坐标轴标签

一个完整的图表通常包含标题、X轴标签和Y轴标签，用于说明图表内容和坐标轴含义。

以下是设置这些属性的函数：
*   **`plt.title()`**: 设置图表的标题。
*   **`plt.xlabel()`**: 设置X轴的标签。
*   **`plt.ylabel()`**: 设置Y轴的标签。

这些函数需要在`plt.show()`之前调用。例如：
```python
plt.title('Matplotlib Test Plot')
plt.xlabel('X Label')
plt.ylabel('Y Label')
plt.show()
```

## 设置坐标轴范围

![](img/1006484581a32e19c34db9bc6f441add_15.png)

默认情况下，Matplotlib会自动调整坐标轴范围以适应数据。但有时需要手动设置。

![](img/1006484581a32e19c34db9bc6f441add_17.png)

以下是设置坐标轴范围的函数：
*   **`plt.xlim(min, max)`**: 设置X轴显示的最小值和最大值。
*   **`plt.ylim(min, max)`**: 设置Y轴显示的最小值和最大值。

例如，将X轴范围设置为0到5：
```python
plt.xlim(0, 5)
```

## 设置坐标轴刻度

刻度是坐标轴上的标记点。可以自定义刻度的位置和显示的标签。

以下是设置坐标轴刻度的函数：
*   **`plt.xticks(ticks, labels)`**: 设置X轴的刻度位置和标签。
*   **`plt.yticks(ticks, labels)`**: 设置Y轴的刻度位置和标签。

参数`ticks`是一个列表，指定刻度出现的位置。可选的`labels`参数是一个列表，指定对应位置显示的标签文本。

![](img/1006484581a32e19c34db9bc6f441add_19.png)

例如，设置X轴刻度：
```python
import numpy as np
# 设置刻度位置为0, 2, 4, 6, 8, 10
plt.xticks(np.arange(0, 11, 2))
# 或者用自定义标签
plt.xticks([0, 1, 2, 3, 4], ['A', 'B', 'C', 'D', 'E'])
```

![](img/1006484581a32e19c34db9bc6f441add_21.png)

## 添加图例

当图表中有多条曲线时，需要使用图例来说明每条曲线代表的数据系列。

![](img/1006484581a32e19c34db9bc6f441add_23.png)

添加图例最直接的方法是在调用每个`plot`函数时，通过`label`参数指定该曲线的名称，然后调用`plt.legend()`函数显示图例。

![](img/1006484581a32e19c34db9bc6f441add_25.png)

以下是添加图例的示例：
```python
# 绘制曲线时指定label
plt.plot([1, 2, 3, 4], [1, 4, 9, 16], label='Line A')
plt.plot([1, 2, 3, 4], [2, 5, 10, 17], label='Line B')

![](img/1006484581a32e19c34db9bc6f441add_27.png)

# 显示图例
plt.legend()
plt.show()
```
图例会自动根据`label`参数生成。`plt.legend()`函数还有其他高级用法，但为`plot`函数设置`label`参数是最直观和推荐的方式。

本节课中我们一起学习了`plot`函数的周边设置功能。我们掌握了如何在同一图表中绘制多条曲线，以及如何通过`title`、`xlabel`、`ylabel`、`xlim`、`ylim`、`xticks`/`yticks`和`legend`等函数来完善图表的标题、坐标轴、刻度和图例，从而创建出信息完整、清晰易懂的专业图表。