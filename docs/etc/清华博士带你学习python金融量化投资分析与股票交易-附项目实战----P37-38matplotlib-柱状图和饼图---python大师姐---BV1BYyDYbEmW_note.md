# Python金融量化投资分析与股票交易：P37：Matplotlib 柱状图和饼图 📊

在本节课中，我们将学习Matplotlib库中除折线图外的两种常用图表：柱状图和饼图。我们将了解它们的基本绘制方法、常用参数以及如何自定义图表样式。

![](img/9c317bf673e7d527ff8b2e515658cdf7_1.png)

上一节我们重点介绍了`plt.plot()`函数用于绘制折线图。实际上，Matplotlib支持绘制多种图表类型，例如象形图、条形图、极坐标图、饼图、功率谱密度图、谱图、相关性函数图、散点图和直方图等。其中，柱状图和饼图是最常见的两种，本节我们将重点学习它们。

![](img/9c317bf673e7d527ff8b2e515658cdf7_3.png)

## 绘制柱状图

柱状图使用`plt.bar()`函数绘制。其核心参数是柱子的位置和高度。

以下是绘制柱状图的基本步骤：

1.  **准备数据**：定义柱子的位置（通常是一个整数序列）和对应的高度（数据值）。
2.  **调用函数**：使用`plt.bar(x, height)`绘制。
3.  **设置坐标轴标签**：使用`plt.xticks()`将位置标签替换为有意义的文本（如月份、类别）。

```python
import matplotlib.pyplot as plt
import numpy as np

# 准备数据：四个柱子的高度
data = [32, 48, 21, 100]
# 准备位置：0, 1, 2, 3
positions = np.arange(len(data))
# 准备标签：四个月份
labels = ['JAN', 'FEB', 'MAR', 'APR']

# 绘制柱状图
plt.bar(positions, data)
# 设置x轴刻度标签
plt.xticks(positions, labels)
plt.show()
```

## 自定义柱状图样式

`plt.bar()`函数提供了多个参数用于自定义图表外观。

![](img/9c317bf673e7d527ff8b2e515658cdf7_5.png)

以下是常用的自定义参数：

![](img/9c317bf673e7d527ff8b2e515658cdf7_7.png)

*   **颜色 (`color`)**：可以设置所有柱子的统一颜色，例如 `color='red'`。
*   **宽度 (`width`)**：控制柱子的宽度，默认值为0.8。可以传入一个小于1的值使柱子变细，例如 `width=0.3`。也可以传入一个列表为每个柱子设置不同宽度。
*   **对齐方式 (`align`)**：控制柱子相对于刻度线的对齐方式。默认是 `'center'`（居中）。可以设置为 `'edge'`，使柱子左边缘或右边缘对齐刻度线。

```python
# 示例：设置颜色、宽度和对齐方式
plt.bar(positions, data, color='skyblue', width=0.5, align='edge')
plt.xticks(positions, labels)
plt.show()
```

![](img/9c317bf673e7d527ff8b2e515658cdf7_9.png)

![](img/9c317bf673e7d527ff8b2e515658cdf7_11.png)

## 绘制饼图

![](img/9c317bf673e7d527ff8b2e515658cdf7_13.png)

![](img/9c317bf673e7d527ff8b2e515658cdf7_15.png)

饼图使用`plt.pie()`函数绘制。其核心参数是各部分的大小。

![](img/9c317bf673e7d527ff8b2e515658cdf7_17.png)

以下是绘制饼图的基本方法：

![](img/9c317bf673e7d527ff8b2e515658cdf7_19.png)

```python
# 准备数据：四个部分的大小
sizes = [10, 20, 30, 40]
# 绘制饼图
plt.pie(sizes)
plt.show()
```

![](img/9c317bf673e7d527ff8b2e515658cdf7_21.png)

![](img/9c317bf673e7d527ff8b2e515658cdf7_23.png)

## 自定义饼图样式

`plt.pie()`函数同样提供了丰富的参数用于美化饼图。

以下是常用的自定义参数：

*   **标签 (`labels`)**：为每个扇形区域添加文本标签。
*   **百分比显示 (`autopct`)**：在每个扇形区域内显示所占百分比。使用格式化字符串，如 `'%1.1f%%'` 表示保留一位小数。
*   **突出显示 (`explode`)**：将某个扇形区域从饼图中“拉出来”以突出显示。参数是一个列表，其中非零值表示对应扇形的突出距离。
*   **等轴比例 (`equal`)**：设置 `plt.axis('equal')` 可以使饼图呈正圆形。

```python
# 准备标签
labels = ['A', 'B', 'C', 'D']
# 设置突出显示（突出第三部分‘C’）
explode = (0, 0, 0.1, 0)

# 绘制饼图
plt.pie(sizes, explode=explode, labels=labels, autopct='%1.1f%%')
# 使饼图为正圆
plt.axis('equal')
plt.show()
```

## 更多学习资源

![](img/9c317bf673e7d527ff8b2e515658cdf7_25.png)

![](img/9c317bf673e7d527ff8b2e515658cdf7_27.png)

Matplotlib功能非常强大，以上只是柱状图和饼图的基础用法。对于更复杂的定制需求，如设置阴影、调整起始角度等，建议查阅官方文档和示例库。

![](img/9c317bf673e7d527ff8b2e515658cdf7_29.png)

Matplotlib官方网站提供了大量的示例代码（Examples），涵盖了几乎所有图表类型和高级用法，是深入学习的最佳途径。

![](img/9c317bf673e7d527ff8b2e515658cdf7_31.png)

本节课中我们一起学习了如何使用Matplotlib绘制柱状图和饼图。我们掌握了它们的基本绘制函数`plt.bar()`和`plt.pie()`，并学习了如何通过参数自定义颜色、标签、百分比显示等样式。虽然Matplotlib支持的图表类型很多，但在量化分析中，折线图、柱状图和饼图已经能够满足大部分基础数据可视化需求。