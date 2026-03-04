# 机器学习：P116：SVM二分类最大间隔线绘制 🧠

![](img/4b7a4d9230e02fa97c1610d7b6e35c77_1.png)

![](img/4b7a4d9230e02fa97c1610d7b6e35c77_3.png)

在本节课中，我们将学习如何使用Python代码实现支持向量机（SVM）算法，并可视化其核心概念——最大间隔分类超平面及其边界。我们将从创建数据开始，训练一个线性SVM模型，然后手动推导并绘制出决策边界和支持向量边界。

## 导包 📦

首先，我们需要导入必要的Python库。

```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn import svm
from sklearn import datasets
```

## 创建数据 📊

上一节我们导入了必要的工具包，本节中我们来看看如何生成用于演示的模拟数据。我们将使用`sklearn`的`make_blobs`函数来创建两类易于区分的二维数据点，这有助于我们直观地观察SVM的分类效果。

以下是创建数据的步骤：
*   `n_samples=100`：生成100个数据点。
*   `n_features=2`：每个数据点有两个特征，便于在二维平面上绘制。
*   `centers=2`：生成两个簇，即两类数据。
*   `random_state=3`：固定随机种子，确保每次运行代码生成的数据相同。

```python
# 生成模拟数据
X, y = datasets.make_blobs(n_samples=100, n_features=2, centers=2, random_state=3)
```

![](img/4b7a4d9230e02fa97c1610d7b6e35c77_5.png)

![](img/4b7a4d9230e02fa97c1610d7b6e35c77_7.png)

我们可以查看数据的形状和目标值的类别，并用散点图将其可视化。

```python
print(“数据形状：”, X.shape)
print(“目标值类别：”, np.unique(y))

# 绘制数据散点图
plt.scatter(X[:, 0], X[:, 1], c=y)
plt.show()
```

## 算法建模 🤖

数据准备就绪后，接下来我们使用支持向量机算法对数据进行建模。我们将创建一个线性SVM分类器。

以下是建模的关键步骤：
*   初始化一个`SVC`（支持向量分类）对象。
*   指定`kernel=‘linear’`，即使用线性核函数。
*   调用`.fit()`方法，传入特征`X`和目标值`y`来训练模型。

```python
# 创建线性SVM分类器并训练
clf = svm.SVC(kernel=‘linear’)
clf.fit(X, y)

# 评估模型在训练集上的准确率
score = clf.score(X, y)
print(“模型得分：”, score)
```

## 绘制分割线 📐

模型训练完成后，我们获得了决策超平面的参数。对于二维数据，这个超平面就是一条直线。本节我们将推导这条直线的方程并将其绘制出来。

SVM分类器对象中存储了决策函数的系数（`coef_`，即权重向量 **w**）和截距（`intercept_`，即 **b**）。决策函数为：
**f(x) = w · x + b**
其中，**w** 是一个向量，**x** 是输入特征向量。当 **f(x) = 0** 时，就得到了决策边界。

```python
# 获取模型参数：权重向量w和截距b
w = clf.coef_[0]
b = clf.intercept_[0]
print(“权重 w:”, w)
print(“截距 b:”, b)
```

对于二维情况，决策边界直线方程可以表示为：
**w₁x + w₂y + b = 0**
将其转换为标准的直线方程 **y = kx + c** 的形式：
**y = -(w₁ / w₂) * x - (b / w₂)**

根据这个公式，我们可以计算直线的斜率和截距，并生成对应的点进行绘制。

```python
# 计算决策边界的斜率和截距（转换为y=kx+c形式）
slope = -w[0] / w[1]
intercept = -b / w[1]

# 生成x轴坐标点
x_plot = np.linspace(-5, 1, 100)
# 根据直线方程计算对应的y轴坐标点
y_plot = slope * x_plot + intercept

# 绘制原始数据点
plt.scatter(X[:, 0], X[:, 1], c=y)
# 绘制决策边界（最大间隔线）
plt.plot(x_plot, y_plot, color=‘red’, label=‘Decision Boundary’)
plt.legend()
plt.show()
```

## 绘制支持向量边界 🛡️

决策边界位于两类数据“间隔”的正中间，这个间隔由“支持向量”决定。支持向量是距离决策边界最近的那些数据点。接下来，我们找出这些支持向量，并绘制出两条平行的边界线，它们分别穿过两类的支持向量。

我们可以从训练好的模型中获取支持向量。边界线与决策边界平行（即斜率相同），但截距不同。边界线的方程可以表示为：
**w · x + b = ±1**
（在标准SVM推导中，间隔边界对应函数值±1）。

我们可以利用支持向量点来求解边界线的截距。对于一个支持向量点 **(x_sv, y_sv)**，根据边界线方程 **w₁x_sv + w₂y_sv + b_boundary = 0**，可以推导出该边界线的截距 **b_boundary**。

以下是计算和绘制边界线的步骤：
*   从模型中获得所有支持向量。
*   分别选取两类中（通过目标值`y`区分）的支持向量来计算边界线截距。
*   使用相同的斜率和计算出的新截距绘制两条虚线。

```python
# 获取所有支持向量
support_vectors = clf.support_vectors_
print(“支持向量：”, support_vectors)

# 绘制原始数据点和决策边界
plt.scatter(X[:, 0], X[:, 1], c=y)
plt.plot(x_plot, y_plot, color=‘red’, label=‘Decision Boundary’)

# 绘制支持向量点
plt.scatter(support_vectors[:, 0], support_vectors[:, 1], s=100, facecolors=‘none’, edgecolors=‘k’, label=‘Support Vectors’)

# 为每个支持向量计算并绘制一条边界线（方法示例：通过点斜式计算截距）
for sv in support_vectors:
    # 计算通过该支持向量且与决策边界平行的线的截距
    # 由 w·sv + b_boundary = 0 得 b_boundary = -w·sv
    b_boundary = -np.dot(w, sv)
    y_boundary = slope * x_plot + (-b_boundary / w[1]) # 转换为y=kx+c形式
    plt.plot(x_plot, y_boundary, ‘k--’, alpha=0.5) # 用黑色虚线绘制

plt.legend()
plt.show()
```

## 总结 🎯

![](img/4b7a4d9230e02fa97c1610d7b6e35c77_9.png)

本节课中我们一起学习了支持向量机（SVM）的代码实现与可视化。我们从生成模拟的二分类数据集开始，使用`sklearn`训练了一个线性SVM模型。核心部分在于理解并提取模型参数（**w** 和 **b**），手动推导出决策边界直线方程 **y = -(w₁ / w₂)x - (b / w₂)** 并将其绘制出来。最后，我们通过识别“支持向量”，绘制了决定分类间隔的两条边界线，直观地展示了SVM追求“最大间隔”的核心思想。这个过程将算法背后的数学原理与直观的图形结果联系了起来。