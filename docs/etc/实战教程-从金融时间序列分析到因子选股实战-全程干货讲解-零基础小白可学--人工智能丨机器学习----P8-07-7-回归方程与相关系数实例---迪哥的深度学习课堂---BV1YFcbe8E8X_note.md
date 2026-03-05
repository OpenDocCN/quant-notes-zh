# Python金融分析与量化交易实战：P8：07-7-回归方程与相关系数实例 📈

![](img/f801af2de834962a69cc09ee21f27d9f_1.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，以分析两个金融指标之间的关系。我们将使用NumPy和Pandas库来完成这些任务，并通过可视化来直观地展示分析结果。

![](img/f801af2de834962a69cc09ee21f27d9f_3.png)

![](img/f801af2de834962a69cc09ee21f27d9f_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

构建回归方程的方法有很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/f801af2de834962a69cc09ee21f27d9f_7.png)

![](img/f801af2de834962a69cc09ee21f27d9f_9.png)

**公式**：`y = kx + b`

![](img/f801af2de834962a69cc09ee21f27d9f_11.png)

![](img/f801af2de834962a69cc09ee21f27d9f_13.png)

以下是使用`np.polyfit`函数计算回归系数的步骤：

![](img/f801af2de834962a69cc09ee21f27d9f_15.png)

![](img/f801af2de834962a69cc09ee21f27d9f_17.png)

1.  导入必要的库。
2.  准备数据，确保数据中不包含空值。
3.  调用`np.polyfit(x, y, deg)`函数，其中`deg`参数指定多项式的阶数。

![](img/f801af2de834962a69cc09ee21f27d9f_19.png)

```python
import numpy as np
import pandas as pd

![](img/f801af2de834962a69cc09ee21f27d9f_21.png)

# 假设 df 是包含数据的DataFrame，且已处理空值
# df.dropna(inplace=True)

![](img/f801af2de834962a69cc09ee21f27d9f_23.png)

# 指定自变量 x 和因变量 y
x = df[‘指标A‘]
y = df[‘指标B‘]

# 计算一阶线性回归的系数 (deg=1)
# 返回的系数中，第一个是斜率k，第二个是截距b
coefficients = np.polyfit(x, y, deg=1)
k, b = coefficients
print(f“回归方程: y = {k:.4f}x + {b:.4f}“)
```

![](img/f801af2de834962a69cc09ee21f27d9f_25.png)

![](img/f801af2de834962a69cc09ee21f27d9f_27.png)

参数`deg`（阶数）决定了回归方程的形式。当`deg=1`时，方程为`y = kx + b`，即包含x的一次项和常数项。如果指定`deg=2`，则方程会包含x的二次项、一次项和常数项，可以拟合曲线。在本例中，我们使用`deg=1`来拟合直线关系。

![](img/f801af2de834962a69cc09ee21f27d9f_29.png)

---

![](img/f801af2de834962a69cc09ee21f27d9f_31.png)

## 可视化回归方程

![](img/f801af2de834962a69cc09ee21f27d9f_33.png)

得到回归系数后，我们可以将回归直线与原始数据的散点图一起绘制出来，以便直观地观察拟合效果。

![](img/f801af2de834962a69cc09ee21f27d9f_35.png)

以下是绘制散点图和回归直线的步骤：

![](img/f801af2de834962a69cc09ee21f27d9f_37.png)

1.  使用Matplotlib绘制原始数据的散点图。
2.  根据回归方程计算对应的y值。
3.  在同一张图上绘制回归直线。

```python
import matplotlib.pyplot as plt

![](img/f801af2de834962a69cc09ee21f27d9f_39.png)

![](img/f801af2de834962a69cc09ee21f27d9f_41.png)

# 绘制散点图
plt.figure(figsize=(16, 9))
plt.scatter(x, y, label=‘原始数据‘)

![](img/f801af2de834962a69cc09ee21f27d9f_43.png)

![](img/f801af2de834962a69cc09ee21f27d9f_45.png)

# 根据回归方程计算直线上的y值
# np.poly1d 根据系数生成一个多项式函数
regression_func = np.poly1d(coefficients)
y_fit = regression_func(x)

![](img/f801af2de834962a69cc09ee21f27d9f_47.png)

# 绘制回归直线
plt.plot(x, y_fit, color=‘red‘, linewidth=2, label=‘回归直线‘)

![](img/f801af2de834962a69cc09ee21f27d9f_49.png)

![](img/f801af2de834962a69cc09ee21f27d9f_51.png)

plt.xlabel(‘指标A‘)
plt.ylabel(‘指标B‘)
plt.legend()
plt.title(‘指标A与指标B的回归分析‘)
plt.show()
```

![](img/f801af2de834962a69cc09ee21f27d9f_53.png)

![](img/f801af2de834962a69cc09ee21f27d9f_55.png)

通过可视化，我们可以清晰地看到两个指标之间的线性关系以及回归直线的拟合情况。需要注意的是，在分析前对数据进行适当的预处理（例如，使用增长率而非原始价格）可能使结果更具解释性。

![](img/f801af2de834962a69cc09ee21f27d9f_57.png)

![](img/f801af2de834962a69cc09ee21f27d9f_59.png)

---

![](img/f801af2de834962a69cc09ee21f27d9f_61.png)

![](img/f801af2de834962a69cc09ee21f27d9f_63.png)

## 计算相关系数

除了回归方程，相关系数是衡量两个变量线性关系强度和方向的另一个重要指标。

计算相关系数非常简单，可以直接使用Pandas DataFrame的`.corr()`方法。

![](img/f801af2de834962a69cc09ee21f27d9f_65.png)

![](img/f801af2de834962a69cc09ee21f27d9f_67.png)

以下是计算并展示相关系数矩阵的代码：

![](img/f801af2de834962a69cc09ee21f27d9f_69.png)

```python
# 假设DataFrame df 中包含‘指标A‘和‘指标B‘两列
correlation_matrix = df[[‘指标A‘, ‘指标B‘]].corr()
print(correlation_matrix)
```

该代码会输出一个对称的矩阵，对角线上的值均为1（表示变量与自身的完全相关），非对角线上的值即为两个指标之间的皮尔逊相关系数。

![](img/f801af2de834962a69cc09ee21f27d9f_71.png)

![](img/f801af2de834962a69cc09ee21f27d9f_73.png)

---

![](img/f801af2de834962a69cc09ee21f27d9f_75.png)

![](img/f801af2de834962a69cc09ee21f27d9f_77.png)

## 附加任务：分析相关系数随时间的变化

为了更深入地理解指标间关系的动态特性，我们可以分析相关系数随时间窗口的变化情况。

![](img/f801af2de834962a69cc09ee21f27d9f_79.png)

这个任务的核心是使用滚动窗口计算动态的相关系数。以下是实现步骤：

1.  为其中一个指标创建一个滚动窗口。
2.  在每一个滚动窗口内，计算该窗口数据与另一个指标（或另一个指标的对应窗口）的相关系数。
3.  将计算出的相关系数序列进行可视化。

![](img/f801af2de834962a69cc09ee21f27d9f_81.png)

```python
# 设置滚动窗口大小，例如250天
window_size = 250

![](img/f801af2de834962a69cc09ee21f27d9f_83.png)

# 计算‘指标A‘滚动窗口与‘指标B‘（整个序列）的相关系数
# 注意：这里计算的是每个窗口内的‘指标A‘数据与整个‘指标B‘序列的相关系数，是一种简化计算。
# 更精确的做法是让‘指标B‘也取相同的滚动窗口。
rolling_corr = df[‘指标A‘].rolling(window=window_size).corr(df[‘指标B‘])

![](img/f801af2de834962a69cc09ee21f27d9f_85.png)

# 绘制动态相关系数变化图
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.xlabel(‘时间‘)
plt.ylabel(‘滚动相关系数‘)
plt.title(f‘{window_size}天滚动窗口下指标A与指标B的相关系数变化‘)
plt.axhline(y=0, color=‘black‘, linestyle=‘--‘, linewidth=0.5) # 添加y=0的参考线
plt.show()
```

![](img/f801af2de834962a69cc09ee21f27d9f_87.png)

![](img/f801af2de834962a69cc09ee21f27d9f_89.png)

通过这张图，我们可以观察到两个指标间的相关性如何随着时间推移而增强或减弱，这对于理解市场不同阶段的关系模式非常有价值。

![](img/f801af2de834962a69cc09ee21f27d9f_91.png)

---

![](img/f801af2de834962a69cc09ee21f27d9f_93.png)

![](img/f801af2de834962a69cc09ee21f27d9f_95.png)

## 总结

![](img/f801af2de834962a69cc09ee21f27d9f_97.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/f801af2de834962a69cc09ee21f27d9f_99.png)

我们首先使用NumPy的`np.polyfit`函数构建了线性回归方程，并通过可视化将回归直线与数据散点图结合展示。接着，我们使用Pandas快速计算了两个指标之间的相关系数。最后，我们通过一个附加任务，探索了如何使用滚动窗口计算动态的相关系数，以分析关系随时间的变化趋势。

![](img/f801af2de834962a69cc09ee21f27d9f_101.png)

![](img/f801af2de834962a69cc09ee21f27d9f_103.png)

这些方法是金融时间序列分析的基础，熟练掌握它们能帮助我们量化并理解不同金融指标间的关联。建议大家在课后亲自动手实践代码，并通过修改参数（如滚动窗口大小）来加深理解。