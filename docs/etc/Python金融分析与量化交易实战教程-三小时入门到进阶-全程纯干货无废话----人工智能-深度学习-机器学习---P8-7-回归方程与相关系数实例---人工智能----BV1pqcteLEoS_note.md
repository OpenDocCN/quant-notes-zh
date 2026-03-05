# Python金融分析与量化交易实战教程：P8：回归方程与相关系数实例 📈

![](img/abdfdfa907c737dd335d77cf74d893a7_1.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是金融数据分析中量化两个变量间关系的基础技能。我们将使用NumPy和Pandas库，通过实际代码演示，从数据预处理到结果可视化的完整流程。

![](img/abdfdfa907c737dd335d77cf74d893a7_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量间的线性关系。

![](img/abdfdfa907c737dd335d77cf74d893a7_7.png)

构建回归方程的方法很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/abdfdfa907c737dd335d77cf74d893a7_9.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_11.png)

**公式**：`Y = K * X + B`

![](img/abdfdfa907c737dd335d77cf74d893a7_13.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_15.png)

以下是使用`np.polyfit`函数的关键步骤：

![](img/abdfdfa907c737dd335d77cf74d893a7_17.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_19.png)

1.  **准备数据**：需要传入两个指标的数据，例如`X`和`Y`。
2.  **指定阶数**：`deg`参数指定回归方程的阶数。`deg=1`表示我们构建一个一次线性方程（即`Y = KX + B`）。如果指定`deg=2`，则会包含`X`的二次项，用于拟合曲线关系。在本例中，我们使用`deg=1`。
3.  **处理缺失值**：在计算前，必须清除数据中的空值（NaN），否则会导致计算错误。

![](img/abdfdfa907c737dd335d77cf74d893a7_21.png)

```python
import numpy as np
import pandas as pd

# 假设 df 是包含数据的DataFrame，'col_x' 和 'col_y' 是两个指标列
# 1. 清除缺失值
df.dropna(subset=['col_x', 'col_y'], inplace=True)

![](img/abdfdfa907c737dd335d77cf74d893a7_23.png)

# 2. 提取X和Y数据
x_data = df['col_x'].values
y_data = df['col_y'].values

# 3. 使用np.polyfit计算回归系数 (K, B)
# 参数：X数据，Y数据，多项式阶数
coefficients = np.polyfit(x_data, y_data, deg=1)
K, B = coefficients
print(f"回归方程: Y = {K:.4f} * X + {B:.4f}")
```

![](img/abdfdfa907c737dd335d77cf74d893a7_25.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_27.png)

---

![](img/abdfdfa907c737dd335d77cf74d893a7_29.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_31.png)

## 可视化回归方程

得到回归系数后，我们可以将回归线绘制在散点图上，直观地展示变量间的关系。

![](img/abdfdfa907c737dd335d77cf74d893a7_33.png)

以下是绘图步骤：

![](img/abdfdfa907c737dd335d77cf74d893a7_35.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_37.png)

1.  首先绘制原始数据的散点图。
2.  根据回归方程`Y = KX + B`，生成对应的Y值。
3.  将回归线绘制在同一个图中。

```python
import matplotlib.pyplot as plt

![](img/abdfdfa907c737dd335d77cf74d893a7_39.png)

# 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(x_data, y_data, alpha=0.5, label='原始数据点')

![](img/abdfdfa907c737dd335d77cf74d893a7_41.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_43.png)

# 生成回归线的Y值
# 使用np.poly1d根据系数创建一个多项式函数，方便计算
regression_func = np.poly1d(coefficients)
y_regression = regression_func(x_data)

![](img/abdfdfa907c737dd335d77cf74d893a7_45.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_47.png)

# 绘制回归线
plt.plot(x_data, y_regression, color='red', linewidth=2, label='回归线')

![](img/abdfdfa907c737dd335d77cf74d893a7_49.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_51.png)

plt.xlabel('X 指标')
plt.ylabel('Y 指标')
plt.title('指标间关系与回归线')
plt.legend()
plt.grid(True, linestyle='--', alpha=0.5)
plt.show()
```

![](img/abdfdfa907c737dd335d77cf74d893a7_53.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_55.png)

**注意**：在金融分析中，用于回归的数据通常是经过预处理的，例如使用价格的对数收益率或增长率，而不是原始价格。这能更好地反映变量间的变化关系。

![](img/abdfdfa907c737dd335d77cf74d893a7_57.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_59.png)

---

![](img/abdfdfa907c737dd335d77cf74d893a7_61.png)

## 计算相关系数

![](img/abdfdfa907c737dd335d77cf74d893a7_63.png)

除了回归方程，相关系数是另一个衡量两个变量线性关系强度和方向的常用指标。

在Pandas中，计算两个序列的相关系数非常简单。

![](img/abdfdfa907c737dd335d77cf74d893a7_65.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_67.png)

```python
# 计算两个数据列的相关系数
correlation = df['col_x'].corr(df['col_y'])
print(f"指标 'col_x' 与 'col_y' 的相关系数为: {correlation:.4f}")
```

![](img/abdfdfa907c737dd335d77cf74d893a7_69.png)

如果要计算多个指标两两之间的相关系数，并生成矩阵：

```python
# 假设df包含多个指标列
correlation_matrix = df[['col_a', 'col_b', 'col_c']].corr()
print(correlation_matrix)
```
相关系数矩阵的对角线为1（自己与自己的完全相关），矩阵是对称的。

![](img/abdfdfa907c737dd335d77cf74d893a7_71.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_73.png)

---

![](img/abdfdfa907c737dd335d77cf74d893a7_75.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_77.png)

## 附加任务：计算滚动相关系数

在时间序列分析中，我们常常关心关系如何随时间变化。因此，我们可以计算滚动窗口（滑动窗口）内的相关系数。

![](img/abdfdfa907c737dd335d77cf74d893a7_79.png)

**任务描述**：随着时间推移，计算两个指标在指定窗口期内的相关系数变化情况。

以下是实现步骤：

1.  确定滚动窗口的大小（例如250个交易日）。
2.  对第一个指标序列使用`.rolling()`方法创建滚动窗口对象。
3.  对每个窗口内的数据，调用`.corr()`方法并与第二个指标的对应窗口数据计算相关系数。

![](img/abdfdfa907c737dd335d77cf74d893a7_81.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_83.png)

```python
# 设置滚动窗口大小
window_size = 250

![](img/abdfdfa907c737dd335d77cf74d893a7_85.png)

# 计算滚动相关系数
rolling_corr = df['col_x'].rolling(window=window_size).corr(df['col_y'])

![](img/abdfdfa907c737dd335d77cf74d893a7_87.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_89.png)

# 绘制滚动相关系数变化图
plt.figure(figsize=(12, 6))
rolling_corr.plot()
plt.title(f'{window_size}期滚动相关系数变化')
plt.xlabel('时间')
plt.ylabel('相关系数')
plt.axhline(y=0, color='black', linestyle='--', alpha=0.3) # 添加y=0参考线
plt.grid(True, linestyle='--', alpha=0.5)
plt.show()
```
通过这张图，我们可以清晰地看到两个指标间的关联性在不同时期是增强、减弱还是发生了方向性转变。

![](img/abdfdfa907c737dd335d77cf74d893a7_91.png)

---

![](img/abdfdfa907c737dd335d77cf74d893a7_93.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_95.png)

## 总结

![](img/abdfdfa907c737dd335d77cf74d893a7_97.png)

本节课中我们一起学习了金融数据分析中的两个核心关系度量工具：

![](img/abdfdfa907c737dd335d77cf74d893a7_99.png)

1.  **回归方程**：使用`np.polyfit`函数构建，用于量化并预测一个变量如何随另一个变量线性变化，并通过可视化进行直观展示。
2.  **相关系数**：使用`.corr()`方法计算，用于衡量两个变量线性关系的强度和方向。我们不仅学习了计算静态的总体相关系数，还掌握了计算**滚动相关系数**以观察关系动态变化的方法。

![](img/abdfdfa907c737dd335d77cf74d893a7_101.png)

![](img/abdfdfa907c737dd335d77cf74d893a7_103.png)

建议大家在课后亲自动手，将课程中的代码实践一遍。可以尝试修改参数（如回归方程的阶数、滚动窗口的大小），观察结果如何变化，以加深对概念和代码的理解。这些技能是进行更深入的金融量化分析与建模的重要基础。