# 量化交易：P37：Matplotlib 柱状图与饼图

## 概述
在本节课中，我们将学习Matplotlib库中两种常见的图表类型：柱状图和饼图。我们将了解它们的基本绘制方法、常用参数以及如何自定义图表样式。

![](img/3e986b919ecaba51b619a4d47a91aeb8_1.png)

![](img/3e986b919ecaba51b619a4d47a91aeb8_3.png)

## 从折线图到其他图表
上一节我们重点介绍了`plt.plot()`函数用于绘制折线图。实际上，Matplotlib支持绘制多种类型的图表，例如象形图、条形图、横向条形图、极坐标图、饼图、功率谱密度图、谱图、XY相关性函数图、散点图和直方图等。其中，散点图和直方图在某些研究场景中也会用到。本节我们将讲解两个最常见且实用的图表：条形图（柱状图）和饼图。

## 绘制柱状图
柱状图使用`plt.bar()`函数绘制。其核心参数是柱子的位置和高度。

以下是绘制基础柱状图的步骤：
1.  准备数据：定义柱子的位置（通常是一个整数序列）和对应的高度值。
2.  调用`plt.bar()`函数。
3.  自定义坐标轴标签和样式。

**代码示例：绘制四个月份销售额的柱状图**
```python
import matplotlib.pyplot as plt
import numpy as np

# 数据：四个月份的销售额
data = [32, 48, 21, 100]
# 月份标签
labels = ['JAN', 'FEB', 'MAR', 'APR']
# 柱子的位置：0, 1, 2, 3
positions = np.arange(len(data))

# 绘制柱状图
plt.bar(positions, data, color='red', width=0.3)

![](img/3e986b919ecaba51b619a4d47a91aeb8_5.png)

# 设置x轴刻度标签
plt.xticks(positions, labels)

![](img/3e986b919ecaba51b619a4d47a91aeb8_7.png)

# 显示图表
plt.show()
```

![](img/3e986b919ecaba51b619a4d47a91aeb8_9.png)

![](img/3e986b919ecaba51b619a4d47a91aeb8_11.png)

**参数详解**
*   `positions`: 柱子在x轴上的位置，通常使用`np.arange(len(data))`生成。
*   `data`: 每个柱子的高度（y值）。
*   `color`: 设置柱子的颜色。
*   `width`: 设置柱子的宽度，值应小于1以避免柱子重叠。
*   `align`: 控制柱子相对于刻度线的对齐方式，可选`'center'`（默认，居中）或`'edge'`（靠左或靠右对齐）。

![](img/3e986b919ecaba51b619a4d47a91aeb8_13.png)

![](img/3e986b919ecaba51b619a4d47a91aeb8_15.png)

## 绘制饼图
饼图使用`plt.pie()`函数绘制，用于展示各部分占总体的比例。

![](img/3e986b919ecaba51b619a4d47a91aeb8_17.png)

![](img/3e986b919ecaba51b619a4d47a91aeb8_19.png)

以下是绘制基础饼图的步骤：
1.  准备数据：一个包含各部分数值的列表。
2.  调用`plt.pie()`函数。
3.  添加标签、百分比等自定义信息。

![](img/3e986b919ecaba51b619a4d47a91aeb8_21.png)

![](img/3e986b919ecaba51b619a4d47a91aeb8_23.png)

**代码示例：绘制各部分占比的饼图**
```python
import matplotlib.pyplot as plt

# 数据：四个部分的数值
sizes = [10, 20, 30, 40]
# 各部分标签
labels = ['A', 'B', 'C', 'D']

# 绘制饼图
plt.pie(sizes, labels=labels, autopct='%1.1f%%', startangle=90)

# 显示为正圆
plt.axis('equal')

# 显示图表
plt.show()
```

![](img/3e986b919ecaba51b619a4d47a91aeb8_25.png)

![](img/3e986b919ecaba51b619a4d47a91aeb8_27.png)

**参数详解**
*   `sizes`: 各部分大小的列表。
*   `labels`: 对应各部分的标签列表。
*   `autopct`: 用于在饼图切片上显示百分比的格式化字符串。例如`'%1.1f%%'`表示保留一位小数。
*   `explode`: 一个列表，用于指定某些部分从饼图中“突出”显示的距离。例如`[0, 0, 0.1, 0]`会使第三部分突出。
*   `startangle`: 饼图起始的绘制角度（逆时针方向）。设置为90度可使饼图竖直向上。
*   `shadow`: 设置为`True`可为饼图添加阴影效果。

![](img/3e986b919ecaba51b619a4d47a91aeb8_29.png)

![](img/3e986b919ecaba51b619a4d47a91aeb8_31.png)

## 总结
本节课我们一起学习了Matplotlib中柱状图和饼图的绘制方法。我们掌握了`plt.bar()`和`plt.pie()`两个核心函数的基本用法，并了解了如何通过参数调整图表的位置、大小、颜色和标签。虽然Matplotlib支持众多图表类型，但在量化分析和日常数据可视化中，折线图、柱状图和饼图已能满足大部分需求。对于更复杂的图表，可以参考Matplotlib官方文档中的示例进行学习。