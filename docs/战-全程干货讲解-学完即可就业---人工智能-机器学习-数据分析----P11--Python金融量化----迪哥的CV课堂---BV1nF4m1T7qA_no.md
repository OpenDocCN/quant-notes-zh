# Python金融量化：P11：回归方程与相关系数实例 📈

![](img/22e00a26da919a8f06d174885f2df85e_1.png)

![](img/22e00a26da919a8f06d174885f2df85e_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是金融量化分析中衡量两个变量关系的基础工具。我们将通过实际代码演示，从数据处理到结果可视化，完整地走一遍流程。

![](img/22e00a26da919a8f06d174885f2df85e_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

![](img/22e00a26da919a8f06d174885f2df85e_7.png)

构建回归方程的方法很多，可以使用多种工具包。这里我们选择使用NumPy库来完成。NumPy中有一个`polyfit`函数，可以帮助我们计算回归模型的系数。

![](img/22e00a26da919a8f06d174885f2df85e_9.png)

![](img/22e00a26da919a8f06d174885f2df85e_11.png)

以下是构建回归方程的核心步骤：

![](img/22e00a26da919a8f06d174885f2df85e_13.png)

![](img/22e00a26da919a8f06d174885f2df85e_15.png)

1.  **准备数据**：首先，我们需要两个指标的数据，例如`X`和`Y`。
2.  **调用函数**：使用`np.polyfit(X, Y, deg)`函数。其中`deg`参数指定了回归方程的次数。
    *   `deg=1`：表示构建**一元线性回归**方程，形式为 **Y = kX + b**。
    *   `deg=2`：表示构建**二次多项式回归**方程，形式为 **Y = aX² + bX + c**。
3.  **处理数据问题**：在计算前，必须确保数据中没有空值（NaN），否则会导致计算错误。我们可以使用`dropna()`方法清理数据。

![](img/22e00a26da919a8f06d174885f2df85e_17.png)

![](img/22e00a26da919a8f06d174885f2df85e_19.png)

```python
import numpy as np
import pandas as pd

![](img/22e00a26da919a8f06d174885f2df85e_21.png)

# 假设 df 是一个包含‘X’和‘Y’两列的DataFrame
# 1. 清理数据中的空值
df.dropna(inplace=True)

![](img/22e00a26da919a8f06d174885f2df85e_23.png)

# 2. 提取X和Y的数据
X = df['X'].values
Y = df['Y'].values

# 3. 构建一元线性回归方程 (deg=1)
coefficients = np.polyfit(X, Y, deg=1)
# coefficients 是一个数组，例如 [k, b]，对应 Y = k*X + b
k, b = coefficients
print(f"回归方程为: Y = {k:.4f} * X + {b:.4f}")
```

![](img/22e00a26da919a8f06d174885f2df85e_25.png)

![](img/22e00a26da919a8f06d174885f2df85e_27.png)

---

![](img/22e00a26da919a8f06d174885f2df85e_29.png)

## 可视化回归方程

![](img/22e00a26da919a8f06d174885f2df85e_31.png)

得到回归方程的系数后，我们可以将其可视化，与原始数据点一起绘制在图上，以便直观地观察拟合效果。

![](img/22e00a26da919a8f06d174885f2df85e_33.png)

以下是绘制散点图和回归线的步骤：

![](img/22e00a26da919a8f06d174885f2df85e_35.png)

![](img/22e00a26da919a8f06d174885f2df85e_37.png)

1.  使用Matplotlib绘制原始数据的散点图。
2.  根据回归方程，生成一组对应的预测Y值。
3.  将回归线绘制在同一个图上。

```python
import matplotlib.pyplot as plt

![](img/22e00a26da919a8f06d174885f2df85e_39.png)

![](img/22e00a26da919a8f06d174885f2df85e_41.png)

# 1. 绘制原始数据散点图
plt.figure(figsize=(10, 6))
plt.scatter(X, Y, s=16, label='原始数据点') # s参数控制点的大小

![](img/22e00a26da919a8f06d174885f2df85e_43.png)

![](img/22e00a26da919a8f06d174885f2df85e_45.png)

# 2. 生成回归线的点
# 创建一组X值（通常使用最小值和最大值来画线）
X_fit = np.linspace(X.min(), X.max(), 100)
# 根据方程 Y = kX + b 计算对应的Y值
Y_fit = k * X_fit + b

![](img/22e00a26da919a8f06d174885f2df85e_47.png)

# 3. 绘制回归线
plt.plot(X_fit, Y_fit, color='red', linewidth=2, label='回归线')

![](img/22e00a26da919a8f06d174885f2df85e_49.png)

![](img/22e00a26da919a8f06d174885f2df85e_51.png)

plt.xlabel('X 指标')
plt.ylabel('Y 指标')
plt.title('指标关系与回归线')
plt.legend()
plt.grid(True)
plt.show()
```

![](img/22e00a26da919a8f06d174885f2df85e_53.png)

![](img/22e00a26da919a8f06d174885f2df85e_55.png)

通过这张图，我们可以一目了然地看到两个指标之间的关系以及回归线对数据的拟合情况。

![](img/22e00a26da919a8f06d174885f2df85e_57.png)

![](img/22e00a26da919a8f06d174885f2df85e_59.png)

---

![](img/22e00a26da919a8f06d174885f2df85e_61.png)

![](img/22e00a26da919a8f06d174885f2df85e_63.png)

## 计算相关系数

![](img/22e00a26da919a8f06d174885f2df85e_65.png)

除了回归方程，相关系数是另一个衡量两个变量线性关系强度和方向的常用指标。在Pandas中，计算相关系数非常简单。

以下是计算相关系数的方法：

![](img/22e00a26da919a8f06d174885f2df85e_67.png)

![](img/22e00a26da919a8f06d174885f2df85e_69.png)

```python
# 计算DataFrame中所有列两两之间的相关系数矩阵
correlation_matrix = df.corr()
print(correlation_matrix)

![](img/22e00a26da919a8f06d174885f2df85e_71.png)

# 如果只想计算两个特定列（如‘X’和‘Y’）的相关系数
corr_xy = df['X'].corr(df['Y'])
print(f"X与Y的相关系数为: {corr_xy:.4f}")
```
相关系数矩阵是一个对称矩阵，对角线上的值均为1（表示自己与自己的完全相关）。相关系数的值介于-1到1之间，越接近1表示正相关性越强，越接近-1表示负相关性越强，接近0则表示线性关系很弱。

---

![](img/22e00a26da919a8f06d174885f2df85e_73.png)

![](img/22e00a26da919a8f06d174885f2df85e_75.png)

![](img/22e00a26da919a8f06d174885f2df85e_77.png)

## 进阶应用：滑动窗口相关系数

![](img/22e00a26da919a8f06d174885f2df85e_79.png)

在金融时间序列分析中，我们常常关心关系是否随时间变化。这时，可以计算滑动窗口内的相关系数。

![](img/22e00a26da919a8f06d174885f2df85e_81.png)

**任务描述**：随着时间推移，计算两个指标在固定时间窗口内的相关系数变化情况。

以下是实现思路和代码：

![](img/22e00a26da919a8f06d174885f2df85e_83.png)

![](img/22e00a26da919a8f06d174885f2df85e_85.png)

1.  **定义窗口**：例如，定义一个包含250个数据点的窗口。
2.  **滑动计算**：让这个窗口在时间序列上滑动，在每个窗口位置，计算窗口内两个指标的相关系数。
3.  **结果可视化**：将计算出的相关系数序列绘制成图，观察其随时间的变化趋势。

![](img/22e00a26da919a8f06d174885f2df85e_87.png)

```python
# 假设有两个时间序列数据列 ‘stock_A’ 和 ‘stock_B’
window_size = 250 # 窗口大小设为250个交易日（约一年）

![](img/22e00a26da919a8f06d174885f2df85e_89.png)

# 计算‘stock_A’与‘stock_B’在滑动窗口内的相关系数
rolling_corr = df['stock_A'].rolling(window=window_size).corr(df['stock_B'])

![](img/22e00a26da919a8f06d174885f2df85e_91.png)

![](img/22e00a26da919a8f06d174885f2df85e_93.png)

# 绘制滑动窗口相关系数变化图
plt.figure(figsize=(12, 6))
rolling_corr.plot()
plt.title(f'滑动窗口（大小={window_size}）相关系数变化图')
plt.xlabel('时间')
plt.ylabel('相关系数')
plt.axhline(y=0, color='black', linestyle='--', linewidth=0.5) # 添加y=0的参考线
plt.grid(True)
plt.show()
```
这张图可以揭示两个资产间的关联性是如何动态演变的，对于策略构建和风险控制非常有价值。

![](img/22e00a26da919a8f06d174885f2df85e_95.png)

---

![](img/22e00a26da919a8f06d174885f2df85e_97.png)

![](img/22e00a26da919a8f06d174885f2df85e_99.png)

## 总结与练习建议

![](img/22e00a26da919a8f06d174885f2df85e_101.png)

![](img/22e00a26da919a8f06d174885f2df85e_103.png)

本节课中我们一起学习了金融量化分析中的两个核心工具：**回归方程**与**相关系数**。

![](img/22e00a26da919a8f06d174885f2df85e_105.png)

*   **回归方程**（使用`np.polyfit`）用于量化并预测一个变量如何随另一个变量变化。
*   **相关系数**（使用`.corr()`）用于快速衡量两个变量线性关系的强度和方向。
*   **滑动窗口相关系数**（使用`.rolling().corr()`）帮助我们分析变量间关系的动态特性。

![](img/22e00a26da919a8f06d174885f2df85e_107.png)

![](img/22e00a26da919a8f06d174885f2df85e_109.png)

建议大家在看完课程后，亲自动手完成以下练习，以巩固理解：
*   使用自己的数据或不同的股票代码重复上述分析。
*   尝试改变回归方程的阶数（如`deg=2`），观察拟合效果。
*   调整滑动窗口的大小（如改为50或500），观察相关系数变化图的差异。
*   思考在构建回归模型前，对增长率数据或价格数据分别进行建模有何不同含义。

![](img/22e00a26da919a8f06d174885f2df85e_111.png)

![](img/22e00a26da919a8f06d174885f2df85e_113.png)

通过反复练习和修改参数，你将能更深刻地掌握这些工具在金融数据分析中的应用。