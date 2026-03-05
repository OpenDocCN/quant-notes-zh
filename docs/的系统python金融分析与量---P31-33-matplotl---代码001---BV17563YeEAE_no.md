# 量化交易教程：P31：Matplotlib介绍

## 概述
在本节课中，我们将要学习Python金融分析与量化交易中第三个核心工具包——**Matplotlib**。这是一个用于数据可视化的强大工具包，能够帮助我们绘制各种图表，使数据分析结果更加直观。

---

## 什么是Matplotlib？
Matplotlib是一个强大的Python绘图和数据可视化工具包。简而言之，它是用来画图的。在金融分析或数据分析中，我们通常需要查看图表，例如股票价格走势图，这比直接看数据表格更直观。

![](img/706f3f653767feca4505d4adee522ad3_1.png)

## 安装与引入
Matplotlib的安装方法依然是使用pip进行安装。

```bash
pip install matplotlib
```

在代码中，我们通常这样引入Matplotlib的绘图模块：

![](img/706f3f653767feca4505d4adee522ad3_3.png)

```python
import matplotlib.pyplot as plt
```

![](img/706f3f653767feca4505d4adee522ad3_5.png)

![](img/706f3f653767feca4505d4adee522ad3_7.png)

## 基础绘图：折线图
`plt.plot()`函数用于绘制折线图，`plt.show()`函数用于展示绘制的图像。

![](img/706f3f653767feca4505d4adee522ad3_9.png)

如果`plot`函数不传入任何参数，运行后将显示一个空的坐标轴。

```python
import matplotlib.pyplot as plt
plt.plot()
plt.show()
```

### 绘制简单折线
`plot`函数最基本的用法是传入两个参数：X轴坐标列表和Y轴坐标列表。它会将这些点依次连接起来形成折线。

```python
plt.plot([1, 2, 3, 4], [2, 4, 6, 8])
plt.show()
```
这段代码会绘制一条直线，连接点(1,2)、(2,4)、(3,6)、(4,8)。

如果Y值不是线性变化的，就会形成折线。

```python
plt.plot([1, 2, 3, 4], [2, 3, 5, 7])
plt.show()
```

![](img/706f3f653767feca4505d4adee522ad3_11.png)

![](img/706f3f653767feca4505d4adee522ad3_13.png)

### 自定义线条样式
`plot`函数可以接受第三个参数，一个格式字符串，用于控制线条的**线型**、**标记点**和**颜色**。

格式字符串的语法通常为：`‘[颜色][标记点][线型]’`。

以下是几个例子：
*   `‘o’`：只绘制圆点，不连线。
*   `‘-o’`：绘制实线并将数据点标记为圆点。
*   `‘–r’`：绘制红色虚线。
*   `‘g^:’`：绘制绿色点线，并将数据点标记为朝上的三角形。

![](img/706f3f653767feca4505d4adee522ad3_15.png)

颜色、标记点和线型都有各自的简写代码。例如：
*   **颜色**：`‘b’`（蓝色）、`‘g’`（绿色）、`‘r’`（红色）、`‘k’`（黑色）。
*   **标记点**：`‘o’`（圆点）、`‘^’`（上三角）、`‘v’`（下三角）、`‘*’`（星号）、`‘+’`（加号）、`‘x’`（叉号）、`‘d’`（菱形）。
*   **线型**：`‘-’`（实线）、`‘–’`（虚线）、`‘:’`（点线）。

![](img/706f3f653767feca4505d4adee522ad3_17.png)

你也可以使用关键字参数分别指定，这样代码可读性更高：

![](img/706f3f653767feca4505d4adee522ad3_19.png)

```python
plt.plot([1, 2, 3, 4], [2, 4, 6, 8], color=‘red’, marker=‘o’, linestyle=‘–’)
plt.show()
```

![](img/706f3f653767feca4505d4adee522ad3_21.png)

---

## 高级功能
上一节我们介绍了如何绘制一条基本的折线图。本节中我们来看看一些更复杂的用法。

![](img/706f3f653767feca4505d4adee522ad3_23.png)

### 在同一图中绘制多条线
只需多次调用`plt.plot()`函数即可在同一坐标系中绘制多条线。Matplotlib会自动为它们分配不同的颜色。

![](img/706f3f653767feca4505d4adee522ad3_25.png)

```python
import matplotlib.pyplot as plt
import numpy as np

![](img/706f3f653767feca4505d4adee522ad3_27.png)

x = np.arange(0, 10, 0.1) # 生成0到10，间隔0.1的数据点
y1 = np.sin(x)
y2 = np.cos(x)

![](img/706f3f653767feca4505d4adee522ad3_29.png)

plt.plot(x, y1, ‘-b’, label=‘sin(x)’) # 蓝色实线，标签为‘sin(x)’
plt.plot(x, y2, ‘–r’, label=‘cos(x)’) # 红色虚线，标签为‘cos(x)’
plt.legend() # 显示图例
plt.show()
```

![](img/706f3f653767feca4505d4adee522ad3_31.png)

### 图表的基本设置
Matplotlib允许我们对图表的各个部分进行详细设置，使其更符合出版或报告要求。

![](img/706f3f653767feca4505d4adee522ad3_33.png)

以下是几个常用的设置函数：
*   `plt.title(‘标题’)`：设置图表标题。
*   `plt.xlabel(‘X轴标签’)`：设置X轴标签。
*   `plt.ylabel(‘Y轴标签’)`：设置Y轴标签。
*   `plt.xlim([xmin, xmax])`：设置X轴显示范围。
*   `plt.ylim([ymin, ymax])`：设置Y轴显示范围。
*   `plt.grid(True)`：显示网格线。

![](img/706f3f653767feca4505d4adee522ad3_35.png)

```python
plt.plot([1, 2, 3, 4], [1, 4, 9, 16])
plt.title(‘示例图表’)
plt.xlabel(‘X轴’)
plt.ylabel(‘Y轴’)
plt.grid(True)
plt.show()
```

![](img/706f3f653767feca4505d4adee522ad3_37.png)

---

![](img/706f3f653767feca4505d4adee522ad3_39.png)

## 总结
本节课中我们一起学习了Matplotlib的基础知识。我们了解了Matplotlib是一个用于数据可视化的Python工具包，掌握了如何使用`plt.plot()`函数绘制基本的折线图，包括如何传入数据、自定义线条的样式（颜色、标记、线型）。最后，我们还初步接触了在同一图中绘制多条线以及设置图表标题、坐标轴等高级功能。掌握这些基础是后续进行复杂金融数据图表分析的第一步。