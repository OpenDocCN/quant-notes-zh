# 机器学习：P105：损失函数立体化呈现（一） 📊

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_0.png)

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_2.png)

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_4.png)

在本节课中，我们将学习如何通过代码将逻辑斯蒂回归的损失函数进行立体化呈现，直观地观察其形状，并理解梯度下降过程所对应的函数特性。

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_6.png)

---

## 导入必要的库

首先，我们需要导入实现功能所需的Python库。

```python
from sklearn import datasets
from sklearn.linear_model import LogisticRegression
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D
from sklearn.preprocessing import StandardScaler
```

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_8.png)

---

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_10.png)

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_12.png)

## 加载与处理数据

上一节我们导入了必要的工具包，本节中我们来看看如何加载和处理数据。

以下是加载乳腺癌数据集的步骤：
*   使用 `datasets.load_breast_cancer(return_X_y=True)` 加载数据，该函数返回特征数据 `X` 和标签 `y`。
*   标签 `y` 中的0代表健康样本，1代表患病样本。
*   为了便于后续的可视化，我们只选取前两个特征进行分析。

```python
# 加载数据
X, y = datasets.load_breast_cancer(return_X_y=True)
# 查看数据形状
print(X.shape, y.shape)
# 仅选取前两个特征用于画图
X = X[:, :2]
```

---

## 训练逻辑斯蒂回归模型

数据准备就绪后，接下来我们使用逻辑斯蒂回归模型进行训练。

以下是建模的关键步骤：
*   实例化一个 `LogisticRegression` 模型。
*   使用 `fit` 方法，传入特征 `X` 和标签 `y` 来训练模型。
*   训练完成后，模型会学习到数据中的规律。

```python
# 实例化并训练模型
model = LogisticRegression()
model.fit(X, y)
```

---

## 提取模型参数

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_14.png)

模型训练完成后，我们可以提取其中的关键参数，即线性方程的系数和截距。

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_16.png)

以下是提取参数的代码：
*   使用 `model.coef_` 获取特征系数。
*   使用 `model.intercept_` 获取截距项。

```python
# 提取系数 w1, w2 和截距 b
w1 = model.coef_[0, 0]
w2 = model.coef_[0, 1]
b = model.intercept_[0]
print(“方程的系数 w1, w2:”, w1, w2)
print(“方程的截距 b:”, b)
```

---

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_18.png)

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_20.png)

## 定义 Sigmoid 函数

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_22.png)

逻辑斯蒂回归的核心是将线性方程的输出通过 Sigmoid 函数转换为概率。现在我们来定义这个函数。

Sigmoid 函数的公式为：
**`σ(z) = 1 / (1 + e^{-z})`**
其中，`z = w1*x1 + w2*x2 + b`。

```python
def sigmoid(x, w1, w2, b):
    # 计算线性部分 z
    z = w1 * x[0] + w2 * x[1] + b
    # 将 z 转换为概率
    return 1 / (1 + np.exp(-z))
```

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_24.png)

---

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_26.png)

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_28.png)

## 定义损失函数

定义了Sigmoid函数后，我们需要根据逻辑斯蒂回归的损失函数公式来计算整体损失。

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_30.png)

逻辑斯蒂回归的损失函数（对数损失）公式为：
**`J(θ) = -1/m * Σ [y⁽ⁱ⁾ * log(hθ(x⁽ⁱ⁾)) + (1 - y⁽ⁱ⁾) * log(1 - hθ(x⁽ⁱ⁾))]`**
其中 `hθ(x)` 即 Sigmoid 函数的输出。

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_32.png)

```python
def loss_function(X, y, w1, w2, b):
    total_loss = 0
    for x_i, y_i in zip(X, y):
        # 计算预测概率 p
        p = sigmoid(x_i, w1, w2, b)
        # 使用 clip 防止 log(0) 导致计算错误
        p = np.clip(p, 1e-7, 1 - 1e-7)
        # 根据公式计算单个样本的损失并累加
        loss_i = -(y_i * np.log(p) + (1 - y_i) * np.log(1 - p))
        total_loss += loss_i
    return total_loss
```

---

## 准备参数空间以绘制损失函数曲面

为了绘制损失函数的三维曲面图，我们需要为系数 `w1` 和 `w2` 创建一个取值网格。

以下是创建参数网格的步骤：
*   以训练得到的 `w1` 和 `w2` 为中心，在其前后各取一定范围（例如 ±2），生成一系列数值。
*   使用 `np.linspace` 在这个范围内生成100个点。
*   计算网格中每一对 `(w1, w2)` 取值所对应的损失值。

```python
# 创建 w1 和 w2 的取值空间
w1_space = np.linspace(w1 - 2, w1 + 2, 100)
w2_space = np.linspace(w2 - 2, w2 + 2, 100)

# 计算损失值网格
loss_values = np.array([[loss_function(X, y, i, j, b) for j in w2_space] for i in w1_space])
```

---

## 三维可视化损失函数

所有数据准备完毕，最后一步是将损失函数以三维曲面的形式呈现出来。

以下是绘制三维图的代码：
*   创建一个三维坐标系。
*   使用 `plot_surface` 方法绘制 `(w1, w2, loss)` 构成的曲面。
*   添加坐标轴标签和图形标题。

```python
# 创建三维图形
fig = plt.figure(figsize=(10, 8))
ax = fig.add_subplot(111, projection=‘3d’)

# 生成网格坐标
W1, W2 = np.meshgrid(w1_space, w2_space)

# 绘制曲面
surf = ax.plot_surface(W1, W2, loss_values.T, cmap=‘viridis’, alpha=0.8)
fig.colorbar(surf, shrink=0.5, aspect=5) # 添加颜色条

# 标记最优解（模型训练得到的参数点）
ax.scatter(w1, w2, loss_function(X, y, w1, w2, b), color=‘red’, s=100, label=‘Optimal Point’)

# 设置标签和标题
ax.set_xlabel(‘Weight w1’)
ax.set_ylabel(‘Weight w2’)
ax.set_zlabel(‘Loss’)
ax.set_title(‘3D Visualization of Logistic Regression Loss Function’)
ax.legend()

plt.show()
```

---

![](img/5514eba0bd6b5d3d47392abe7e86ed2d_34.png)

本节课中我们一起学习了如何将逻辑斯蒂回归的损失函数进行立体化呈现。我们完成了从数据加载、模型训练、参数提取，到自定义损失函数，并最终绘制出损失函数三维曲面的全过程。通过可视化，我们可以直观地看到损失函数的形状，并理解梯度下降算法寻找最小值（最优参数）的过程。图中红色的点即为模型找到的最优解所在位置。