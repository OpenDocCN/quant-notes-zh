# 量化交易实战：P11：回归方程与相关系数实例 📈

![](img/d45538871842fead0e7072f90f9b175d_1.png)

![](img/d45538871842fead0e7072f90f9b175d_3.png)

![](img/d45538871842fead0e7072f90f9b175d_5.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是量化分析中理解变量间关系的基础。我们将使用NumPy和Pandas库，通过实际金融数据案例，从数据预处理到结果可视化，完整地演示分析流程。

---

## 构建回归方程

![](img/d45538871842fead0e7072f90f9b175d_7.png)

![](img/d45538871842fead0e7072f90f9b175d_9.png)

上一节我们介绍了金融时间序列的基本处理方法，本节中我们来看看如何量化两个指标之间的关系。构建回归方程的方法很多，可以使用多种工具包。这里我们使用NumPy库来完成。

![](img/d45538871842fead0e7072f90f9b175d_11.png)

![](img/d45538871842fead0e7072f90f9b175d_13.png)

![](img/d45538871842fead0e7072f90f9b175d_15.png)

NumPy中有一个`polyfit`函数，可以帮助我们计算回归模型的系数。它并非训练模型，而是直接计算出回归方程的系数。

![](img/d45538871842fead0e7072f90f9b175d_17.png)

![](img/d45538871842fead0e7072f90f9b175d_19.png)

![](img/d45538871842fead0e7072f90f9b175d_21.png)

以下是使用`polyfit`函数的关键步骤：

![](img/d45538871842fead0e7072f90f9b175d_23.png)

1.  **准备数据**：需要传入两个指标的数据，分别作为自变量X和因变量Y。
2.  **指定阶数**：需要指定回归方程的阶数（`deg`参数）。例如，`deg=1`表示构建一元线性回归方程 **Y = kX + b**，其中包含X的一次项和常数项。`deg=2`则表示构建包含X二次项的曲线方程。
3.  **处理数据**：在计算前，必须确保数据中没有空值（NaN），否则会导致计算错误。

![](img/d45538871842fead0e7072f90f9b175d_25.png)

```python
# 假设 df 是包含数据的DataFrame，'X_column' 和 ‘Y_column’ 是两个列名
# 1. 删除空值
df.dropna(inplace=True)

# 2. 提取X和Y数据
x_data = df[‘X_column’].values
y_data = df[‘Y_column’].values

![](img/d45538871842fead0e7072f90f9b175d_27.png)

![](img/d45538871842fead0e7072f90f9b175d_29.png)

![](img/d45538871842fead0e7072f90f9b175d_31.png)

# 3. 使用 polyfit 构建一阶（线性）回归方程，返回系数 [k, b]
coefficients = np.polyfit(x_data, y_data, deg=1)
k, b = coefficients[0], coefficients[1]
print(f“回归方程为：Y = {k:.4f} * X + {b:.4f}”)
```

![](img/d45538871842fead0e7072f90f9b175d_33.png)

## 可视化回归线

![](img/d45538871842fead0e7072f90f9b175d_35.png)

得到回归方程后，我们可以将其可视化，与原始数据散点图叠加，直观地观察拟合效果。

![](img/d45538871842fead0e7072f90f9b175d_37.png)

![](img/d45538871842fead0e7072f90f9b175d_39.png)

以下是绘制散点图和回归线的步骤：

1.  首先绘制原始数据的散点图。
2.  根据回归方程系数，计算出一系列用于绘制直线的预测Y值。
3.  将回归线绘制在同一个图上。

![](img/d45538871842fead0e7072f90f9b175d_41.png)

![](img/d45538871842fead0e7072f90f9b175d_43.png)

![](img/d45538871842fead0e7072f90f9b175d_45.png)

```python
import matplotlib.pyplot as plt

![](img/d45538871842fead0e7072f90f9b175d_47.png)

![](img/d45538871842fead0e7072f90f9b175d_49.png)

# 1. 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(x_data, y_data, s=16, label=‘原始数据’)

![](img/d45538871842fead0e7072f90f9b175d_51.png)

![](img/d45538871842fead0e7072f90f9b175d_53.png)

![](img/d45538871842fead0e7072f90f9b175d_55.png)

# 2. 生成回归线的点
# 利用 poly1d 函数根据系数生成一个多项式函数，方便计算预测值
regression_func = np.poly1d(coefficients)
# 使用原始的 x_data 计算对应的预测 y 值
y_pred = regression_func(x_data)

![](img/d45538871842fead0e7072f90f9b175d_57.png)

![](img/d45538871842fead0e7072f90f9b175d_59.png)

![](img/d45538871842fead0e7072f90f9b175d_61.png)

# 3. 绘制回归线
plt.plot(x_data, y_pred, color=‘red’, linewidth=2, label=‘回归线’)

![](img/d45538871842fead0e7072f90f9b175d_63.png)

![](img/d45538871842fead0e7072f90f9b175d_65.png)

plt.xlabel(‘X 指标’)
plt.ylabel(‘Y 指标’)
plt.title(‘指标关系与回归线’)
plt.legend()
plt.show()
```

![](img/d45538871842fead0e7072f90f9b175d_67.png)

需要注意的是，在金融分析中，用于回归的数据通常是经过预处理的，例如使用收益率而不是原始价格。选择合适的预处理数据对分析结果的合理性至关重要。

![](img/d45538871842fead0e7072f90f9b175d_69.png)

## 计算相关系数

![](img/d45538871842fead0e7072f90f9b175d_71.png)

![](img/d45538871842fead0e7072f90f9b175d_73.png)

除了回归方程，相关系数是衡量两个变量线性关系强度和方向的另一个重要指标。计算相关系数非常简单。

在Pandas中，可以直接调用`.corr()`方法计算整个DataFrame的相关系数矩阵。

![](img/d45538871842fead0e7072f90f9b175d_75.png)

![](img/d45538871842fead0e7072f90f9b175d_77.png)

![](img/d45538871842fead0e7072f90f9b175d_79.png)

```python
# 计算两个指标列的相关系数矩阵
correlation_matrix = df[[‘X_column’， ‘Y_column’]].corr()
print(correlation_matrix)
```
相关系数矩阵是一个对称矩阵，对角线上的值均为1（变量与自身的完全相关），非对角线上的值就是两个变量间的相关系数，其值在-1到1之间。

![](img/d45538871842fead0e7072f90f9b175d_81.png)

## 附加任务：滑动窗口相关系数分析

![](img/d45538871842fead0e7072f90f9b175d_83.png)

为了更深入地分析，我们可以研究相关系数随时间的变化情况。这需要通过滑动窗口来实现。

![](img/d45538871842fead0e7072f90f9b175d_85.png)

核心思路是：随着时间推移，在每一个时间点，取最近一定周期（窗口）的数据，计算该窗口内两个指标的相关系数。这样就能得到一条随时间变化的相关系数曲线。

![](img/d45538871842fead0e7072f90f9b175d_87.png)

![](img/d45538871842fead0e7072f90f9b175d_89.png)

以下是实现步骤：

![](img/d45538871842fead0e7072f90f9b175d_91.png)

![](img/d45538871842fead0e7072f90f9b175d_93.png)

1.  确定滑动窗口的大小（例如250天）。
2.  对其中一个指标序列使用`.rolling()`方法创建滑动窗口。
3.  在每个窗口内，计算该窗口数据与另一个指标对应数据的相关系数。

![](img/d45538871842fead0e7072f90f9b175d_95.png)

```python
# 设置窗口大小
window_size = 250

![](img/d45538871842fead0e7072f90f9b175d_97.png)

![](img/d45538871842fead0e7072f90f9b175d_99.png)

![](img/d45538871842fead0e7072f90f9b175d_101.png)

# 计算指标X_column与Y_column在滑动窗口内的相关系数
rolling_corr = df[‘X_column’].rolling(window=window_size).corr(df[‘Y_column’])

![](img/d45538871842fead0e7072f90f9b175d_103.png)

![](img/d45538871842fead0e7072f90f9b175d_105.png)

# 绘制滑动窗口相关系数变化图
plt.figure(figsize=(12, 6))
rolling_corr.plot(figsize=(12, 6))
plt.axhline(y=0, color=‘black’， linestyle=‘--’， linewidth=0.5) # 添加y=0的参考线
plt.title(f‘{window_size}天滑动窗口相关系数变化’)
plt.ylabel(‘相关系数’)
plt.xlabel(‘时间’)
plt.show()
```
通过这个分析，我们可以观察到两个指标间的关联性在不同市场阶段是增强、减弱还是发生反转，这比单一的全局相关系数包含更多信息。

![](img/d45538871842fead0e7072f90f9b175d_107.png)

![](img/d45538871842fead0e7072f90f9b175d_109.png)

![](img/d45538871842fead0e7072f90f9b175d_111.png)

---

![](img/d45538871842fead0e7072f90f9b175d_113.png)

![](img/d45538871842fead0e7072f90f9b175d_115.png)

本节课中我们一起学习了量化分析中的两个核心关系度量工具：回归方程与相关系数。我们掌握了如何使用`np.polyfit`构建线性回归模型并可视化，以及如何使用`.corr()`计算全局和滑动窗口的相关系数。这些技能是理解金融变量间相互作用的基础，建议大家在理解代码逻辑后，尝试修改参数（如窗口大小、回归阶数）进行练习，以加深理解。