# 机器学习：P127：支持向量机原理可视化 🎯

![](img/02b5e314afafa7c30d4b02422a77139f_1.png)

在本节课中，我们将通过一个具体的编程案例，可视化地探索支持向量机（SVM）的工作原理。我们将从创建数据开始，训练一个SVM模型，并最终通过二维和三维图形展示模型是如何将数据分类的。

## 1. 创建数据 📊

首先，我们需要创建一组用于训练和可视化的数据。我们将生成一组二维的随机点，并根据它们所在的象限为其分配类别标签。

以下是创建数据的步骤：

```python
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D
from sklearn.svm import SVC

# 设置随机种子，确保每次生成的数据相同
np.random.seed(256)
rs = np.random.RandomState(256)

# 生成300个二维数据点，范围大致在[-3, 3]之间
X = rs.randn(300, 2)

# 根据点的象限创建目标值y
# 规则：第一象限和第三象限（x1*x2 > 0）为类别1，第二和第四象限（x1*x2 < 0）为类别0
y = [1 if i > 0 else 0 for i in X[:, 0] * X[:, 1]]

# 绘制原始数据散点图，用颜色区分类别
plt.figure(figsize=(6, 6))
plt.scatter(X[:, 0], X[:, 1], c=y)
plt.title('原始数据分布')
plt.show()
```

## 2. 建模与训练 🤖

上一节我们创建了数据，本节中我们来看看如何使用支持向量机算法来学习数据中的规律。

![](img/02b5e314afafa7c30d4b02422a77139f_3.png)

我们使用Scikit-learn库中的SVC类，并选择径向基函数（RBF）作为核函数。

![](img/02b5e314afafa7c30d4b02422a77139f_5.png)

![](img/02b5e314afafa7c30d4b02422a77139f_7.png)

![](img/02b5e314afafa7c30d4b02422a77139f_9.png)

```python
# 创建SVM模型，使用RBF核函数
model = SVC(kernel='rbf')
# 使用数据训练模型
model.fit(X, y)
```

## 3. 创建测试网格 🔍

为了可视化模型的决策边界，我们需要在整个数据范围内创建一个密集的网格点作为测试数据。

![](img/02b5e314afafa7c30d4b02422a77139f_11.png)

以下是创建测试网格的步骤：

![](img/02b5e314afafa7c30d4b02422a77139f_13.png)

```python
# 在x和y轴上分别生成一系列点
x1 = np.linspace(-3, 3, 200)  # 生成200个点
x2 = np.linspace(-3, 3, 180)  # 生成180个点

# 使用meshgrid生成网格坐标矩阵
X1, X2 = np.meshgrid(x1, x2)

![](img/02b5e314afafa7c30d4b02422a77139f_15.png)

![](img/02b5e314afafa7c30d4b02422a77139f_17.png)

# 将网格矩阵展平并合并，形成测试数据集
X_test = np.column_stack([X1.ravel(), X2.ravel()])
```

![](img/02b5e314afafa7c30d4b02422a77139f_19.png)

![](img/02b5e314afafa7c30d4b02422a77139f_21.png)

## 4. 模型预测与可视化 🎨

![](img/02b5e314afafa7c30d4b02422a77139f_23.png)

![](img/02b5e314afafa7c30d4b02422a77139f_25.png)

![](img/02b5e314afafa7c30d4b02422a77139f_27.png)

现在，我们使用训练好的模型对密集的测试网格进行预测，并将预测结果可视化出来。

![](img/02b5e314afafa7c30d4b02422a77139f_29.png)

![](img/02b5e314afafa7c30d4b02422a77139f_31.png)

![](img/02b5e314afafa7c30d4b02422a77139f_33.png)

![](img/02b5e314afafa7c30d4b02422a77139f_35.png)

```python
# 使用模型进行预测
y_predict = model.predict(X_test)

# 绘制预测结果
plt.figure(figsize=(6, 6))
plt.scatter(X_test[:, 0], X_test[:, 1], c=y_predict, alpha=0.6)
plt.title('SVM模型预测结果（二维视图）')
plt.show()
```

![](img/02b5e314afafa7c30d4b02422a77139f_37.png)

## 5. 算法原理可视化：决策函数与距离 📐

![](img/02b5e314afafa7c30d4b02422a77139f_39.png)

支持向量机的核心在于找到一个“超平面”来分隔数据。对于非线性数据（如本例），核函数（如RBF）通过将数据映射到高维空间来实现分离。`decision_function`方法可以计算每个点到这个决策超平面的“距离”。

```python
# 计算每个测试点到决策超平面的有符号距离
# 正值表示在超平面的一侧，负值表示在另一侧
d = model.decision_function(X_test)

# 将距离数组重塑为与网格X1, X2相同的形状
D = d.reshape(X1.shape)

# 绘制决策函数的等高线（二维轮廓）
plt.figure(figsize=(6, 6))
plt.contour(X1, X2, D, levels=10, colors='k', linestyles='--', alpha=0.5)
plt.contourf(X1, X2, D, levels=10, alpha=0.8) # 填充轮廓
plt.scatter(X[:, 0], X[:, 1], c=y, edgecolors='k') # 叠加原始数据点
plt.title('决策函数等高线图')
plt.colorbar(label='到超平面的距离')
plt.show()
```

## 6. 3D可视化：理解“升维”过程 🚀

![](img/02b5e314afafa7c30d4b02422a77139f_41.png)

二维视图展示了分类结果，但3D视图能更直观地揭示SVM RBF核的工作原理：它实际上将数据“抬升”到了一个三维空间，使得原本在二维平面无法线性分割的数据，可以通过一个平面（即超平面）分开。

![](img/02b5e314afafa7c30d4b02422a77139f_43.png)

```python
# 创建3D图形
fig = plt.figure(figsize=(12, 9))
ax = fig.add_subplot(111, projection='3d')

![](img/02b5e314afafa7c30d4b02422a77139f_45.png)

![](img/02b5e314afafa7c30d4b02422a77139f_47.png)

# 绘制决策函数曲面
surf = ax.plot_surface(X1, X2, D, cmap='coolwarm', alpha=0.8, linewidth=0)

# 绘制原始数据点，在3D空间中的高度即为其决策函数值
ax.scatter(X[:, 0], X[:, 1], model.decision_function(X), c=y, depthshade=True, s=30, edgecolors='k')

![](img/02b5e314afafa7c30d4b02422a77139f_49.png)

![](img/02b5e314afafa7c30d4b02422a77139f_51.png)

![](img/02b5e314afafa7c30d4b02422a77139f_53.png)

![](img/02b5e314afafa7c30d4b02422a77139f_55.png)

ax.set_xlabel('X1')
ax.set_ylabel('X2')
ax.set_zlabel('Decision Function')
ax.set_title('SVM决策函数3D视图')
ax.view_init(elev=40, azim=-60) # 调整视角
plt.colorbar(surf, ax=ax, shrink=0.5, aspect=10, label='到超平面的距离')
plt.show()
```

![](img/02b5e314afafa7c30d4b02422a77139f_57.png)

![](img/02b5e314afafa7c30d4b02422a77139f_59.png)

在3D图中，你可以清晰地看到：
*   属于类别1（一三象限）的数据点被“抬升”到了正值区域（曲面凸起部分）。
*   属于类别0（二四象限）的数据点被“压制”到了负值区域（曲面凹陷部分）。
*   决策超平面就是这个三维空间中`Z=0`的平面。在三维空间里，这个平面可以完美地将两类点分开。

![](img/02b5e314afafa7c30d4b02422a77139f_61.png)

## 总结 📝

![](img/02b5e314afafa7c30d4b02422a77139f_63.png)

本节课中我们一起学习了支持向量机原理的可视化过程。我们从创建简单的象限分类数据开始，训练了一个RBF核的SVM模型。通过生成测试网格和计算决策函数，我们先后绘制了：
1.  **模型预测的二维分类图**，直观看到分类效果。
2.  **决策函数的等高线图**，展示了模型认为的“边界”区域。
3.  **决策函数的3D曲面图**，深刻揭示了RBF核函数通过将数据映射到高维空间（本例中可理解为增加了一个由`x1*x2`计算出的新维度），从而用一个平面（超平面）实现了对非线性数据的完美分割。

![](img/02b5e314afafa7c30d4b02422a77139f_65.png)

![](img/02b5e314afafa7c30d4b02422a77139f_67.png)

![](img/02b5e314afafa7c30d4b02422a77139f_69.png)

![](img/02b5e314afafa7c30d4b02422a77139f_70.png)

这种“升维打击”的策略，正是SVM处理复杂分类问题的核心思想。