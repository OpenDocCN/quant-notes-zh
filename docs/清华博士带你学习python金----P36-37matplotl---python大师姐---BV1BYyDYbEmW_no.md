# Python金融量化投资分析：P36：matplotlib 画布与子图 📊

![](img/fb01315875d6e5f21c1270d1cfac95bd_1.png)

在本节课中，我们将要学习如何使用matplotlib在一个窗口中创建并排的多个图表。这对于金融分析非常有用，例如，可以同时展示股票的K线图和大盘的走势图。

![](img/fb01315875d6e5f21c1270d1cfac95bd_3.png)

上一节我们介绍了如何在一个图表中绘制多条线。本节中我们来看看如何在一个画布上创建多个独立的子图。

## 画布与子图的概念

有时我们需要在一个窗口中展示多个图表。例如，分析股票时，可能希望上方显示个股的K线图，下方显示大盘指数图。这需要使用画布和子图功能来实现。

![](img/fb01315875d6e5f21c1270d1cfac95bd_5.png)

![](img/fb01315875d6e5f21c1270d1cfac95bd_7.png)

*   **画布**：可以理解为一个窗口或一张画纸。创建方法为 `fig = plt.figure()`。
*   **子图**：是画布上的一个独立绘图区域。创建方法为 `ax = fig.add_subplot(...)`。

![](img/fb01315875d6e5f21c1270d1cfac95bd_9.png)

创建子图后，后续的绘图操作（如 `plot`）应通过子图对象（如 `ax`）进行，而不是直接使用 `plt`。

## 创建子图的方法

以下是创建子图的核心步骤。

1.  **创建画布**：首先使用 `plt.figure()` 创建一个画布对象。
2.  **添加子图**：使用画布对象的 `add_subplot` 方法添加子图。其参数通常为三个数字，例如 `221`。
    *   第一个数字表示总行数。
    *   第二个数字表示总列数。
    *   第三个数字表示子图的位置序号（从左到右，从上到下计数）。
    例如，`add_subplot(2, 2, 1)` 表示将画布分为2行2列，并在第1个位置创建子图。
3.  **在子图上绘图**：获取子图对象后，调用其绘图方法，如 `ax.plot(...)`。

![](img/fb01315875d6e5f21c1270d1cfac95bd_11.png)

![](img/fb01315875d6e5f21c1270d1cfac95bd_13.png)

```python
import matplotlib.pyplot as plt

# 1. 创建画布
fig = plt.figure()

# 2. 在画布上添加第一个子图 (2行2列中的第1个)
ax1 = fig.add_subplot(2, 2, 1)
# 3. 在第一个子图上绘图
ax1.plot([1, 2, 3, 4, 5, 6, 7, 8])

![](img/fb01315875d6e5f21c1270d1cfac95bd_15.png)

# 4. 添加第二个子图 (2行2列中的第2个)
ax2 = fig.add_subplot(2, 2, 2)
# 可以继续在ax2上绘图...

# 5. 显示画布
fig.show()
# 或使用 plt.show()
```

## 调整子图布局

默认情况下，子图之间会有一定的间距。如果需要调整，可以使用 `subplots_adjust` 函数。

以下是 `subplots_adjust` 的主要参数：
*   `left`, `right`, `bottom`, `top`: 控制画布四周的留白比例。
*   `wspace`: 控制子图之间的宽度间距。
*   `hspace`: 控制子图之间的高度间距。

```python
# 调整子图间距示例
fig.subplots_adjust(left=0.1, bottom=0.1, right=0.9, top=0.9, wspace=0.4, hspace=0.4)
```

![](img/fb01315875d6e5f21c1270d1cfac95bd_17.png)

## 创建上下排列的子图

要实现“上K线，下大盘”的布局，需要创建两行一列的子图。

![](img/fb01315875d6e5f21c1270d1cfac95bd_19.png)

```python
import matplotlib.pyplot as plt

![](img/fb01315875d6e5f21c1270d1cfac95bd_21.png)

fig = plt.figure()

# 创建第一个子图 (2行1列中的第1个)
ax1 = fig.add_subplot(2, 1, 1)
ax1.plot([1, 2, 3, 4, 5])  # 此处模拟绘制K线图

![](img/fb01315875d6e5f21c1270d1cfac95bd_23.png)

# 创建第二个子图 (2行1列中的第2个)
ax2 = fig.add_subplot(2, 1, 2)
ax2.plot([5, 4, 3, 2, 1])  # 此处模拟绘制大盘图

plt.show()
```

本节课中我们一起学习了matplotlib的画布与子图功能。我们掌握了如何通过 `plt.figure()` 和 `fig.add_subplot()` 在一个窗口中创建多个图表，并了解了如何调整子图的布局。这在金融量化分析中用于多图对比展示时非常实用。下一节，我们将介绍matplotlib支持的其他常用图表类型。