# Python金融分析与量化交易实战教程：P9：07-7-回归方程与相关系数实例 📈

![](img/d5a90bba4116d0979875ebc1fc60c7ec_1.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是金融数据分析中量化两个指标间关系的基础技能。我们将使用NumPy和Pandas库，通过实例代码演示从数据处理到结果可视化的完整流程。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_7.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_9.png)

构建回归方程的方法很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_11.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_13.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_15.png)

以下是使用`polyfit`函数的关键步骤：

![](img/d5a90bba4116d0979875ebc1fc60c7ec_17.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_19.png)

1.  **准备数据**：需要传入两个指标的数据，例如`y`（因变量）和`x`（自变量）。
2.  **指定阶数**：通过`deg`参数指定回归方程的阶数。`deg=1`表示构建**一次线性方程** `y = kx + b`，即包含x的一次项和常数项。若指定`deg=2`，则方程包含x的二次项、一次项和常数项，可用于拟合曲线。
3.  **处理数据**：在计算前，必须确保数据中没有空值（NaN），否则会导致计算错误。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_21.png)

核心公式为一次线性回归方程：
`y = k * x + b`

![](img/d5a90bba4116d0979875ebc1fc60c7ec_23.png)

对应的计算代码如下：
```python
import numpy as np
# 假设 y_data 和 x_data 是准备好的数据序列
k, b = np.polyfit(x_data, y_data, deg=1)
```

---

![](img/d5a90bba4116d0979875ebc1fc60c7ec_25.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_27.png)

## 可视化回归方程

![](img/d5a90bba4116d0979875ebc1fc60c7ec_29.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_31.png)

得到回归系数后，我们可以将回归线绘制在散点图上，直观地展示关系。

以下是绘制回归线的步骤：

![](img/d5a90bba4116d0979875ebc1fc60c7ec_33.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_35.png)

1.  首先绘制两个指标的散点图。
2.  根据回归方程 `y = kx + b`，利用计算出的系数`k`和`b`，为每个x值计算对应的预测y值。
3.  将计算出的(x, y_pred)点连接成线，覆盖在散点图上。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_37.png)

具体实现代码如下：
```python
import matplotlib.pyplot as plt

# 1. 绘制散点图
plt.figure(figsize=(16, 9))
plt.scatter(x_data, y_data, label=‘数据点’)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_39.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_41.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_43.png)

# 2. 计算回归线对应的y值
y_pred = k * x_data + b

![](img/d5a90bba4116d0979875ebc1fc60c7ec_45.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_47.png)

# 3. 绘制回归线
plt.plot(x_data, y_pred, color=‘red’, label=‘回归线’)
plt.legend()
plt.show()
```
通过可视化，两个指标间的线性关系便一目了然。需要注意的是，分析时应确保数据经过了合适的预处理（例如，本例中使用的是增长率数据），以使分析结果更具意义。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_49.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_51.png)

---

![](img/d5a90bba4116d0979875ebc1fc60c7ec_53.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_55.png)

## 计算相关系数

![](img/d5a90bba4116d0979875ebc1fc60c7ec_57.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_59.png)

除了回归方程，相关系数是另一个衡量两个变量间线性关系强度和方向的常用指标。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_61.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_63.png)

在Pandas中，计算相关系数非常简单。对于一个包含多个指标的数据框，可以直接调用`.corr()`方法得到相关系数矩阵。

以下是计算和解读相关系数的步骤：

![](img/d5a90bba4116d0979875ebc1fc60c7ec_65.png)

*   相关系数矩阵是一个对称矩阵。
*   对角线上的值均为1，表示变量与自身的完全相关。
*   非对角线上的值表示对应两个变量间的相关系数，其值介于-1到1之间。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_67.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_69.png)

示例代码如下：
```python
# 假设df是一个包含‘指标A’和‘指标B’两列数据的DataFrame
correlation_matrix = df[[‘指标A‘， ’指标B‘]].corr()
print(correlation_matrix)
```

---

![](img/d5a90bba4116d0979875ebc1fc60c7ec_71.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_73.png)

## 附加任务：分析相关系数随时间的变化

![](img/d5a90bba4116d0979875ebc1fc60c7ec_75.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_77.png)

为了更深入地分析，我们可以研究两个指标间的相关系数如何随时间变化。这通常通过计算滚动窗口（滑动窗口）的相关系数来实现。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_79.png)

以下是实现滚动相关系数分析的思路：

1.  **定义窗口**：选择一个时间窗口大小（例如250天）。
2.  **滚动计算**：对其中一个指标的数据，在定义的窗口上滚动。
3.  **计算关系**：在每个滚动窗口内，计算该窗口数据与另一个指标对应窗口数据之间的相关系数。
4.  **可视化**：将计算出的随时间变化的相关系数序列绘制成图。

这能帮助我们观察两者关系是稳定的，还是在特定时期发生了显著变化。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_81.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_83.png)

示例代码如下：
```python
# 计算‘指标A’与‘指标B’的250天滚动相关系数
rolling_corr = df[‘指标A‘].rolling(window=250).corr(df[‘指标B‘])

![](img/d5a90bba4116d0979875ebc1fc60c7ec_85.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_87.png)

# 绘制结果
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.title(‘滚动相关系数变化图’)
plt.show()
```

![](img/d5a90bba4116d0979875ebc1fc60c7ec_89.png)

---

![](img/d5a90bba4116d0979875ebc1fc60c7ec_91.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_93.png)

## 总结

![](img/d5a90bba4116d0979875ebc1fc60c7ec_95.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_97.png)

本节课中我们一起学习了金融时间序列分析中的两个核心关系量化工具：**回归方程**与**相关系数**。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_99.png)

我们掌握了如何使用NumPy的`polyfit`函数构建线性回归方程，并通过Matplotlib将结果可视化。同时，也学会了使用Pandas便捷地计算整体及滚动窗口的相关系数，从而多角度评估变量间的关联动态。

![](img/d5a90bba4116d0979875ebc1fc60c7ec_101.png)

![](img/d5a90bba4116d0979875ebc1fc60c7ec_103.png)

建议大家在课后对照代码实践一遍，并尝试修改参数（如窗口大小），以加深理解并巩固学习效果。这些技能是进行更深入的金融数据分析和量化策略研究的重要基础。