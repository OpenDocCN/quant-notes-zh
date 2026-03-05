# Python金融量化投资分析与股票交易：P34：pandas与Matplotlib 📊

在本节课中，我们将学习如何将pandas库中的DataFrame和Series数据结构与Matplotlib结合，直接绘制图表。这是一种非常便捷的数据可视化方法，特别适合处理金融时间序列数据。

上一节我们介绍了Matplotlib绘图函数的一些周边功能。本节中我们来看看pandas库与Matplotlib库之间的直接关联。

## 从DataFrame直接绘图 📈

我们之前创建过DataFrame，有时我们希望将整个DataFrame或其中的数据直接可视化为图表。例如，我们可以使用之前用过的股票数据文件。

以下是读取和处理数据的步骤：
1.  读取股票数据文件（例如 `601318.csv`）。
2.  将日期列转换为日期时间对象，并设置为索引。
3.  使用花式索引选择我们关心的价格列（例如开盘价、收盘价、最高价、最低价）。

核心操作代码如下：
```python
import pandas as pd
import matplotlib.pyplot as plt

# 读取数据
df = pd.read_csv('601318.csv')
# 转换日期并设为索引
df['date'] = pd.to_datetime(df['date'])
df.set_index('date', inplace=True)
# 选择需要的列
price_df = df[['open', 'close', 'high', 'low']]
```

## 一键生成图表 🖼️

如果我想把这个处理好的DataFrame直接绘制成图表，有没有简便的方法？答案是肯定的。

我们可以直接调用DataFrame对象的 `.plot()` 方法，然后使用 `plt.show()` 显示图像。

```python
price_df.plot()
plt.show()
```

执行以上代码后，Matplotlib会很智能地生成一个图表。在这个图表中：
*   **X轴** 自动使用了DataFrame的索引列，即日期时间序列。
*   **Y轴** 显示了所选的四列价格数据，每条线代表一个价格序列。

图表中可能看起来只有一条线，这是因为每日的开盘价、收盘价、最高价和最低价数值非常接近，且数据量很大，导致多条线几乎重叠。放大观察或调整图表窗口，可以看到它们是用不同颜色区分的。

## Series对象的绘图 📉

不仅DataFrame可以这样操作，Series对象同样可以。Series是单列数据，调用 `.plot()` 方法后，绘制的就是一条线。

```python
# 假设我们有一个Series对象，例如收盘价序列
close_series = df['close']
close_series.plot()
plt.show()
```

![](img/6a2f32ab55ae94b2fe41511bb078f060_1.png)

总结来说，pandas与Matplotlib的紧密集成体现在：**可以直接使用 `DataFrame.plot()` 或 `Series.plot()` 方法来快速绘制图表**，这大大简化了数据可视化的流程。

![](img/6a2f32ab55ae94b2fe41511bb078f060_3.png)

---

## 课后练习 ✍️

为了巩固所学知识，这里有一个小练习供大家尝试。

以下是练习的具体要求：
*   **任务**：在同一个图表窗口中绘制三个数学函数图像。
*   **函数**：
    1.  `y = x` （一条直线）
    2.  `y = x^2` （一条抛物线）
    3.  `y = 3*x^3 + 5*x^2 + 2*x + 1` （一个复杂的三次函数）
*   **要求**：
    *   用不同颜色的线区分三个函数。
    *   为图表添加图例（legend），说明每条线对应的函数。

前两个函数图像我们很容易想象，第三个函数则较为复杂。请大家尝试编写代码实现这个可视化任务。

![](img/6a2f32ab55ae94b2fe41511bb078f060_5.png)

完成练习后，可以观看下一个视频，我们将一起编写代码，你可以对比一下你的实现思路与我的有何不同。

本节课中我们一起学习了如何利用pandas与Matplotlib的集成功能，将DataFrame和Series数据快速转换为图表，并布置了一个绘制数学函数图像的练习来加深理解。