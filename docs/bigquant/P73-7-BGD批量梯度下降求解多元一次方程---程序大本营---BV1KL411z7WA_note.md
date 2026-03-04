# 机器学习：P73：批量梯度下降求解多元一次方程 🧮

![](img/b6790dfc3d186d698f70b853cffd8de2_1.png)

![](img/b6790dfc3d186d698f70b853cffd8de2_3.png)

在本节课中，我们将学习如何使用批量梯度下降法来求解一个多元一次方程。我们将从数据准备开始，逐步构建模型，并通过迭代优化参数，最终得到与真实系数接近的解。

![](img/b6790dfc3d186d698f70b853cffd8de2_5.png)

![](img/b6790dfc3d186d698f70b853cffd8de2_7.png)

上一节我们介绍了梯度下降的基本概念，本节中我们来看看如何将其应用于解决更复杂的多元一次方程问题。

![](img/b6790dfc3d186d698f70b853cffd8de2_9.png)

## 数据准备

![](img/b6790dfc3d186d698f70b853cffd8de2_11.png)

![](img/b6790dfc3d186d698f70b853cffd8de2_13.png)

首先，我们需要创建用于训练的数据。这里我们以八元一次方程为例，生成包含噪声的模拟数据。

![](img/b6790dfc3d186d698f70b853cffd8de2_15.png)

```python
import numpy as np

![](img/b6790dfc3d186d698f70b853cffd8de2_17.png)

# 生成特征数据 X：100个样本，8个特征
X = np.random.randint(1, 10, size=(100, 8))

# 生成真实的权重 w（8行1列）和偏置 b
w = np.random.randint(1, 10, size=(8, 1))
b = np.random.randint(1, 10, size=(1,))

![](img/b6790dfc3d186d698f70b853cffd8de2_19.png)

# 根据方程 y = X·w + b + 噪声 生成目标值 y
noise = np.random.randn(100, 1) * 0.1
y = X.dot(w) + b + noise
```

为了将偏置项 `b` 也作为参数进行优化，我们需要对特征数据 `X` 进行扩展，添加一列全为1的特征。

```python
# 在X右侧添加一列全为1的特征，用于合并偏置项
X_with_bias = np.concatenate([X, np.full((100, 1), fill_value=1)], axis=1)
```

## 模型参数初始化

接下来，我们初始化模型参数 `theta`。由于现在特征数量是8个，加上一个偏置项，总共需要9个参数。

```python
# 初始化参数theta，形状为(9, 1)，对应8个权重和1个偏置
theta = np.random.randn(9, 1)
```

## 定义学习率调度器

![](img/b6790dfc3d186d698f70b853cffd8de2_21.png)

为了在训练后期更精细地调整参数，我们使用逆时衰减策略来动态调整学习率。

![](img/b6790dfc3d186d698f70b853cffd8de2_23.png)

```python
def learning_rate_schedule(t):
    """逆时衰减学习率调度器"""
    return 1.0 / (t + 10)
```

![](img/b6790dfc3d186d698f70b853cffd8de2_25.png)

![](img/b6790dfc3d186d698f70b853cffd8de2_27.png)

## 批量梯度下降训练过程

![](img/b6790dfc3d186d698f70b853cffd8de2_29.png)

以下是使用批量梯度下降法进行训练的核心循环。在每次迭代中，我们使用全部数据计算梯度并更新参数。

![](img/b6790dfc3d186d698f70b853cffd8de2_31.png)

```python
# 设置迭代次数
n_iterations = 10000

![](img/b6790dfc3d186d698f70b853cffd8de2_33.png)

for iteration in range(n_iterations):
    # 使用当前参数theta计算预测值
    y_predict = X_with_bias.dot(theta)
    
    # 计算预测值与真实值之间的误差
    error = y_predict - y
    
    # 计算梯度：梯度 = (1/m) * X^T · error
    gradients = (1 / len(X_with_bias)) * X_with_bias.T.dot(error)
    
    # 获取当前迭代的学习率
    learning_rate = learning_rate_schedule(iteration)
    
    # 更新参数：theta = theta - learning_rate * gradients
    theta = theta - learning_rate * gradients
```

![](img/b6790dfc3d186d698f70b853cffd8de2_35.png)

## 结果分析

训练完成后，我们可以查看优化得到的参数 `theta`。

```python
print("优化后的参数 theta:")
print(theta)
```

![](img/b6790dfc3d186d698f70b853cffd8de2_37.png)

输出的 `theta` 向量中，前8个值对应原始方程中8个特征的权重系数，最后一个值对应偏置项 `b`。通过对比可以发现，优化得到的参数值与最初生成数据时使用的真实系数 `w` 和 `b` 非常接近，这证明了批量梯度下降法的有效性。

![](img/b6790dfc3d186d698f70b853cffd8de2_39.png)

本节课中我们一起学习了如何使用批量梯度下降法求解多元一次方程。我们掌握了从数据合成、参数初始化到梯度计算和迭代优化的完整流程。批量梯度下降法通过使用全部数据计算梯度，保证了每次更新的方向相对稳定，是解决线性回归等问题的经典方法。