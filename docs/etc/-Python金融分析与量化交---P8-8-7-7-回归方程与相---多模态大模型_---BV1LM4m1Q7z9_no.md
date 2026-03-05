# Python金融分析与量化交易实战：P8：8.7.7-回归方程与相关系数实例P7 📈

![](img/db66ed85da670bb4d125d9fc22b93dc9_1.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_3.png)

在本节课中，我们将学习如何使用Python构建回归方程和计算相关系数，这是金融数据分析中衡量两个变量关系的基础方法。我们将使用NumPy和Pandas库来完成这些任务，并通过可视化来直观地展示分析结果。

![](img/db66ed85da670bb4d125d9fc22b93dc9_5.png)

---

## 构建回归方程

上一节我们介绍了数据预处理，本节中我们来看看如何构建回归方程。构建回归方程的方法有很多，我们可以选择多种工具包。这里我们使用NumPy库来完成。

NumPy库中有一个`polyfit`函数，可以帮助我们计算回归模型的系数。它并不是训练一个复杂的模型，而是直接计算回归系数。

![](img/db66ed85da670bb4d125d9fc22b93dc9_7.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_9.png)

**公式**：`Y = kX + b`

![](img/db66ed85da670bb4d125d9fc22b93dc9_11.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_13.png)

以下是使用`polyfit`函数的关键步骤：

![](img/db66ed85da670bb4d125d9fc22b93dc9_15.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_17.png)

1.  我们需要传入三个主要参数：自变量X、因变量Y以及回归方程的阶数。
2.  阶数为1表示我们构建的是一元线性回归方程，即只包含X的一次项和常数项。
3.  如果阶数为2，则方程会包含X的二次项、一次项和常数项，可以拟合曲线。在本例中，我们使用线性关系，因此阶数指定为1。

![](img/db66ed85da670bb4d125d9fc22b93dc9_19.png)

在计算之前，必须确保数据中没有空值（NaN），否则会导致计算错误。我们可以使用Pandas的`dropna`方法清理数据。

![](img/db66ed85da670bb4d125d9fc22b93dc9_21.png)

```python
import numpy as np
import pandas as pd

![](img/db66ed85da670bb4d125d9fc22b93dc9_23.png)

# 假设 df 是包含数据的DataFrame，`x_col` 和 `y_col` 是列名
# 清理数据中的空值
df[[x_col, y_col]].dropna(inplace=True)

# 使用 numpy 的 polyfit 计算回归系数 (k, b)
coefficients = np.polyfit(df[x_col], df[y_col], deg=1)
k, b = coefficients
print(f"回归方程为: Y = {k:.4f} * X + {b:.4f}")
```

![](img/db66ed85da670bb4d125d9fc22b93dc9_25.png)

得到回归方程后，我们可以将其可视化，与原始数据的散点图进行对比。

![](img/db66ed85da670bb4d125d9fc22b93dc9_27.png)

---

![](img/db66ed85da670bb4d125d9fc22b93dc9_29.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_31.png)

## 可视化回归方程

现在我们已经求解出回归方程，接下来看看如何在图中将这条回归线画出来。

![](img/db66ed85da670bb4d125d9fc22b93dc9_33.png)

首先，我们需要绘制原始数据的散点图。然后，根据我们计算出的回归系数`k`和`b`，为一系列X值计算出对应的预测Y值，从而生成回归线。

![](img/db66ed85da670bb4d125d9fc22b93dc9_35.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_37.png)

以下是绘制散点图和回归线的步骤：

1.  使用Matplotlib绘制原始数据的散点图。
2.  生成一组覆盖X值范围的序列。
3.  利用回归方程 `Y = k * X + b` 计算这组X对应的预测Y值。
4.  将计算出的(X, Y_pred)点连接成线，绘制在同一个图上。

```python
import matplotlib.pyplot as plt

![](img/db66ed85da670bb4d125d9fc22b93dc9_39.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_41.png)

# 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(df[x_col], df[y_col], s=16, label='原始数据点')

![](img/db66ed85da670bb4d125d9fc22b93dc9_43.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_45.png)

# 生成用于绘制回归线的X值范围
x_line = np.linspace(df[x_col].min(), df[x_col].max(), 100)
# 根据回归方程计算对应的Y值
y_line = k * x_line + b

![](img/db66ed85da670bb4d125d9fc22b93dc9_47.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_49.png)

# 绘制回归线
plt.plot(x_line, y_line, color='red', linewidth=2, label='回归线')

![](img/db66ed85da670bb4d125d9fc22b93dc9_51.png)

plt.xlabel('X 指标')
plt.ylabel('Y 指标')
plt.title('指标关系散点图与回归线')
plt.legend()
plt.grid(True, linestyle='--', alpha=0.5)
plt.show()
```

![](img/db66ed85da670bb4d125d9fc22b93dc9_53.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_55.png)

通过这张图，两个指标之间的关系就一目了然了。需要注意的是，在构建回归方程时，要确保使用的数据是经过适当预处理的，例如在本例中，我们使用的是增长率数据，而非原始价格数据。

![](img/db66ed85da670bb4d125d9fc22b93dc9_57.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_59.png)

---

![](img/db66ed85da670bb4d125d9fc22b93dc9_61.png)

## 计算相关系数

![](img/db66ed85da670bb4d125d9fc22b93dc9_63.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_65.png)

除了回归方程，衡量两个变量之间线性关系强度的另一个重要指标是相关系数。

相关系数的取值范围在-1到1之间。其绝对值越接近1，表示线性关系越强；越接近0，表示线性关系越弱。计算相关系数在Pandas中非常简单。

![](img/db66ed85da670bb4d125d9fc22b93dc9_67.png)

我们可以直接使用DataFrame的`corr()`方法来计算两列数据之间的相关系数。

![](img/db66ed85da670bb4d125d9fc22b93dc9_69.png)

```python
# 计算两个指标之间的相关系数
correlation = df[[x_col, y_col]].corr().iloc[0, 1]
print(f"指标 '{x_col}' 与 '{y_col}' 的相关系数为: {correlation:.4f}")

![](img/db66ed85da670bb4d125d9fc22b93dc9_71.png)

# 或者，如果需要计算多个指标两两之间的相关系数矩阵
corr_matrix = df[[col1, col2, col3]].corr()
print("相关系数矩阵：")
print(corr_matrix)
```

相关系数矩阵是一个对称矩阵，对角线上的值均为1（每个变量与自身的完全相关）。

![](img/db66ed85da670bb4d125d9fc22b93dc9_73.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_75.png)

---

![](img/db66ed85da670bb4d125d9fc22b93dc9_77.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_79.png)

## 附加任务：滑动窗口相关系数分析

为了更深入地分析，我们可以研究相关系数如何随时间变化。这可以通过计算滑动窗口内的相关系数来实现。

![](img/db66ed85da670bb4d125d9fc22b93dc9_81.png)

思路是：定义一个固定大小的窗口（例如250个交易日），让这个窗口在时间序列上滑动。在每一个窗口内，计算两个指标在该时间段内的相关系数。最终，我们会得到一个随时间变化的相关系数序列。

以下是实现滑动窗口相关系数的步骤：

1.  对第一个指标序列使用`rolling`方法创建滑动窗口。
2.  在每一个窗口上，调用`corr`方法，并传入第二个指标的对应窗口数据，计算相关系数。
3.  结果是一个新的时间序列，展示了两个指标间关系强度的动态变化。

![](img/db66ed85da670bb4d125d9fc22b93dc9_83.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_85.png)

```python
# 设置滑动窗口大小，例如250个数据点（代表约一年的交易日）
window_size = 250

![](img/db66ed85da670bb4d125d9fc22b93dc9_87.png)

# 计算滚动相关系数
rolling_corr = df[x_col].rolling(window=window_size).corr(df[y_col])

![](img/db66ed85da670bb4d125d9fc22b93dc9_89.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_91.png)

# 绘制滚动相关系数随时间的变化
plt.figure(figsize=(12, 6))
rolling_corr.plot(linewidth=1)
plt.title(f'{x_col} 与 {y_col} 的滚动相关系数 (窗口={window_size})')
plt.xlabel('时间')
plt.ylabel('相关系数')
plt.axhline(y=0, color='black', linestyle='-', linewidth=0.5) # 添加y=0的参考线
plt.grid(True, linestyle='--', alpha=0.5)
plt.show()
```

通过这个分析，我们可以观察到两个指标间的关联性是稳定的，还是在某些市场阶段发生了显著变化。

![](img/db66ed85da670bb4d125d9fc22b93dc9_93.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_95.png)

---

![](img/db66ed85da670bb4d125d9fc22b93dc9_97.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_99.png)

## 总结

![](img/db66ed85da670bb4d125d9fc22b93dc9_101.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/db66ed85da670bb4d125d9fc22b93dc9_103.png)

![](img/db66ed85da670bb4d125d9fc22b93dc9_105.png)

我们首先使用NumPy的`polyfit`函数构建了一元线性回归方程，并学会了如何将回归线可视化，使其与数据散点图结合展示。接着，我们使用Pandas便捷地计算了两个变量之间的相关系数，以量化其线性关系的强度。最后，我们通过一个附加任务，探索了如何利用滑动窗口计算动态变化的相关系数，从而分析关系随时间演变的模式。

![](img/db66ed85da670bb4d125d9fc22b93dc9_107.png)

这些方法是理解变量间关系的基础，建议大家在课后对照代码练习，尝试修改参数（如窗口大小），以加深对概念和代码实现的理解。