# 量化交易教程：P33：35 pandas与Matplotlib

## 概述
在本节课中，我们将学习如何将pandas库中的`DataFrame`和`Series`数据对象，与Matplotlib绘图库结合起来，直接生成图表。这是一种非常高效的数据可视化方法。

---

![](img/3b1fb4dc2b0567aee487a34f566cc9ff_1.png)

## pandas与Matplotlib的集成

上一节我们介绍了`plot`函数及其相关参数。本节中我们来看看pandas数据结构如何与Matplotlib协同工作。

pandas库内置了基于Matplotlib的绘图功能。这意味着我们可以直接在`DataFrame`或`Series`对象上调用`.plot()`方法来生成图表，而无需手动处理数据与坐标轴的映射关系。

### 从DataFrame直接绘图

以下是具体的操作步骤。我们将使用一个股票数据文件作为示例。

首先，我们读取数据文件并创建一个`DataFrame`。

```python
import pandas as pd

# 读取股票数据文件
df = pd.read_csv('601318.csv')
```

接着，我们需要对数据进行一些预处理。例如，将日期列转换为日期时间对象，并设置为索引。

```python
# 将‘date’列转换为日期时间格式并设为索引
df['date'] = pd.to_datetime(df['date'])
df.set_index('date', inplace=True)
```

这个数据集中可能包含一些非数值列（如股票代码）或无用的序列。我们可以使用花式索引选择我们需要的几列数据，例如四个价格列。

```python
# 选择开盘价、收盘价、最高价、最低价这四列
price_df = df[['open', 'close', 'high', 'low']]
```

现在，我们有了一个只包含价格数据的`DataFrame`。如果我们想将这个`DataFrame`可视化为图表，有一个非常简单的方法。

```python
import matplotlib.pyplot as plt

# 直接在DataFrame上调用.plot()方法
price_df.plot()
plt.show()
```

执行上述代码后，Matplotlib会生成一个图表。图表会智能地将索引列（日期）作为X轴坐标，将选中的四列数据绘制成四条折线，并自动生成图例。因为股票每日的开盘价、收盘价、最高价和最低价数值通常很接近，所以四条线可能会重叠在一起，看起来像一条线。放大图表或在交互式窗口中查看，可以观察到细微的差别。

### 从Series直接绘图

`Series`对象也可以直接绘图。由于`Series`只有一列数据，调用`.plot()`方法将只生成一条线。

![](img/3b1fb4dc2b0567aee487a34f566cc9ff_3.png)

```python
# 假设我们只绘制收盘价这一列
close_series = df['close']
close_series.plot()
plt.show()
```

![](img/3b1fb4dc2b0567aee487a34f566cc9ff_5.png)

---

## 本节小结

本节课中我们一起学习了pandas与Matplotlib集成的核心方法。关键在于，可以直接在`DataFrame`或`Series`对象上调用`.plot()`方法来快速生成图表。Matplotlib会自动处理索引与坐标轴的映射，极大简化了数据可视化的流程。

---

## 课后练习

![](img/3b1fb4dc2b0567aee487a34f566cc9ff_7.png)

为了巩固所学知识，这里有一个小练习供大家尝试。

以下是练习的具体要求：
*   **任务**：在同一个图表窗口中绘制三个数学函数的图像。
*   **函数1**：`y = x` （一条直线）
*   **函数2**：`y = x^2` （一条抛物线）
*   **函数3**：`y = 3*x^3 + 5*x^2 + 2*x + 1` （一个复杂的三次函数）
*   **要求**：
    1.  使用不同颜色的线条区分三个函数。
    2.  为图表添加图例，说明每条线对应的函数。

![](img/3b1fb4dc2b0567aee487a34f566cc9ff_9.png)

请大家尝试独立完成这个练习。在下一个视频中，我们将一起编写代码，你可以对比一下你的实现思路。