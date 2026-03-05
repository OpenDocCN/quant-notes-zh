# 金融量化分析实战：P11：回归方程与相关系数实例 📈

![](img/4976361ef4a646fc585a64a7ba40e702_1.png)

![](img/4976361ef4a646fc585a64a7ba40e702_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，以分析金融时间序列数据中两个指标之间的关系。我们将使用NumPy和Pandas库来完成这些任务。

![](img/4976361ef4a646fc585a64a7ba40e702_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

![](img/4976361ef4a646fc585a64a7ba40e702_7.png)

构建回归方程的方法有很多，许多工具包都可以实现。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/4976361ef4a646fc585a64a7ba40e702_9.png)

![](img/4976361ef4a646fc585a64a7ba40e702_11.png)

**公式**：`y = kx + b`

![](img/4976361ef4a646fc585a64a7ba40e702_13.png)

![](img/4976361ef4a646fc585a64a7ba40e702_15.png)

以下是使用`polyfit`函数的关键步骤：

![](img/4976361ef4a646fc585a64a7ba40e702_17.png)

![](img/4976361ef4a646fc585a64a7ba40e702_19.png)

1.  **准备数据**：需要传入两个指标的数据，分别作为自变量X和因变量Y。
2.  **指定阶数**：`polyfit`函数的第三个参数`deg`用于指定回归方程的阶数。
    *   `deg=1`：表示方程包含X的一次项和常数项，即线性方程 `y = kx + b`。
    *   `deg=2`：表示方程包含X的二次项、一次项和常数项，即二次曲线。
3.  **处理数据**：在计算前，需要确保数据中没有空值（NaN），否则可能导致计算错误。

以下是构建线性回归方程的代码示例：

![](img/4976361ef4a646fc585a64a7ba40e702_21.png)

```python
import numpy as np
import pandas as pd

![](img/4976361ef4a646fc585a64a7ba40e702_23.png)

# 假设 df 是包含数据的DataFrame，且 ‘X_column‘ 和 ‘Y_column‘ 是列名
# 首先处理空值
df[['X_column', 'Y_column']].dropna(inplace=True)

# 使用 polyfit 计算回归系数 (k, b)
coefficients = np.polyfit(df['X_column'], df['Y_column'], deg=1)
k, b = coefficients
print(f"回归方程为: y = {k:.4f} * x + {b:.4f}")
```

![](img/4976361ef4a646fc585a64a7ba40e702_25.png)

![](img/4976361ef4a646fc585a64a7ba40e702_27.png)

---

![](img/4976361ef4a646fc585a64a7ba40e702_29.png)

![](img/4976361ef4a646fc585a64a7ba40e702_31.png)

## 可视化回归结果

得到回归方程后，我们可以将其可视化，以便更直观地观察变量间的关系。

![](img/4976361ef4a646fc585a64a7ba40e702_33.png)

以下是绘制散点图和回归线的步骤：

![](img/4976361ef4a646fc585a64a7ba40e702_35.png)

![](img/4976361ef4a646fc585a64a7ba40e702_37.png)

1.  使用Matplotlib绘制原始数据的散点图。
2.  根据回归方程 `y = kx + b`，计算出一系列预测的Y值。
3.  将这些预测点连接起来，绘制出回归线。

以下是实现可视化的代码：

![](img/4976361ef4a646fc585a64a7ba40e702_39.png)

```python
import matplotlib.pyplot as plt

![](img/4976361ef4a646fc585a64a7ba40e702_41.png)

![](img/4976361ef4a646fc585a64a7ba40e702_43.png)

# 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(df['X_column'], df['Y_column'], s=16, label='原始数据点')

![](img/4976361ef4a646fc585a64a7ba40e702_45.png)

![](img/4976361ef4a646fc585a64a7ba40e702_47.png)

# 生成用于绘制回归线的X值（通常使用最小值和最大值）
x_line = np.linspace(df['X_column'].min(), df['X_column'].max(), 100)
# 根据回归方程计算对应的Y值
y_line = k * x_line + b

![](img/4976361ef4a646fc585a64a7ba40e702_49.png)

# 绘制回归线
plt.plot(x_line, y_line, color='red', label='回归线')

![](img/4976361ef4a646fc585a64a7ba40e702_51.png)

![](img/4976361ef4a646fc585a64a7ba40e702_53.png)

plt.xlabel('X指标')
plt.ylabel('Y指标')
plt.title('指标关系与回归线')
plt.legend()
plt.show()
```

![](img/4976361ef4a646fc585a64a7ba40e702_55.png)

![](img/4976361ef4a646fc585a64a7ba40e702_57.png)

**注意**：在金融分析中，直接使用原始价格数据构建回归方程可能不合适。通常会对数据进行预处理，例如计算收益率或对数收益率，以消除量纲和趋势的影响，使分析更具统计意义。

![](img/4976361ef4a646fc585a64a7ba40e702_59.png)

---

![](img/4976361ef4a646fc585a64a7ba40e702_61.png)

![](img/4976361ef4a646fc585a64a7ba40e702_63.png)

## 计算相关系数

![](img/4976361ef4a646fc585a64a7ba40e702_65.png)

除了回归方程，相关系数是衡量两个变量线性关系强度和方向的另一个重要指标。

在Pandas中，计算两个序列的相关系数非常简单。`corr()`方法可以直接返回相关系数矩阵。

![](img/4976361ef4a646fc585a64a7ba40e702_67.png)

![](img/4976361ef4a646fc585a64a7ba40e702_69.png)

以下是计算相关系数的代码：

![](img/4976361ef4a646fc585a64a7ba40e702_71.png)

```python
# 计算两个指标列的相关系数
correlation = df['X_column'].corr(df['Y_column'])
print(f"X_column 与 Y_column 的相关系数为: {correlation:.4f}")

# 或者计算整个DataFrame中所有数值列的相关矩阵
corr_matrix = df[['X_column', 'Y_column']].corr()
print("相关系数矩阵：")
print(corr_matrix)
```

![](img/4976361ef4a646fc585a64a7ba40e702_73.png)

![](img/4976361ef4a646fc585a64a7ba40e702_75.png)

相关系数矩阵是一个对称矩阵，对角线上的值均为1（变量与自身的完全相关）。

![](img/4976361ef4a646fc585a64a7ba40e702_77.png)

![](img/4976361ef4a646fc585a64a7ba40e702_79.png)

---

## 附加任务：计算滚动相关系数

![](img/4976361ef4a646fc585a64a7ba40e702_81.png)

在实际分析中，我们常常关心关系是否随时间变化。因此，我们可以计算滚动窗口（滑动窗口）下的相关系数。

**核心概念**：滚动窗口计算。我们设定一个固定大小的窗口（例如250个交易日），让这个窗口在时间序列上滑动。在每个窗口内，计算两个指标的相关系数，从而得到一条随时间变化的相关系数曲线。

以下是计算并绘制滚动相关系数的代码：

![](img/4976361ef4a646fc585a64a7ba40e702_83.png)

![](img/4976361ef4a646fc585a64a7ba40e702_85.png)

```python
# 设置滚动窗口大小，例如250个数据点（代表约一年的交易日）
window_size = 250

![](img/4976361ef4a646fc585a64a7ba40e702_87.png)

# 计算指标A相对于指标B的滚动相关系数
rolling_corr = df['指标A'].rolling(window=window_size).corr(df['指标B'])

![](img/4976361ef4a646fc585a64a7ba40e702_89.png)

![](img/4976361ef4a646fc585a64a7ba40e702_91.png)

# 绘制滚动相关系数变化图
plt.figure(figsize=(12, 6))
rolling_corr.plot(figsize=(12, 6))
plt.axhline(y=0, color='grey', linestyle='--', linewidth=0.8) # 添加y=0的参考线
plt.title(f'滚动相关系数变化 (窗口大小={window_size})')
plt.ylabel('相关系数')
plt.xlabel('时间')
plt.grid(True, alpha=0.3)
plt.show()
```

![](img/4976361ef4a646fc585a64a7ba40e702_93.png)

通过这个图表，我们可以清晰地看到两个指标间的关联强度在不同时期是如何演变的，这对于理解市场动态非常有价值。

![](img/4976361ef4a646fc585a64a7ba40e702_95.png)

![](img/4976361ef4a646fc585a64a7ba40e702_97.png)

---

![](img/4976361ef4a646fc585a64a7ba40e702_99.png)

## 总结

![](img/4976361ef4a646fc585a64a7ba40e702_101.png)

![](img/4976361ef4a646fc585a64a7ba40e702_103.png)

本节课中我们一起学习了金融时间序列分析中的两个核心工具：

![](img/4976361ef4a646fc585a64a7ba40e702_105.png)

![](img/4976361ef4a646fc585a64a7ba40e702_107.png)

1.  **回归方程**：我们使用NumPy的`polyfit`函数构建了线性回归方程 `y = kx + b`，并通过可视化将回归线叠加在散点图上，直观展示了变量间的线性趋势。
2.  **相关系数**：我们使用Pandas的`corr()`方法计算了皮尔逊相关系数，用以量化两个变量线性关系的强度和方向。
3.  **进阶应用**：我们进一步引入了**滚动相关系数**的计算，展示了如何通过滑动窗口来观察两个指标间关系随时间的变化情况，这是金融时间序列分析中一个非常实用的技巧。

![](img/4976361ef4a646fc585a64a7ba40e702_109.png)

![](img/4976361ef4a646fc585a64a7ba40e702_111.png)

建议大家在课后亲自动手实践一遍代码，尝试修改参数（如回归方程的阶数、滚动窗口的大小），以加深对每个步骤和概念的理解。这些技能是进行更复杂金融数据分析和量化策略研究的基础。