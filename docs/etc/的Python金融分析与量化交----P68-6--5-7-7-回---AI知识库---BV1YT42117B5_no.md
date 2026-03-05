# 量化交易完全可自学教程：P68：6.5.7.7-回归方程与相关系数实例P7 📈

![](img/01698ead355cc30322ef9406a1dd8942_1.png)

![](img/01698ead355cc30322ef9406a1dd8942_3.png)

在本节课中，我们将学习如何使用Python构建回归方程、绘制回归线，并计算和可视化相关系数及其随时间的变化情况。我们将使用NumPy和Pandas库来完成这些任务。

![](img/01698ead355cc30322ef9406a1dd8942_5.png)

---

## 构建回归方程

上一节我们介绍了回归分析的基本概念，本节中我们来看看如何用代码实现。

![](img/01698ead355cc30322ef9406a1dd8942_7.png)

![](img/01698ead355cc30322ef9406a1dd8942_9.png)

构建回归方程的方法有很多，许多工具包都可以完成。这里我们选择使用NumPy库。NumPy中有一个`polyfit`函数，可以帮助我们计算回归系数。

![](img/01698ead355cc30322ef9406a1dd8942_11.png)

以下是构建回归方程的关键步骤：

![](img/01698ead355cc30322ef9406a1dd8942_13.png)

1.  **准备数据**：确保用于回归分析的数据中没有空值（NaN）。如果存在空值，需要先进行清理。
2.  **调用函数**：使用`np.polyfit(x, y, deg)`函数。其中：
    *   `x`：自变量数据。
    *   `y`：因变量数据。
    *   `deg`：回归方程的阶数。`deg=1`表示拟合一个线性方程 `y = kx + b`。

![](img/01698ead355cc30322ef9406a1dd8942_15.png)

**代码示例**：
```python
import numpy as np
import pandas as pd

![](img/01698ead355cc30322ef9406a1dd8942_17.png)

# 假设 df 是一个包含 ‘x_column‘ 和 ‘y_column‘ 列的DataFrame
# 1. 清理数据中的空值
df.dropna(subset=[‘x_column‘, ‘y_column‘], inplace=True)

![](img/01698ead355cc30322ef9406a1dd8942_19.png)

# 2. 提取自变量x和因变量y
x_data = df[‘x_column‘].values
y_data = df[‘y_column‘].values

![](img/01698ead355cc30322ef9406a1dd8942_21.png)

![](img/01698ead355cc30322ef9406a1dd8942_23.png)

# 3. 使用polyfit进行一元线性回归 (deg=1)
coefficients = np.polyfit(x_data, y_data, deg=1)
# coefficients 是一个数组，例如 [k, b]，对应 y = k*x + b
k, b = coefficients
print(f“回归方程为: y = {k:.4f} * x + {b:.4f}“)
```

![](img/01698ead355cc30322ef9406a1dd8942_25.png)

---

![](img/01698ead355cc30322ef9406a1dd8942_27.png)

## 绘制回归线

![](img/01698ead355cc30322ef9406a1dd8942_29.png)

得到回归方程后，我们可以将其可视化，与原始数据点一起绘制在图表上。

![](img/01698ead355cc30322ef9406a1dd8942_31.png)

![](img/01698ead355cc30322ef9406a1dd8942_33.png)

以下是绘制散点图和回归线的步骤：

![](img/01698ead355cc30322ef9406a1dd8942_35.png)

1.  **绘制散点图**：使用Matplotlib绘制原始数据点。
2.  **生成回归线**：根据回归方程，为一系列x值计算对应的y值，从而得到一条直线。
3.  **绘制回归线**：将这条直线添加到图表中。

![](img/01698ead355cc30322ef9406a1dd8942_37.png)

![](img/01698ead355cc30322ef9406a1dd8942_39.png)

**代码示例**：
```python
import matplotlib.pyplot as plt

![](img/01698ead355cc30322ef9406a1dd8942_41.png)

![](img/01698ead355cc30322ef9406a1dd8942_43.png)

# 继续使用上面的 x_data, y_data, k, b
# 1. 绘制原始数据散点图
plt.figure(figsize=(10, 6))
plt.scatter(x_data, y_data, s=16, label=‘原始数据‘)

![](img/01698ead355cc30322ef9406a1dd8942_45.png)

![](img/01698ead355cc30322ef9406a1dd8942_47.png)

# 2. 生成回归线的数据点
# 创建一组覆盖x数据范围的点
x_line = np.linspace(x_data.min(), x_data.max(), 100)
# 根据回归方程计算y值
y_line = k * x_line + b

![](img/01698ead355cc30322ef9406a1dd8942_49.png)

![](img/01698ead355cc30322ef9406a1dd8942_51.png)

# 3. 绘制回归线
plt.plot(x_line, y_line, color=‘red‘, linewidth=2, label=‘回归线‘)

![](img/01698ead355cc30322ef9406a1dd8942_53.png)

![](img/01698ead355cc30322ef9406a1dd8942_55.png)

plt.xlabel(‘X 指标‘)
plt.ylabel(‘Y 指标‘)
plt.title(‘回归分析图‘)
plt.legend()
plt.grid(True, linestyle=‘--‘, alpha=0.5)
plt.show()
```

![](img/01698ead355cc30322ef9406a1dd8942_57.png)

![](img/01698ead355cc30322ef9406a1dd8942_59.png)

---

![](img/01698ead355cc30322ef9406a1dd8942_61.png)

![](img/01698ead355cc30322ef9406a1dd8942_63.png)

## 计算相关系数

除了回归方程，衡量两个变量之间线性关系强度的常用指标是相关系数。Pandas可以非常方便地计算它。

![](img/01698ead355cc30322ef9406a1dd8942_65.png)

![](img/01698ead355cc30322ef9406a1dd8942_67.png)

以下是计算相关系数的方法：

![](img/01698ead355cc30322ef9406a1dd8942_69.png)

*   使用DataFrame的`.corr()`方法，可以计算所有列两两之间的相关系数矩阵。

**代码示例**：
```python
# 假设df包含多个指标列
correlation_matrix = df[[‘指标A‘, ‘指标B‘, ‘指标C‘]].corr()
print(correlation_matrix)
```
相关系数矩阵是一个对称矩阵，对角线上的值均为1（变量与自身的完全相关），其他位置的值表示对应两个变量之间的相关系数。

![](img/01698ead355cc30322ef9406a1dd8942_71.png)

![](img/01698ead355cc30322ef9406a1dd8942_73.png)

---

![](img/01698ead355cc30322ef9406a1dd8942_75.png)

![](img/01698ead355cc30322ef9406a1dd8942_77.png)

## 附加任务：计算滚动相关系数

![](img/01698ead355cc30322ef9406a1dd8942_79.png)

在实际分析中，我们常常关心关系是否随时间稳定。我们可以计算滚动窗口内的相关系数，以观察其变化趋势。

以下是计算滚动相关系数的思路：

![](img/01698ead355cc30322ef9406a1dd8942_81.png)

1.  **定义窗口**：选择一个时间窗口大小（例如250天）。
2.  **滚动计算**：对其中一个序列，在滚动窗口内计算其与另一个完整序列的相关系数。
3.  **可视化**：将计算出的滚动相关系数序列绘制成折线图。

![](img/01698ead355cc30322ef9406a1dd8942_83.png)

**代码示例**：
```python
# 继续使用DataFrame df，包含‘指标1‘和‘指标2‘，索引应为时间序列
window_size = 250  # 设定滚动窗口大小

![](img/01698ead355cc30322ef9406a1dd8942_85.png)

![](img/01698ead355cc30322ef9406a1dd8942_87.png)

# 计算‘指标1‘相对于‘指标2‘的滚动相关系数
rolling_corr = df[‘指标1‘].rolling(window=window_size).corr(df[‘指标2‘])

![](img/01698ead355cc30322ef9406a1dd8942_89.png)

![](img/01698ead355cc30322ef9406a1dd8942_91.png)

# 绘制滚动相关系数变化图
plt.figure(figsize=(12, 6))
rolling_corr.plot()
plt.title(f‘{window_size}天滚动相关系数变化‘)
plt.xlabel(‘时间‘)
plt.ylabel(‘相关系数‘)
plt.axhline(y=0, color=‘black‘, linestyle=‘-‘, linewidth=0.5)  # 添加y=0的参考线
plt.grid(True, linestyle=‘--‘, alpha=0.5)
plt.show()
```

![](img/01698ead355cc30322ef9406a1dd8942_93.png)

---

![](img/01698ead355cc30322ef9406a1dd8942_95.png)

![](img/01698ead355cc30322ef9406a1dd8942_97.png)

## 总结

![](img/01698ead355cc30322ef9406a1dd8942_99.png)

![](img/01698ead355cc30322ef9406a1dd8942_101.png)

本节课中我们一起学习了金融时间序列分析中的几个核心操作：

![](img/01698ead355cc30322ef9406a1dd8942_103.png)

![](img/01698ead355cc30322ef9406a1dd8942_105.png)

1.  **构建回归方程**：使用`np.polyfit`函数拟合数据，得到描述变量间关系的线性方程 **`y = kx + b`**。
2.  **结果可视化**：通过Matplotlib将原始数据散点图和回归线绘制在一起，直观展示关系。
3.  **计算相关系数**：使用`.corr()`方法快速计算变量间的线性相关程度。
4.  **分析动态关系**：通过计算**滚动相关系数**，观察两个变量间的关系如何随时间窗口变化。

![](img/01698ead355cc30322ef9406a1dd8942_107.png)

建议大家在课后亲自动手实践代码，尝试修改参数（如回归方程阶数`deg`、滚动窗口大小`window_size`），以加深对每个步骤和其背后意义的理解。这些是进行量化策略研究和基本面分析的重要基础工具。