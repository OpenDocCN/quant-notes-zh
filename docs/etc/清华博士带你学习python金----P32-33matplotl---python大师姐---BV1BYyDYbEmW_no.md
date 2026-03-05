# Python金融量化投资分析与股票交易：P32：Matplotlib介绍

## 概述
在本节课中，我们将要学习Python中一个非常重要的数据可视化工具包——Matplotlib。它是金融量化分析和数据分析中用于绘制图表的核心工具。我们将从最基础的绘图开始，逐步介绍其核心功能和使用方法。

## Matplotlib是什么
Matplotlib是一个强大的Python绘图和数据可视化工具包。简而言之，它是用来画图的。在金融分析或数据分析中，我们经常需要将数据（如股票价格）以图表的形式直观展示，而不是仅仅查看表格数据，Matplotlib正是为此而生。

![](img/bc19eefdc3bffadcd896b6284b2ffc42_1.png)

## 安装与引入
Matplotlib的安装方法与其他Python包一样，使用pip命令进行安装。

**安装命令：**
```bash
pip install matplotlib
```

![](img/bc19eefdc3bffadcd896b6284b2ffc42_3.png)

**引入方式：**
在代码中，我们通常引入`matplotlib.pyplot`子模块，并简写为`plt`。
```python
import matplotlib.pyplot as plt
```

![](img/bc19eefdc3bffadcd896b6284b2ffc42_5.png)

![](img/bc19eefdc3bffadcd896b6284b2ffc42_7.png)

![](img/bc19eefdc3bffadcd896b6284b2ffc42_9.png)

## 基础绘图：plot与show
`plt.plot()`函数用于绘制图形，而`plt.show()`函数则用于将绘制的图形展示出来。

**基础代码示例：**
```python
import matplotlib.pyplot as plt
plt.plot()
plt.show()
```
运行以上代码会显示一个空的坐标轴图像，因为`plot`函数没有传入任何数据。

## plot函数的基本用法
`plt.plot()`函数主要用于绘制**折线图**。其基本用法是传入两个参数，分别代表X轴和Y轴的数据。

**示例：绘制一条直线**
```python
import matplotlib.pyplot as plt
# X轴数据：[1, 2, 3, 4]
# Y轴数据：[2, 4, 6, 8]
plt.plot([1, 2, 3, 4], [2, 4, 6, 8])
plt.show()
```
这段代码会将点(1,2)、(2,4)、(3,6)、(4,8)连接起来，形成一条直线。

**示例：绘制一条折线**
```python
import matplotlib.pyplot as plt
plt.plot([1, 2, 3, 4], [2, 3, 1, 4])
plt.show()
```
更改Y轴数据后，点与点之间的连线就不再是直线，而是一条折线。

![](img/bc19eefdc3bffadcd896b6284b2ffc42_11.png)

![](img/bc19eefdc3bffadcd896b6284b2ffc42_13.png)

## 自定义图表样式
`plot`函数可以接受第三个参数，它是一个格式字符串，用于控制折线图的**线型**、**标记点**和**颜色**。

**格式字符串结构：** `[颜色][标记点][线型]`

![](img/bc19eefdc3bffadcd896b6284b2ffc42_15.png)

**示例：绘制带圆点和实线的图表**
```python
import matplotlib.pyplot as plt
# ‘o-’ 表示使用小圆点作为标记，并用实线连接
plt.plot([1, 2, 3, 4], [2, 4, 6, 8], ‘o-‘)
plt.show()
```

![](img/bc19eefdc3bffadcd896b6284b2ffc42_17.png)

以下是各部分的常见选项：

![](img/bc19eefdc3bffadcd896b6284b2ffc42_19.png)

![](img/bc19eefdc3bffadcd896b6284b2ffc42_21.png)

**1. 线型 (linestyle)**
*   `‘-‘`：实线（默认）
*   `‘–‘`：虚线
*   `‘-.’`：点划线
*   `‘:’`：点线

**2. 标记点 (marker)**
*   `‘o’`：小圆点
*   `‘v’`：倒三角形
*   `‘^’`：正三角形
*   `‘s’`：正方形
*   `‘*’`：五角星
*   `‘+’`：加号
*   `‘x’`：叉号
*   `‘d’`：菱形（Diamond）
*   `‘h’`：六边形（Hexagon）

![](img/bc19eefdc3bffadcd896b6284b2ffc42_23.png)

**3. 颜色 (color)**
*   `‘b’`：蓝色
*   `‘g’`：绿色
*   `‘r’`：红色
*   `‘c’`：青色
*   `‘m’`：洋红色
*   `‘y’`：黄色
*   `‘k’`：黑色
*   `‘w’`：白色

![](img/bc19eefdc3bffadcd896b6284b2ffc42_25.png)

**示例：绘制红色虚线带三角形标记的图表**
```python
import matplotlib.pyplot as plt
plt.plot([1, 2, 3, 4], [2, 4, 6, 8], ‘r^–‘)
plt.show()
```

![](img/bc19eefdc3bffadcd896b6284b2ffc42_27.png)

![](img/bc19eefdc3bffadcd896b6284b2ffc42_29.png)

除了使用格式字符串，也可以使用关键字参数分别指定：
```python
import matplotlib.pyplot as plt
plt.plot([1, 2, 3, 4], [2, 4, 6, 8], color=‘red‘, marker=‘^‘, linestyle=‘–‘)
plt.show()
```

![](img/bc19eefdc3bffadcd896b6284b2ffc42_31.png)

## 高级功能：在同一图表中绘制多条线
上一节我们介绍了如何绘制单条折线，本节中我们来看看如何在一个图表中绘制多条线以进行对比。

![](img/bc19eefdc3bffadcd896b6284b2ffc42_33.png)

方法很简单，只需多次调用`plt.plot()`函数即可。Matplotlib会自动为不同的线条分配不同的颜色。

![](img/bc19eefdc3bffadcd896b6284b2ffc42_35.png)

![](img/bc19eefdc3bffadcd896b6284b2ffc42_37.png)

**示例：在同一图表中绘制两条线**
```python
import matplotlib.pyplot as plt
# 第一条线
plt.plot([1, 2, 3, 4], [1, 4, 9, 16], ‘ro-‘) # 红色圆点实线
# 第二条线
plt.plot([1, 2, 3, 4], [2, 5, 10, 17], ‘b^–‘) # 蓝色三角虚线
plt.show()
```

![](img/bc19eefdc3bffadcd896b6284b2ffc42_39.png)

## 总结
本节课中我们一起学习了Matplotlib的基础知识。我们了解了Matplotlib是一个用于数据可视化的绘图库，学会了如何使用`plt.plot()`和`plt.show()`函数绘制基础的折线图，掌握了通过格式字符串或关键字参数自定义线条样式（颜色、标记、线型）的方法，最后还学习了如何在同一张图表中绘制多条折线。这些是使用Matplotlib进行数据可视化的核心基础。