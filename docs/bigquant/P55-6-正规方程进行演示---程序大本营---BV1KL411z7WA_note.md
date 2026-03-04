# 机器学习：P55：正规方程实战演示 🧮

![](img/737b71341ec83c572026f78f9eb9fe9d_1.png)

在本节课中，我们将学习如何使用正规方程（Normal Equation）来求解线性回归问题。我们将通过两个具体的代码示例，从简单的一元线性回归到稍复杂的多元线性回归，直观地展示正规方程的应用过程，并理解其背后的数学原理。

上一节我们介绍了正规方程的理论基础，本节中我们来看看如何用代码实现它。

![](img/737b71341ec83c572026f78f9eb9fe9d_3.png)

## 正规方程的理论前提与数据分布

如果数据不符合高斯（正态）分布，使用基于最小二乘法原理的正规方程进行计算，结果可能不准确。

![](img/737b71341ec83c572026f78f9eb9fe9d_1.png)

大部分数学或算法问题中，许多数据都服从正态分布，例如身高、体重、智商等。

但人类社会中有一样东西不是正态分布。

![](img/737b71341ec83c572026f78f9eb9fe9d_3.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_5.png)

财富的分布不是正态分布。财富与智商或年龄没有简单的线性关系。这提醒我们，分析问题需要依靠科学方法，而非个别案例。

![](img/737b71341ec83c572026f78f9eb9fe9d_7.png)

持续学习是积累知识、提升能力的可靠途径。学习应像流水一样，注重长期坚持而非短期得失。

![](img/737b71341ec83c572026f78f9eb9fe9d_5.png)

![](img/737b71341ec83c572026f9eb9fe9d_7.png)

使用科学统计方法（如理解正态分布）能帮助我们更客观地认识世界，避免被少数特例误导。

理解了数据分布与线性回归的关系后，我们就可以开始使用正规方程进行实际操作了。

## 使用正规方程求解简单线性回归

![](img/737b71341ec83c572026f78f9eb9fe9d_9.png)

我们将同时用代码实现计算，并将结果图形化展示出来。

![](img/737b71341ec83c572026f78f9eb9fe9d_9.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_11.png)

正规方程已在上一讲的代码中讲解过，现在我们再操作一遍。首先实现一个简单的线性回归模型：`y = w * x + b`。

![](img/737b71341ec83c572026f78f9eb9fe9d_13.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_11.png)

以下是实现简单线性回归的完整代码步骤：

```python
import numpy as np
import matplotlib.pyplot as plt

# 1. 创建数据
np.random.seed(42)
x = np.linspace(0, 10, 30).reshape(-1, 1)  # 转换为二维数组，满足算法要求
w_true = 1.0  # 真实斜率
b_true = 5.0  # 真实截距
y_true = w_true * x + b_true

# 2. 添加噪声，模拟真实数据
noise = np.random.randn(30, 1) * 1.5
y = y_true + noise

# 3. 绘制原始数据散点图
plt.scatter(x, y, label='Data with noise')
plt.plot(x, y_true, color='red', label='True line')
plt.legend()
plt.show()

# 4. 使用正规方程求解参数
# 为系数w0（即截距b）添加一列全为1的特征
X_b = np.c_[np.ones((30, 1)), x]  # 构造 X 矩阵，包含 x0=1
# 正规方程公式: theta = (X^T * X)^(-1) * X^T * y
theta_best = np.linalg.inv(X_b.T.dot(X_b)).dot(X_b.T).dot(y)

# 5. 提取求解出的斜率和截距
b_pred, w_pred = theta_best.flatten()
print(f"求解的截距 b: {b_pred:.2f}, 斜率 w: {w_pred:.2f}")
print(f"真实的截距 b: {b_true}, 斜率 w: {w_true}")

# 6. 绘制拟合直线
y_pred = X_b.dot(theta_best)
plt.scatter(x, y, label='Data with noise')
plt.plot(x, y_true, color='red', label='True line')
plt.plot(x, y_pred, color='green', label='Fitted line')
plt.legend()
plt.show()
```

**代码关键点解析：**
*   `x.reshape(-1, 1)`：将一维数据转换为二维矩阵，因为算法要求输入特征为二维形式。`-1`表示自动计算该维度大小，`1`表示每行只有一个特征。
*   构造 `X_b` 矩阵：通过 `np.c_` 拼接一列全为1的向量，对应线性方程中的截距项 `b`（即 `w0`）。
*   **正规方程核心公式**：
    `theta = (X^T * X)^(-1) * X^T * y`
    其中 `X` 是包含 `x0=1` 的特征矩阵，`y` 是目标值向量，`theta` 是包含截距和斜率的参数向量。

![](img/737b71341ec83c572026f78f9eb9fe9d_15.png)

执行代码后，可以看到算法求解出的直线（绿色）非常接近真实直线（红色），并且穿过了带有噪声的数据点。由于数据中添加了噪声，求解结果 `(w_pred, b_pred)` 与真实值 `(w_true, b_true)` 非常接近但并非完全相等，这证明了正规方程在存在噪声时的有效性。

![](img/737b71341ec83c572026f78f9eb9fe9d_15.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_17.png)

以上便是使用正规方程解决简单线性回归问题的全过程。

## 使用正规方程求解多元线性回归

接下来，我们看一个稍复杂一点的例子：多元线性回归。我们以二元回归为例，以便进行三维可视化。

![](img/737b71341ec83c572026f78f9eb9fe9d_19.png)

模型形式为：`y = w1 * x1 + w2 * x2`

![](img/737b71341ec83c572026f78f9eb9fe9d_21.png)

以下是实现多元线性回归的代码：

```python
import numpy as np
import matplotlib.pyplot as plt

![](img/737b71341ec83c572026f78f9eb9fe9d_17.png)

# 1. 创建二元数据
np.random.seed(42)
x1 = np.random.randint(-150, 150, 100)  # 特征1
x2 = np.random.randint(0, 300, 100)     # 特征2
w1_true, w2_true = 2.0, 3.0             # 真实系数

![](img/737b71341ec83c572026f78f9eb9fe9d_19.png)

# 2. 根据方程计算目标值并添加噪声
y_true = w1_true * x1 + w2_true * x2
noise = np.random.randn(100) * 20       # 添加噪声
y = y_true + noise

![](img/737b71341ec83c572026f78f9eb9fe9d_21.png)

# 3. 重新构造特征矩阵X，包含一列全1（对应截距，但本例模型无截距）
# 为了通用性，这里仍添加截距项，方程变为 y = w0 + w1*x1 + w2*x2
X = np.c_[np.ones(100), x1, x2]

# 4. 使用正规方程求解参数 (w0, w1, w2)
theta_best = np.linalg.inv(X.T.dot(X)).dot(X.T).dot(y)
print(f"求解的参数: {theta_best}")

# 5. 三维可视化
fig = plt.figure()
# 创建3D坐标轴
ax = fig.add_subplot(111, projection='3d')

# 绘制原始数据点
ax.scatter(x1, x2, y, c='b', marker='o', label='Data Points')

# 根据求解的参数生成拟合平面
x1_grid, x2_grid = np.meshgrid(np.linspace(-150, 150, 10), np.linspace(0, 300, 10))
y_grid = theta_best[0] + theta_best[1] * x1_grid + theta_best[2] * x2_grid
ax.plot_surface(x1_grid, x2_grid, y_grid, alpha=0.5, color='r', label='Fitted Plane')

ax.set_xlabel('X1')
ax.set_ylabel('X2')
ax.set_zlabel('Y')
plt.legend()
plt.show()
```

![](img/737b71341ec83c572026f78f9eb9fe9d_23.png)

**代码关键点解析：**
*   本例生成了两个特征 `x1` 和 `x2`。
*   构造特征矩阵 `X` 时，同样需要添加一列全1，对应参数向量中的 `w0`（截距）。
*   求解过程使用的**正规方程公式**与一元情况完全相同：`theta = (X^T * X)^(-1) * X^T * y`。
*   使用 `matplotlib` 的 `3d` 投影进行三维绘图，散点代表原始数据，红色平面代表拟合出的回归平面。

![](img/737b71341ec83c572026f78f9eb9fe9d_25.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_23.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_25.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_27.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_27.png)

执行代码后，可以在三维空间中看到数据点与拟合平面的关系。求解出的参数接近真实系数，拟合平面较好地捕捉了数据的整体趋势。

![](img/737b71341ec83c572026f78f9eb9fe9d_29.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_31.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_29.png)

对于高维数据（特征数量 > 3），我们无法进行直观的可视化，但正规方程的求解过程是完全一致的。

![](img/737b71341ec83c572026f78f9eb9fe9d_31.png)

![](img/737b71341ec83c572026f78f9eb9fe9d_33.png)

---

**本节课总结**

在本节课中，我们一起学习了：
1.  **正规方程的应用前提**：数据最好符合或近似正态分布，以保证最小二乘法估计的有效性。
2.  **简单线性回归的实战**：通过生成带噪声的一元数据，使用正规方程公式 `theta = (X^T * X)^(-1) * X^T * y` 求解出了直线的斜率和截距，并进行了可视化对比。
3.  **多元线性回归的实战**：以二元回归为例，演示了如何对多特征数据应用正规方程，并通过三维图形展示了数据点与拟合平面之间的关系。
4.  **核心代码实践**：掌握了使用 `numpy` 构造特征矩阵、实现正规方程计算以及用 `matplotlib` 进行结果可视化的完整流程。

![](img/737b71341ec83c572026f78f9eb9fe9d_33.png)

正规方程提供了一种直接求解线性回归参数的解析方法，对于特征数量不是特别大的问题非常有效。