# Python金融分析与量化交易实战：10.7：回归方程与相关系数实例 📈

![](img/233058759d76c83cbcedaf9d9e83b95f_1.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是分析两个金融指标之间关系的基础。我们将使用NumPy和Pandas库，通过实际代码演示从数据处理到结果可视化的完整流程。

![](img/233058759d76c83cbcedaf9d9e83b95f_5.png)

---

## 构建回归方程

上一节我们介绍了金融时间序列的基本处理方法，本节中我们来看看如何量化两个指标之间的关系。构建回归方程的方法很多，我们可以使用多种工具包。这里我们使用NumPy库来完成。

![](img/233058759d76c83cbcedaf9d9e83b95f_7.png)

NumPy库中有一个`polyfit`函数，可以帮助我们计算回归模型的系数。它本质上不是训练模型，而是直接计算出回归方程的系数。

![](img/233058759d76c83cbcedaf9d9e83b95f_9.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_11.png)

**公式**：对于一元线性回归，其方程为 `Y = kX + b`。其中，`k`是斜率，`b`是截距。

![](img/233058759d76c83cbcedaf9d9e83b95f_13.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_15.png)

在`polyfit`函数中，我们需要传入几个参数：
*   第一个参数是自变量X。
*   第二个参数是因变量Y。
*   第三个参数`deg`指定了回归方程的次数。`deg=1`表示方程包含X的一次项和零次项（即`Y = kX + b`）。如果指定`deg=2`，则方程会包含X的二次项、一次项和零次项，可以拟合曲线。

![](img/233058759d76c83cbcedaf9d9e83b95f_17.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_19.png)

在我们的任务中，两个指标之间是线性关系，因此指定`deg=1`即可。

![](img/233058759d76c83cbcedaf9d9e83b95f_21.png)

以下是构建回归方程的关键步骤代码：

```python
import numpy as np
import pandas as pd

![](img/233058759d76c83cbcedaf9d9e83b95f_23.png)

# 假设df是我们的数据框，包含‘PX’和‘VX’两列
# 首先，处理数据中的空值
df[['PX', 'VX']] = df[['PX', 'VX']].dropna()

# 使用polyfit计算回归系数
# deg=1 表示进行一元线性回归
coefficients = np.polyfit(df['PX'], df['VX'], deg=1)
# coefficients 是一个数组，第一个元素是斜率k，第二个是截距b
k, b = coefficients[0], coefficients[1]
print(f"回归方程为: Y = {k:.4f} * X + {b:.4f}")
```

![](img/233058759d76c83cbcedaf9d9e83b95f_25.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_27.png)

在计算过程中，如果数据包含空值（NaN），`polyfit`函数可能会在求逆矩阵时报错。因此，在构建回归方程前，必须对数据进行清洗，使用`.dropna()`方法删除含有空值的行。

![](img/233058759d76c83cbcedaf9d9e83b95f_29.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_31.png)

---

## 可视化回归方程

![](img/233058759d76c83cbcedaf9d9e83b95f_33.png)

得到回归方程的系数后，我们可以在散点图上将这条回归线画出来，使关系一目了然。

![](img/233058759d76c83cbcedaf9d9e83b95f_35.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_37.png)

以下是绘制散点图及回归线的步骤：

1.  首先，绘制两个指标的散点图。
2.  然后，根据我们计算出的回归方程`Y = kX + b`，生成对应的Y值。
3.  最后，将这条回归线添加到图中。

![](img/233058759d76c83cbcedaf9d9e83b95f_39.png)

```python
import matplotlib.pyplot as plt

![](img/233058759d76c83cbcedaf9d9e83b95f_41.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_43.png)

# 绘制散点图
plt.figure(figsize=(16, 9))
plt.scatter(df['PX'], df['VX'], label='Data Points')

![](img/233058759d76c83cbcedaf9d9e83b95f_45.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_47.png)

# 生成回归线的点
# 使用原始的X值范围来生成对应的Y值
x_line = df['PX']
y_line = k * x_line + b  # 根据回归方程计算Y值

![](img/233058759d76c83cbcedaf9d9e83b95f_49.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_51.png)

# 绘制回归线，并设置为红色以便区分
plt.plot(x_line, y_line, color='red', linewidth=2, label='Regression Line')

![](img/233058759d76c83cbcedaf9d9e83b95f_53.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_55.png)

plt.xlabel('PX')
plt.ylabel('VX')
plt.title('Scatter Plot with Regression Line')
plt.legend()
plt.show()
```

![](img/233058759d76c83cbcedaf9d9e83b95f_57.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_59.png)

通过可视化，我们可以直观地看到两个指标`PX`和`VX`之间的线性关系趋势。需要注意的是，本例中使用的数据是增长率，而非原始价格。在实际分析中，根据目标选择合适的预处理数据（如原始价格、收益率、对数收益率等）至关重要。

![](img/233058759d76c83cbcedaf9d9e83b95f_61.png)

---

![](img/233058759d76c83cbcedaf9d9e83b95f_63.png)

## 计算与可视化相关系数

除了回归方程，衡量两个变量线性关系强度的另一个核心指标是相关系数。在Pandas中，计算相关系数非常简单。

![](img/233058759d76c83cbcedaf9d9e83b95f_65.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_67.png)

以下是计算整个序列相关系数的方法：

![](img/233058759d76c83cbcedaf9d9e83b95f_69.png)

```python
# 计算‘PX’和‘VX’两列数据的相关系数矩阵
correlation_matrix = df[['PX', 'VX']].corr()
print(correlation_matrix)
```

输出的矩阵对角线上的值均为1（变量与自身的相关性为1），非对角线上的值就是`PX`和`VX`的相关系数，矩阵是对称的。

![](img/233058759d76c83cbcedaf9d9e83b95f_71.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_73.png)

---

![](img/233058759d76c83cbcedaf9d9e83b95f_75.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_77.png)

## 附加任务：分析相关系数的动态变化

在实际金融分析中，指标间的相关性并非一成不变。我们可以通过计算滚动窗口相关系数，来观察其随时间的变化情况。

![](img/233058759d76c83cbcedaf9d9e83b95f_79.png)

**任务描述**：随着时间（年份）的变化，计算两个指标`PX`和`VX`之间相关系数的变化情况，并进行展示。

**实现思路**：
1.  为其中一个指标（如`PX`）定义一个固定大小的滚动窗口（例如250天）。
2.  在这个滚动窗口内，计算`PX`的窗口数据与另一个指标`VX`对应数据的相关系数。
3.  随着窗口在时间轴上滑动，我们就能得到一系列随时间变化的相关系数值。

以下是实现代码：

![](img/233058759d76c83cbcedaf9d9e83b95f_81.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_83.png)

```python
# 设置滚动窗口大小，例如250个交易日
window_size = 250

![](img/233058759d76c83cbcedaf9d9e83b95f_85.png)

# 计算‘PX’与‘VX’的滚动相关系数
# 这里‘PX’作为滚动序列，‘VX’作为固定的对比序列
rolling_corr = df['PX'].rolling(window=window_size).corr(df['VX'])

![](img/233058759d76c83cbcedaf9d9e83b95f_87.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_89.png)

# 可视化滚动相关系数的变化
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.title(f'Rolling Correlation (Window={window_size}) between PX and VX')
plt.xlabel('Date')
plt.ylabel('Correlation Coefficient')
plt.axhline(y=0, color='black', linestyle='--', linewidth=0.5) # 添加y=0的参考线
plt.show()
```

![](img/233058759d76c83cbcedaf9d9e83b95f_91.png)

通过这张图，我们可以清晰地看到`PX`（标普500指数）和`VX`（恐慌指数）之间的相关性如何随着市场环境的变化而波动，这比单一的静态相关系数包含了更多信息。

![](img/233058759d76c83cbcedaf9d9e83b95f_93.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_95.png)

---

![](img/233058759d76c83cbcedaf9d9e83b95f_97.png)

## 总结

![](img/233058759d76c83cbcedaf9d9e83b95f_99.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。
*   我们使用NumPy的`polyfit`函数构建了一元线性回归方程，并掌握了将其可视化到散点图上的方法。
*   我们学习了如何使用Pandas快速计算两个时间序列的相关系数。
*   最后，我们通过一个附加任务，探索了如何计算并可视化滚动窗口相关系数，以观察关系强度的动态变化。

![](img/233058759d76c83cbcedaf9d9e83b95f_101.png)

![](img/233058759d76c83cbcedaf9d9e83b95f_103.png)

这些是分析金融指标间关系的基础。建议大家在课后对照代码练习，理解每一步的作用，并尝试修改参数（如回归方程次数`deg`、滚动窗口大小`window_size`），以加深理解。这些技能对于后续的量化策略研究至关重要。