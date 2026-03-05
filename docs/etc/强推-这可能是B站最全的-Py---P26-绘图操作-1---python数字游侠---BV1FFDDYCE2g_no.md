# Python数据分析：P26：绘图操作~1 📊

在本节课中，我们将学习使用 Matplotlib 模块进行数据可视化绘图。绘图是数据分析中展示结论和趋势的重要手段，能够使汇报效果更加直观。我们将重点学习几种常用二维图形的绘制方法。

## 导入模块

首先，我们需要导入绘图所需的模块。我们将使用 Matplotlib 的 `pyplot` 子模块，并配合 NumPy 生成示例数据。

```python
import matplotlib.pyplot as plt
import numpy as np
```

## 线性图绘制

上一节我们介绍了绘图的基本概念，本节中我们来看看如何绘制线性图。线性图用于展示数据点之间的连续关系。

### 绘制单条线性图

绘制线性图需要两组数据：X轴数据和Y轴数据。使用 `plt.plot()` 方法可以绘制满足线性关系的线段。

```python
x = np.array([1, 2, 3, 4, 5])
y = x + 3
plt.plot(x, y)
plt.show()
```

### 绘制多条线性图

有时我们需要在同一坐标系中比较多条数据线。以下是绘制多条线性图的两种方法。

第一种方法是多次调用 `plt.plot()`。

```python
plt.plot(x, y)
plt.plot(x+1, y-2)
plt.show()
```

第二种方法是在一次 `plt.plot()` 调用中传入多组数据。

```python
plt.plot(x, y, x+1, y-2)
plt.show()
```

### 设置图像比例

我们可以通过 `plt.figure()` 方法在绘图前设置画布尺寸，从而等比例放大或缩小整个图像。

```python
plt.figure(figsize=(5, 9))  # 宽度为5，高度为9
plt.plot(x, y)
plt.show()
```

**注意**：`plt.figure()` 的调用必须放在所有绘图操作之前。

### 设置图例

当有多条线时，我们需要图例来区分它们。可以通过在 `plot()` 方法中添加 `label` 参数，并调用 `plt.legend()` 来显示图例。

```python
plt.plot(x, y, label=‘XY‘)
plt.plot(x+1, y-2, label=‘X+1, Y-2‘)
plt.legend()
plt.show()
```

`plt.legend()` 的 `ncol` 参数可以设置图例的列数。

![](img/bdb437faa938d0260207d5dde6fcc23f_1.png)

![](img/bdb437faa938d0260207d5dde6fcc23f_3.png)

### 设置坐标轴标识和标题

为了让图表含义更清晰，我们可以为坐标轴和整个图表添加标签。

```python
plt.plot(x, y)
plt.xlabel(‘温度‘)
plt.ylabel(‘距离‘)
plt.title(‘距离与温度关系图‘)
plt.show()
```

### 保存图像

绘制好的图像可以保存到本地。需要先创建 `figure` 对象，并在绘图后调用 `savefig()` 方法。

```python
fig = plt.figure()  # 创建对象，需在绘图前
plt.plot(x, y)
fig.savefig(‘./123.png‘)  # 保存图像
plt.show()
```

**注意**：`plt.figure()` 对象的创建必须放在绘图操作之前，否则无法保存。

### 曲线样式和风格

`plot()` 方法支持许多参数来定制线条的样式，例如颜色、透明度、线型和标记点。这些参数可以自行查阅文档进行设置。

```python
plt.plot(x, y, c=‘red‘, alpha=0.5, linewidth=2, linestyle=‘--‘)
plt.show()
```

## 柱状图绘制

接下来我们学习柱状图的绘制。柱状图常用于比较不同类别的数据大小。

使用 `plt.bar()` 方法可以绘制柱状图，同样需要传入X和Y两组数据。

```python
plt.bar(x, y)
plt.xlabel(‘类别‘)
plt.ylabel(‘数值‘)
plt.show()
```

使用 `plt.barh()` 可以绘制横向的柱状图。

## 直方图（密度图）绘制

直方图，也称为密度图，用于统计一组数据中各个数值区间出现的频率。频率越高，柱子越高，表示该区间数据的“密度”越大。

使用 `plt.hist()` 方法绘制直方图。参数 `bins` 用于设置柱子的数量。

```python
data = [1,1,2,2,2,3,4,5,6,6,6,6,6,6,7,8,9,5,0]
plt.hist(data, bins=20)
plt.show()
```

`plt.hist()` 函数会返回两个值：第一个是每个柱子（区间）的高度（频率），第二个是每个区间的边界值。

## 饼图绘制

饼图用于显示各部分占总体的比例。使用 `plt.pie()` 方法绘制。

```python
sizes = [11, 22, 31, 15]
plt.pie(sizes)
plt.show()
```

我们可以添加更多参数来美化饼图，例如添加标签、显示百分比、调整各部分与圆心的距离等。

```python
labels = [‘A‘, ‘B‘, ‘C‘, ‘D‘]
explode = (0, 0.1, 0, 0)  # 突出第二部分
plt.pie(sizes, explode=explode, labels=labels, autopct=‘%1.1f%%‘, labeldistance=0.6)
plt.show()
```

## 散点图绘制

散点图用于展示因变量随自变量变化的大致趋势，图中每个点代表一个数据对。

使用 `plt.scatter()` 方法绘制散点图。

```python
x = np.array([1, 3, 5, 7, 9])
y = x * 2  # 因变量 y = 2x
plt.scatter(x, y)
plt.show()
```

## 总结

![](img/bdb437faa938d0260207d5dde6fcc23f_5.png)

本节课中我们一起学习了使用 Matplotlib 进行数据可视化的基本操作。我们掌握了线性图、柱状图、直方图、饼图和散点图这几种核心二维图形的绘制方法，并学习了如何添加图例、坐标轴标签、标题以及保存图像。Matplotlib 绘制的是静态图，这些技能足以应对数据分析中大部分的图表展示需求。在接下来的实战项目中，我们将综合运用这些绘图技巧。