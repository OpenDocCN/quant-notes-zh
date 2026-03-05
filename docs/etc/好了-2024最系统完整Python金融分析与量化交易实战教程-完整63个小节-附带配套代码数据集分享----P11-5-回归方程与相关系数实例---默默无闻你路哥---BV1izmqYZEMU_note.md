# Python金融分析与量化交易实战：P11：5-回归方程与相关系数实例 📈

![](img/f023af23b939d2d1ac03ade8e7e9982e_1.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是分析两个金融指标之间关系的基础。我们将使用NumPy和Pandas库来完成这些任务，并通过可视化来直观地展示分析结果。

![](img/f023af23b939d2d1ac03ade8e7e9982e_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。构建回归方程的方法有很多，我们可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。

![](img/f023af23b939d2d1ac03ade8e7e9982e_7.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_9.png)

`polyfit`函数可以帮助我们计算回归系数，其基本形式是拟合一个多项式。函数需要传入几个参数：
*   第一个参数是自变量X。
*   第二个参数是因变量Y。
*   第三个参数`deg`指定多项式的阶数。

![](img/f023af23b939d2d1ac03ade8e7e9982e_11.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_13.png)

例如，对于线性回归方程 **Y = kX + b**，它包含X的一次项（X¹）和常数项（X⁰）。如果我们设置`deg=1`，就表示拟合一个一次多项式（即直线）。如果设置`deg=2`，则会拟合一个二次多项式（即曲线），包含X的二次项、一次项和常数项。

![](img/f023af23b939d2d1ac03ade8e7e9982e_15.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_17.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_19.png)

在本任务中，我们分析两个指标之间的线性关系，因此将阶数指定为1即可。

![](img/f023af23b939d2d1ac03ade8e7e9982e_21.png)

以下是构建回归方程的关键步骤代码：

```python
import numpy as np
import pandas as pd

![](img/f023af23b939d2d1ac03ade8e7e9982e_23.png)

# 假设df是包含数据的DataFrame，`x_col`和`y_col`是列名
# 在计算前，需要确保数据中没有空值
df.dropna(inplace=True)

![](img/f023af23b939d2d1ac03ade8e7e9982e_25.png)

x = df[‘x_col’].values
y = df[‘y_col’].values

![](img/f023af23b939d2d1ac03ade8e7e9982e_27.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_29.png)

# 使用polyfit进行线性回归拟合，deg=1表示一次多项式
coefficients = np.polyfit(x, y, deg=1)
# coefficients 包含两个值：[k, b]，即斜率和截距
k, b = coefficients
print(f“回归方程为: Y = {k:.4f} * X + {b:.4f}”)
```

![](img/f023af23b939d2d1ac03ade8e7e9982e_31.png)

在计算过程中，如果数据包含空值（NaN），可能会导致计算逆矩阵时出错。因此，在调用`polyfit`函数之前，务必使用`dropna()`等方法清理数据。

![](img/f023af23b939d2d1ac03ade8e7e9982e_33.png)

---

![](img/f023af23b939d2d1ac03ade8e7e9982e_35.png)

## 可视化回归方程

![](img/f023af23b939d2d1ac03ade8e7e9982e_37.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_39.png)

得到回归方程的系数后，我们可以在散点图的基础上将这条回归线画出来，使关系更直观。

以下是绘制散点图和回归线的步骤：

![](img/f023af23b939d2d1ac03ade8e7e9982e_41.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_43.png)

1.  首先，绘制两个指标的散点图。
2.  然后，根据回归方程 **Y = kX + b**，计算出一系列对应的Y值。
3.  最后，将这些点连接起来，绘制回归线。

![](img/f023af23b939d2d1ac03ade8e7e9982e_45.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_47.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_49.png)

```python
import matplotlib.pyplot as plt

![](img/f023af23b939d2d1ac03ade8e7e9982e_51.png)

# 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(x, y, s=16, label=‘数据点’) # s参数控制点的大小

![](img/f023af23b939d2d1ac03ade8e7e9982e_53.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_55.png)

# 生成用于绘制回归线的X值序列（通常使用原X值的范围）
x_line = np.linspace(x.min(), x.max(), 100)
# 根据回归方程计算对应的Y值
y_line = k * x_line + b

![](img/f023af23b939d2d1ac03ade8e7e9982e_57.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_59.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_61.png)

# 绘制回归线
plt.plot(x_line, y_line, color=‘red’, label=‘回归线’)

![](img/f023af23b939d2d1ac03ade8e7e9982e_63.png)

plt.xlabel(‘X 指标’)
plt.ylabel(‘Y 指标’)
plt.title(‘指标关系与回归线’)
plt.legend()
plt.show()
```

![](img/f023af23b939d2d1ac03ade8e7e9982e_65.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_67.png)

通过这张图，我们可以一目了然地看到两个指标之间的线性关系趋势。需要注意的是，在金融分析中，我们有时会对原始数据（如价格）进行预处理，例如计算收益率或增长率，然后再进行回归分析，这可能会更符合分析需求。

---

![](img/f023af23b939d2d1ac03ade8e7e9982e_69.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_71.png)

## 计算相关系数

![](img/f023af23b939d2d1ac03ade8e7e9982e_73.png)

除了回归方程，衡量两个变量之间线性关系紧密程度的另一个重要指标是相关系数。在Pandas中，计算相关系数非常简单。

以下是计算相关系数的方法：

![](img/f023af23b939d2d1ac03ade8e7e9982e_75.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_77.png)

```python
# 计算DataFrame中所有数值列两两之间的相关系数矩阵
correlation_matrix = df[[‘col1’, ‘col2’]].corr()
print(correlation_matrix)
```
相关系数矩阵是一个对称矩阵，对角线上的值均为1（表示自己与自己的完全相关），非对角线上的值就是两个不同列之间的相关系数，其值在-1到1之间。

![](img/f023af23b939d2d1ac03ade8e7e9982e_79.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_81.png)

---

![](img/f023af23b939d2d1ac03ade8e7e9982e_83.png)

## 附加任务：分析相关系数随时间的变化

为了更深入地分析，我们可以研究相关系数如何随时间变化。例如，我们可以计算滚动窗口内的相关系数。

![](img/f023af23b939d2d1ac03ade8e7e9982e_85.png)

以下是实现滚动相关系数计算的思路和代码：

![](img/f023af23b939d2d1ac03ade8e7e9982e_87.png)

1.  思路：随着时间窗口的滑动，在每一个窗口内（例如包含250个数据点），计算两个指标在该窗口期内的相关系数。
2.  这样，我们就得到了一个随时间变化的相关系数序列。

![](img/f023af23b939d2d1ac03ade8e7e9982e_89.png)

```python
# 设置滚动窗口大小，例如250个交易日
window_size = 250

![](img/f023af23b939d2d1ac03ade8e7e9982e_91.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_93.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_95.png)

# 计算滚动相关系数
# 这里计算的是df[‘col1’]与df[‘col2’]在250天窗口内的相关系数
rolling_corr = df[‘col1’].rolling(window=window_size).corr(df[‘col2’])

![](img/f023af23b939d2d1ac03ade8e7e9982e_97.png)

# 绘制滚动相关系数变化图
plt.figure(figsize=(12, 6))
rolling_corr.plot()
plt.title(f‘{window_size}天滚动相关系数变化’)
plt.xlabel(‘时间’)
plt.ylabel(‘相关系数’)
plt.axhline(y=0, color=‘black’, linestyle=‘--’, linewidth=0.5) # 添加y=0的参考线
plt.show()
```
通过这个滚动相关系数图，我们可以观察到两个指标之间的关系是稳定的还是动态变化的，这对于理解市场不同阶段的特点非常有帮助。

![](img/f023af23b939d2d1ac03ade8e7e9982e_99.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_101.png)

---

![](img/f023af23b939d2d1ac03ade8e7e9982e_103.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_105.png)

## 总结

![](img/f023af23b939d2d1ac03ade8e7e9982e_107.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_109.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/f023af23b939d2d1ac03ade8e7e9982e_111.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_113.png)

*   我们使用NumPy的`polyfit`函数构建了线性回归方程，并通过可视化将回归线呈现在散点图上。
*   我们学习了如何使用Pandas便捷地计算相关系数矩阵，以量化变量间的线性关系。
*   最后，我们通过一个附加任务，探索了如何计算并可视化滚动窗口内的相关系数，从而观察关系随时间的变化趋势。

![](img/f023af23b939d2d1ac03ade8e7e9982e_115.png)

![](img/f023af23b939d2d1ac03ade8e7e9982e_117.png)

这些方法是金融时间序列分析的基础。建议大家在课后亲自动手实践一遍代码，尝试修改参数（如滚动窗口的长度），以加深理解并巩固学习效果。