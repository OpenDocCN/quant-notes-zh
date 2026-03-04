# 机器学习梯度下降法：P76：小批量梯度下降（MBGD）代码演示 🧑‍💻

在本节课中，我们将学习小批量梯度下降法的原理，并通过代码演示其实现过程。这是一种介于批量梯度下降和随机梯度下降之间的优化算法，旨在兼顾计算效率和收敛稳定性。

![](img/6d441805d519c69d789209da6ada8e4f_1.png)

![](img/6d441805d519c69d789209da6ada8e4f_3.png)

上一节我们介绍了随机梯度下降，本节中我们来看看如何实现其折中方案——小批量梯度下降。

![](img/6d441805d519c69d789209da6ada8e4f_5.png)

## 核心原理

![](img/6d441805d519c69d789209da6ada8e4f_7.png)

小批量梯度下降与批量梯度下降类似，但每次迭代并非使用全部数据，而是随机抽取一小批样本计算梯度并更新参数。

## 代码实现步骤

以下是实现小批量梯度下降的主要步骤。

1.  **准备数据与参数**：创建模拟数据，并定义超参数，包括训练轮次、样本总数和小批量大小。
2.  **初始化权重**：为模型参数（如斜率和截距）赋予初始值。
3.  **迭代训练**：
    *   在每轮训练中，先打乱数据顺序。
    *   将数据划分为若干小批量。
    *   对每个小批量计算梯度。
    *   使用计算出的梯度更新模型参数。

## 一元线性回归示例

我们首先以一元一次方程 `y = w*x + b` 为例进行演示。以下代码展示了具体实现。

```python
import numpy as np

# 1. 创建模拟数据
np.random.seed(42)
X = 2 * np.random.rand(100, 1)
y = 7 + 3 * X + np.random.randn(100, 1)

# 2. 定义超参数
epochs = 50          # 训练轮次
n = len(X)           # 样本数量，即100
batch_size = 16      # 小批量大小
n_batches = int(np.ceil(n / batch_size)) # 计算每轮需要的小批量数量，例如 100/16 ≈ 7

# 3. 初始化模型参数 (w: 斜率, b: 截距)
w = np.random.randn(2, 1)  # 包含 w0 (截距) 和 w1 (斜率)

# 4. 定义学习率衰减函数
def learning_rate_schedule(t):
    t0, t1 = 5, 50
    return t0 / (t + t1)

# 5. 小批量梯度下降训练循环
for epoch in range(epochs):
    # 打乱数据顺序
    shuffled_indices = np.random.permutation(n)
    X_shuffled = X[shuffled_indices]
    y_shuffled = y[shuffled_indices]
    
    for i in range(n_batches):
        # 切片操作，获取当前小批量数据
        start_index = i * batch_size
        end_index = min((i + 1) * batch_size, n)
        xi = X_shuffled[start_index:end_index]
        yi = y_shuffled[start_index:end_index]
        
        # 为当前小批量数据添加偏置项 x0 = 1
        xi_b = np.c_[np.ones((len(xi), 1)), xi]
        
        # 计算梯度 (对于线性回归，梯度公式为 (1/m) * X.T * (X*theta - y))
        gradients = (2 / len(xi)) * xi_b.T.dot(xi_b.dot(w) - yi)
        
        # 计算当前迭代的学习率
        t = epoch * n_batches + i  # 全局迭代次数
        eta = learning_rate_schedule(t)
        
        # 更新参数
        w = w - eta * gradients

print(f"求解的斜率 (w1) 和截距 (w0): {w[1][0]:.2f}, {w[0][0]:.2f}")
```

执行上述代码，输出结果会接近真实的斜率 `3` 和截距 `7`。增加训练轮次 `epochs` 可以提高结果的准确性。

## 多元线性回归示例

对于多元一次方程 `y = θ0 + θ1*x1 + θ2*x2 + ...`，实现逻辑完全相同，仅数据构建方式不同。

以下是多元线性回归的示例代码，假设真实参数为 `[2, 3, 4]`。

```python
# 1. 创建多元模拟数据
np.random.seed(42)
X = 2 * np.random.rand(100, 2)  # 两个特征
y = 2 + 3*X[:, 0:1] + 4*X[:, 1:2] + np.random.randn(100, 1)

![](img/6d441805d519c69d789209da6ada8e4f_9.png)

# 2. 初始化模型参数 theta (包含 θ0, θ1, θ2)
theta = np.random.randn(3, 1)

![](img/6d441805d519c69d789209da6ada8e4f_11.png)

# 3. 训练循环 (与一元示例完全相同，仅变量名从 w 改为 theta)
for epoch in range(epochs):
    shuffled_indices = np.random.permutation(n)
    X_shuffled = X[shuffled_indices]
    y_shuffled = y[shuffled_indices]
    
    for i in range(n_batches):
        start_index = i * batch_size
        end_index = min((i + 1) * batch_size, n)
        xi = X_shuffled[start_index:end_index]
        yi = y_shuffled[start_index:end_index]
        
        xi_b = np.c_[np.ones((len(xi), 1)), xi]
        gradients = (2 / len(xi)) * xi_b.T.dot(xi_b.dot(theta) - yi)
        
        t = epoch * n_batches + i
        eta = learning_rate_schedule(t)
        theta = theta - eta * gradients

print(f"求解的参数 theta: {theta.flatten()}")
```

执行代码后，输出结果将接近真实值 `[2, 3, 4]`。

---

![](img/6d441805d519c69d789209da6ada8e4f_13.png)

本节课中我们一起学习了小批量梯度下降法的实现。该方法通过**每次迭代随机选取一小批样本**来计算梯度，有效平衡了批量梯度下降的计算成本与随机梯度下降的收敛波动问题。关键步骤包括数据分批、打乱顺序、计算小批量梯度以及应用学习率衰减策略。