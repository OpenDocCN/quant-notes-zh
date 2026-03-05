# Python量化交易：P11：回归方程与相关系数实例 📈

![](img/5acd3b39f692fd42cbb7fdc635245659_1.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_2.png)

在本节课中，我们将学习如何使用Python的NumPy和Pandas库，基于金融时间序列数据构建回归方程并计算相关系数。我们将通过一个具体的实例，演示从数据处理、模型构建到结果可视化的完整流程。

![](img/5acd3b39f692fd42cbb7fdc635245659_3.png)

## 构建回归方程

上一节我们介绍了数据处理的基本方法，本节中我们来看看如何构建回归方程。构建回归方程的方法很多，许多工具包都可以完成。这里我们使用NumPy库来实现。

NumPy库中有一个`polyfit`函数，可以帮助我们计算回归系数。它本质上是通过最小二乘法拟合一个多项式。

以下是构建回归方程的关键步骤：

![](img/5acd3b39f692fd42cbb7fdc635245659_5.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_6.png)

1.  **准备数据**：首先，我们需要两个指标的数据。例如，我们有两个金融时间序列指标 `X` 和 `Y`。
2.  **调用函数**：使用 `np.polyfit(X, Y, deg)` 函数。其中 `deg` 参数指定了多项式的阶数。
    *   `deg=1` 表示拟合一个线性方程：**Y = kX + b**。
    *   `deg=2` 表示拟合一个二次方程：**Y = aX² + bX + c**。
3.  **处理数据**：在拟合前，需要确保数据中没有空值（NaN），否则计算会出错。我们可以使用 `df.dropna(inplace=True)` 来清理数据。

![](img/5acd3b39f692fd42cbb7fdc635245659_7.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_8.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_9.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_10.png)

让我们通过代码来具体实现：

![](img/5acd3b39f692fd42cbb7fdc635245659_11.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_12.png)

```python
import numpy as np
import pandas as pd

![](img/5acd3b39f692fd42cbb7fdc635245659_14.png)

# 假设 df 是一个包含指标 ‘X’ 和 ‘Y’ 的DataFrame
# 清理数据中的空值
df.dropna(inplace=True)

# 提取X和Y的数据
X_data = df[‘X’].values
Y_data = df[‘Y’].values

![](img/5acd3b39f692fd42cbb7fdc635245659_16.png)

# 使用np.polyfit拟合一个一阶（线性）回归方程
# 函数返回的系数从高次项到低次项排列，对于 deg=1，即 [k, b]
coefficients = np.polyfit(X_data, Y_data, deg=1)
k, b = coefficients
print(f“回归方程为：Y = {k:.4f} * X + {b:.4f}”)
```

![](img/5acd3b39f692fd42cbb7fdc635245659_17.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_18.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_19.png)

## 可视化回归方程

得到回归方程后，我们可以将其可视化，以便更直观地观察两个指标之间的关系。

![](img/5acd3b39f692fd42cbb7fdc635245659_21.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_22.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_23.png)

以下是绘制散点图和回归线的步骤：

1.  **绘制散点图**：首先，用散点图展示原始数据点。
2.  **计算回归线**：根据我们求得的系数 `k` 和 `b`，为X数据范围内的点计算对应的Y值，从而得到回归线上的点。
3.  **绘制回归线**：将计算出的回归线点绘制在同一个图上。

![](img/5acd3b39f692fd42cbb7fdc635245659_25.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_26.png)

以下是实现代码：

![](img/5acd3b39f692fd42cbb7fdc635245659_27.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_29.png)

```python
import matplotlib.pyplot as plt

# 绘制原始数据的散点图
plt.figure(figsize=(16, 9))
plt.scatter(x=X_data, y=Y_data, s=16, label=‘原始数据点’)

![](img/5acd3b39f692fd42cbb7fdc635245659_31.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_32.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_33.png)

# 根据回归方程计算拟合的Y值
# 使用np.polyval可以直接根据系数和X值计算Y值
Y_fit = np.polyval(coefficients, X_data)

![](img/5acd3b39f692fd42cbb7fdc635245659_34.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_35.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_36.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_37.png)

# 绘制回归线
plt.plot(X_data, Y_fit, color=‘red’, linewidth=2, label=‘回归线’)

![](img/5acd3b39f692fd42cbb7fdc635245659_39.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_40.png)

plt.xlabel(‘X指标’)
plt.ylabel(‘Y指标’)
plt.title(‘X与Y指标的回归分析’)
plt.legend()
plt.grid(True)
plt.show()
```

## 计算相关系数

![](img/5acd3b39f692fd42cbb7fdc635245659_42.png)

除了回归方程，衡量两个变量之间线性关系强度的另一个重要指标是相关系数。在Pandas中，计算相关系数非常简单。

![](img/5acd3b39f692fd42cbb7fdc635245659_44.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_45.png)

我们可以直接使用DataFrame的`.corr()`方法来计算所有列之间的相关系数矩阵。

![](img/5acd3b39f692fd42cbb7fdc635245659_47.png)

```python
# 计算DataFrame中所有列（这里只有X和Y两列）的相关系数矩阵
correlation_matrix = df[[‘X‘， ’Y‘]].corr()
print(correlation_matrix)
```

![](img/5acd3b39f692fd42cbb7fdc635245659_49.png)

对于一个只有两列的数据框，输出结果是一个2x2的矩阵。对角线上的值都是1（变量与自身的完全相关），非对角线上的值就是X和Y的皮尔逊相关系数，其值在-1到1之间。

![](img/5acd3b39f692fd42cbb7fdc635245659_50.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_51.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_53.png)

## 附加任务：随时间变化的相关系数

在实际分析中，两个指标的关系可能不是一成不变的，它会随着时间推移而发生变化。我们可以通过计算滚动窗口内的相关系数来观察这种动态变化。

![](img/5acd3b39f692fd42cbb7fdc635245659_55.png)

以下是实现步骤：

1.  **定义窗口**：选择一个时间窗口大小，例如250天（代表大约一年的交易日）。
2.  **滚动计算**：对其中一个指标序列，计算其在每个滚动窗口内与另一个固定指标的相关系数。
3.  **可视化结果**：将计算出的滚动相关系数绘制成折线图。

![](img/5acd3b39f692fd42cbb7fdc635245659_57.png)

```python
# 设置滚动窗口大小
window_size = 250

# 计算指标X相对于指标Y的滚动相关系数
# 这里使用 .rolling(window=window_size).corr(other_series) 方法
rolling_corr = df[‘X’].rolling(window=window_size).corr(df[‘Y’])

![](img/5acd3b39f692fd42cbb7fdc635245659_59.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_60.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_61.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_62.png)

# 绘制滚动相关系数变化图
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.title(f‘{window_size}天滚动窗口下X与Y的相关系数变化’)
plt.xlabel(‘时间’)
plt.ylabel(‘相关系数’)
plt.axhline(y=0, color=‘black’, linestyle=‘--’, linewidth=0.5) # 添加y=0的参考线
plt.grid(True)
plt.show()
```

通过这张图，我们可以清晰地看到X和Y两个指标之间的关联强度是如何随时间演变的。

![](img/5acd3b39f692fd42cbb7fdc635245659_64.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_65.png)

## 总结

![](img/5acd3b39f692fd42cbb7fdc635245659_66.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_67.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_68.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_69.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/5acd3b39f692fd42cbb7fdc635245659_71.png)

我们首先使用NumPy的`polyfit`函数构建了线性回归方程，并学会了如何将方程结果可视化。接着，我们使用Pandas便捷地计算了两个指标之间的静态相关系数。最后，我们通过一个附加任务，探索了如何计算并可视化随时间变化的滚动相关系数，这能帮助我们洞察变量间关系的动态特性。

![](img/5acd3b39f692fd42cbb7fdc635245659_72.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_73.png)

![](img/5acd3b39f692fd42cbb7fdc635245659_74.png)

这些方法是量化分析和数据挖掘的基础，建议你在理解代码的基础上，尝试修改参数（如回归方程的阶数、滚动窗口的大小），或者应用于其他数据集，以加深理解并巩固学习效果。