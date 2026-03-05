# 量化交易：Python入门之数据分析：4-2. Python数据分析：折线图与柱状图 📊

![](img/1eae6142e0d30998e5fb650c8396bd01_0.png)

在本节课中，我们将学习如何使用Python的Matplotlib库绘制更复杂的折线图和柱状图。我们将重点掌握如何在两条线之间填充颜色、如何设置时间格式的横坐标，以及如何绘制并排、堆叠和横向的柱状图。

---

![](img/1eae6142e0d30998e5fb650c8396bd01_2.png)

## 在折线之间填充颜色 🎨

![](img/1eae6142e0d30998e5fb650c8396bd01_4.png)

上一节我们介绍了如何绘制折线图。本节中，我们来看看如何在两条折线之间填充颜色，以直观地展示数据差异。

核心操作是使用 `plt.gca().fill_between()` 函数。该函数需要指定X轴的范围和两条Y轴的数据。

以下是具体步骤和代码：

![](img/1eae6142e0d30998e5fb650c8396bd01_6.png)

```python
import matplotlib.pyplot as plt
import numpy as np

![](img/1eae6142e0d30998e5fb650c8396bd01_8.png)

# 生成示例数据
x = np.arange(0, 10, 1)
linear_data = x
exponential_data = x ** 2

![](img/1eae6142e0d30998e5fb650c8396bd01_10.png)

![](img/1eae6142e0d30998e5fb650c8396bd01_12.png)

# 绘制两条折线
plt.plot(x, linear_data, label='Linear')
plt.plot(x, exponential_data, label='Exponential')
plt.legend()

# 在两条线之间填充颜色
ax = plt.gca()
ax.fill_between(x, linear_data, exponential_data, facecolor='red', alpha=0.5)

![](img/1eae6142e0d30998e5fb650c8396bd01_14.png)

plt.show()
```

![](img/1eae6142e0d30998e5fb650c8396bd01_16.png)

**代码解释**：
*   `plt.gca()` 获取当前坐标轴。
*   `fill_between()` 函数在 `linear_data` 和 `exponential_data` 之间填充颜色。
*   `facecolor` 参数设置填充颜色。
*   `alpha` 参数控制透明度，范围是0（完全透明）到1（完全不透明）。

---

## 设置时间格式的横坐标 ⏰

![](img/1eae6142e0d30998e5fb650c8396bd01_18.png)

在金融数据分析中，横坐标通常是日期。接下来，我们学习如何将横坐标设置为时间格式，并解决日期标签重叠的问题。

![](img/1eae6142e0d30998e5fb650c8396bd01_20.png)

以下是实现步骤：

![](img/1eae6142e0d30998e5fb650c8396bd01_21.png)

1.  **生成日期序列**：使用 `numpy` 或 `pandas` 生成日期数据。
2.  **绘制图表**：使用日期序列作为X轴数据。
3.  **旋转标签**：为避免日期标签重叠，需要旋转X轴刻度标签。
4.  **添加坐标轴标签和标题**：使图表信息更完整。

![](img/1eae6142e0d30998e5fb650c8396bd01_23.png)

![](img/1eae6142e0d30998e5fb650c8396bd01_25.png)

```python
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

# 1. 生成日期序列（字符串格式）
observation_dates = pd.date_range('2024-01-01', '2024-09-01', freq='D').strftime('%Y-%m-%d').tolist()

![](img/1eae6142e0d30998e5fb650c8396bd01_27.png)

![](img/1eae6142e0d30998e5fb650c8396bd01_29.png)

# 生成对应的Y轴数据
linear_data = np.arange(len(observation_dates))
exponential_data = linear_data ** 2

# 2. 绘制折线图
plt.figure(figsize=(12, 6))
plt.plot(observation_dates, linear_data, label='Linear')
plt.plot(observation_dates, exponential_data, label='Exponential')
plt.legend()

# 3. 旋转X轴刻度标签，避免重叠
ax = plt.gca()
for tick in ax.get_xticklabels():
    tick.set_rotation(45)

![](img/1eae6142e0d30998e5fb650c8396bd01_31.png)

# 4. 添加坐标轴标签和标题
ax.set_xlabel('Dates')
ax.set_ylabel('Values')
ax.set_title('Time Series Data')

![](img/1eae6142e0d30998e5fb650c8396bd01_32.png)

plt.tight_layout() # 自动调整布局，防止标签被截断
plt.show()
```

![](img/1eae6142e0d30998e5fb650c8396bd01_34.png)

**核心概念**：
*   `pd.date_range()` 用于生成一个日期范围。
*   `ax.get_xticklabels()` 获取X轴的所有刻度标签对象。
*   `tick.set_rotation(45)` 将每个标签旋转45度。

---

![](img/1eae6142e0d30998e5fb650c8396bd01_36.png)

![](img/1eae6142e0d30998e5fb650c8396bd01_38.png)

## 绘制柱状图 📊

![](img/1eae6142e0d30998e5fb650c8396bd01_39.png)

柱状图常用于比较不同类别的数据。本节我们将学习绘制基本柱状图、并排柱状图、带误差线的柱状图、堆叠柱状图和横向柱状图。

以下是不同类型的柱状图绘制方法：

![](img/1eae6142e0d30998e5fb650c8396bd01_41.png)

### 基本柱状图与并排柱状图

![](img/1eae6142e0d30998e5fb650c8396bd01_43.png)

使用 `plt.bar()` 函数绘制柱状图。通过调整柱子的X坐标位置和宽度，可以实现并排效果。

![](img/1eae6142e0d30998e5fb650c8396bd01_45.png)

```python
import matplotlib.pyplot as plt
import numpy as np

x_values = np.arange(10)
linear_data = x_values
exponential_data = x_values ** 2

![](img/1eae6142e0d30998e5fb650c8396bd01_47.png)

plt.figure()

# 绘制第一组数据（蓝色柱子）
plt.bar(x_values, linear_data, width=0.4, label='Linear', color='blue')

# 绘制第二组数据（红色柱子），通过调整X坐标使其并排
new_x_values = x_values + 0.4 # 将X坐标向右偏移一个柱宽
plt.bar(new_x_values, exponential_data, width=0.4, label='Exponential', color='red')

![](img/1eae6142e0d30998e5fb650c8396bd01_49.png)

plt.legend()
plt.show()
```

![](img/1eae6142e0d30998e5fb650c8396bd01_51.png)

### 为柱状图添加误差线

![](img/1eae6142e0d30998e5fb650c8396bd01_53.png)

在科学研究或统计中，经常需要显示数据的误差范围。

![](img/1eae6142e0d30998e5fb650c8396bd01_55.png)

```python
from random import randint

# 为线性数据生成随机误差值
error_values = [randint(0, 15) for _ in range(len(linear_data))]

![](img/1eae6142e0d30998e5fb650c8396bd01_57.png)

plt.figure()
plt.bar(x_values, linear_data, width=0.4, label='Linear', color='blue', yerr=error_values)
plt.legend()
plt.show()
```

![](img/1eae6142e0d30998e5fb650c8396bd01_59.png)

**代码解释**：
*   `yerr` 参数接受一个列表，用于指定每个柱子的误差值（误差线长度）。

![](img/1eae6142e0d30998e5fb650c8396bd01_61.png)

### 绘制堆叠柱状图

堆叠柱状图用于显示各部分与整体的关系。关键参数是 `bottom`，它指定了当前数据系列的起始高度。

![](img/1eae6142e0d30998e5fb650c8396bd01_63.png)

![](img/1eae6142e0d30998e5fb650c8396bd01_65.png)

```python
plt.figure()

![](img/1eae6142e0d30998e5fb650c8396bd01_66.png)

# 先绘制底层数据（绿色）
plt.bar(x_values, linear_data, width=0.4, label='Linear', color='green')

![](img/1eae6142e0d30998e5fb650c8396bd01_68.png)

# 再绘制上层数据（红色），底部设置为linear_data的值
plt.bar(x_values, exponential_data, width=0.4, label='Exponential', color='red', bottom=linear_data)

![](img/1eae6142e0d30998e5fb650c8396bd01_70.png)

plt.legend()
plt.show()
```

![](img/1eae6142e0d30998e5fb650c8396bd01_72.png)

### 绘制横向柱状图

![](img/1eae6142e0d30998e5fb650c8396bd01_74.png)

横向柱状图适用于类别名称较长的情况。使用 `plt.barh()` 函数，参数从 `width` 变为 `height`，`bottom` 变为 `left`。

![](img/1eae6142e0d30998e5fb650c8396bd01_76.png)

```python
plt.figure()

# 绘制横向柱状图
plt.barh(x_values, linear_data, height=0.4, label='Linear', color='green')
plt.barh(x_values, exponential_data, height=0.4, label='Exponential', color='red', left=linear_data)

![](img/1eae6142e0d30998e5fb650c8396bd01_78.png)

plt.legend()
plt.show()
```

![](img/1eae6142e0d30998e5fb650c8396bd01_80.png)

---

![](img/1eae6142e0d30998e5fb650c8396bd01_82.png)

本节课中我们一起学习了Matplotlib中折线图和柱状图的高级绘制技巧。我们掌握了如何在折线间填充颜色以突出差异，如何将横坐标设置为清晰易读的时间格式，以及如何绘制并排、堆叠、带误差线和横向的柱状图来满足不同的数据展示需求。这些技能是进行金融数据可视化分析的重要基础。