# 机器学习：P72：批量梯度下降代码演示 🧑‍💻

![](img/95ef572d3354d42793afb8df4305b749_1.png)

![](img/95ef572d3354d42793afb8df4305b749_2.png)

![](img/95ef572d3354d42793afb8df4305b749_4.png)

在本节课中，我们将学习如何使用Python代码实现批量梯度下降算法，以求解一元线性回归问题。我们将从创建模拟数据开始，逐步编写梯度下降的核心代码，并探讨如何通过调整学习率来优化算法。

![](img/95ef572d3354d42793afb8df4305b749_6.png)

![](img/95ef572d3354d42793afb8df4305b749_8.png)

---

![](img/95ef572d3354d42793afb8df4305b749_10.png)

## 1. 准备环境与数据 📊

首先，我们需要导入必要的库并创建用于训练的数据。我们将使用`numpy`生成数据，并使用`matplotlib`进行可视化。

```python
import numpy as np
import matplotlib.pyplot as plt
```

接下来，我们创建模拟数据。我们生成一个特征`x`，并根据线性方程 `y = w*x + b` 加上一些随机噪声来生成目标值`y`。

```python
# 创建特征数据 x
x = np.random.rand(100, 1)

# 定义真实的权重 w 和偏置 b
w, b = np.random.randint(1, 10, size=2)

# 根据线性方程生成目标值 y，并添加噪声
y = w * x + b + np.random.randn(100, 1)
```

为了更直观地理解数据，我们可以将其可视化。

```python
plt.scatter(x, y)
plt.xlabel('x')
plt.ylabel('y')
plt.title('模拟数据散点图')
plt.show()
```

---

## 2. 数据预处理与参数初始化 ⚙️

在应用梯度下降算法之前，我们需要对数据进行预处理，并初始化算法所需的参数。

上一节我们创建了数据，本节中我们来看看如何为梯度下降准备数据并设置初始参数。

首先，为了将偏置项 `b` 也纳入权重向量中统一求解，我们需要在特征 `x` 后添加一列全为1的值。

```python
# 在 x 后添加一列全为1的值，用于计算偏置项
x_with_bias = np.c_[x, np.ones((100, 1))]
```

接下来，我们初始化算法参数。这包括设置迭代次数、初始学习率以及随机初始化的权重向量 `theta`。

```python
# 设置迭代次数
epochs = 10000

# 设置初始学习率
eta = 0.01

# 随机初始化权重向量 theta (包含 w 和 b)
theta = np.random.randn(2, 1)
```

---

## 3. 实现批量梯度下降算法 🔄

现在，我们进入核心部分：实现批量梯度下降算法。算法的核心思想是迭代地调整参数 `theta`，以最小化损失函数。

以下是批量梯度下降的更新公式：

**公式：** `theta = theta - eta * gradient`

其中，梯度 `gradient` 的计算公式为：

**公式：** `gradient = (1/m) * X.T.dot(X.dot(theta) - y)`

在代码中，我们将其实现如下：

```python
# 开始梯度下降迭代
for epoch in range(epochs):
    # 计算梯度
    gradients = x_with_bias.T.dot(x_with_bias.dot(theta) - y) / len(x)
    # 更新参数 theta
    theta = theta - eta * gradients
```

迭代完成后，我们可以打印出求解得到的参数，并与真实的参数进行比较。

```python
print(f"真实的斜率(w)和截距(b): [{w}, {b}]")
print(f"BGD求解的斜率(w)和截距(b): {theta.flatten()}")
```

---

## 4. 学习率衰减优化 🎯

固定学习率有时可能导致在接近最优解时收敛缓慢或震荡。为了解决这个问题，我们可以引入学习率衰减策略，例如逆时衰减。

上一节我们实现了基础的梯度下降，本节中我们来看看如何通过动态调整学习率来优化收敛过程。

逆时衰减的公式如下，其中 `t` 是当前迭代次数：

**公式：** `learning_rate = initial_rate / (1 + decay_rate * t)`

我们在代码中实现一个简单的学习率调度函数：

```python
def learning_rate_schedule(t, initial_rate=5, decay_rate=1000):
    return initial_rate / (decay_rate + t)
```

然后，在梯度下降循环中动态计算并应用学习率：

```python
# 初始化迭代计数器
t = 0

for epoch in range(epochs):
    # 动态计算当前迭代的学习率
    current_eta = learning_rate_schedule(t)
    t += 1

    # 计算梯度并更新参数
    gradients = x_with_bias.T.dot(x_with_bias.dot(theta) - y) / len(x)
    theta = theta - current_eta * gradients
```

这种方法可以确保在训练初期使用较大的学习率快速下降，在后期使用较小的学习率精细调整，有助于算法更稳定地收敛到最优解。

---

## 5. 总结 📝

本节课中我们一起学习了批量梯度下降算法的完整代码实现。我们从创建模拟数据开始，经历了数据预处理、参数初始化、核心梯度下降循环的实现，最后还探讨了通过学习率衰减来优化算法性能的方法。

关键点总结：
1.  **数据准备**：生成数据并添加偏置项以便统一求解。
2.  **算法核心**：理解并实现了梯度下降的参数更新公式。
3.  **优化技巧**：引入了学习率衰减策略，使收敛过程更稳定。

![](img/95ef572d3354d42793afb8df4305b749_12.png)

通过本教程，你应该能够理解批量梯度下降的基本步骤，并具备使用Python手动实现该算法来解决简单线性回归问题的能力。