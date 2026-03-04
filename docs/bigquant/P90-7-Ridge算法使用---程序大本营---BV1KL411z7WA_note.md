# 机器学习：P90：7-Ridge算法使用

![](img/2cc972680a49e7dcef5e22af9f105253_0.png)

![](img/2cc972680a49e7dcef5e22af9f105253_2.png)

在本节课中，我们将学习如何使用Scikit-learn库中的线性回归衍生算法——岭回归。我们将通过代码演示岭回归的基本用法，并将其与普通线性回归和随机梯度下降回归进行对比，以直观理解岭回归在系数缩减方面的效果。

## 数据准备

首先，我们需要创建一些用于演示的模拟数据。我们将生成一个具有5个特征的随机数据集。

```python
import numpy as np

# 生成特征数据 X，形状为 (100, 5)
X = 2 * np.random.rand(100, 5)

# 生成真实的权重系数 w，形状为 (5, 1)
w = np.random.randint(1, 10, size=(5, 1))

# 生成真实的截距 b
b = np.random.randint(1, 10, size=1)

# 根据线性关系 y = X·w + b 生成目标值 y，并添加一些随机噪声
y = X.dot(w) + b + np.random.randn(100, 1)
```

由于我们的特征数据是五维的，无法在二维或三维空间中直接可视化，因此我们主要通过数值结果来观察算法的表现。

![](img/2cc972680a49e7dcef5e22af9f105253_4.png)

![](img/2cc972680a49e7dcef5e22af9f105253_6.png)

## 岭回归模型训练

![](img/2cc972680a49e7dcef5e22af9f105253_8.png)

岭回归是线性回归的一种改进，通过在损失函数中增加L2正则项来防止过拟合，并倾向于产生更小的系数。

以下是使用Scikit-learn训练岭回归模型的步骤：

```python
from sklearn.linear_model import Ridge

# 创建岭回归模型实例
# alpha参数控制正则化的强度，alpha值越大，正则化效果越强，系数越趋向于0。
# solver参数指定优化算法，'sag'代表使用随机平均梯度下降法。
ridge_model = Ridge(alpha=1.0, solver='sag')

# 使用数据拟合模型
ridge_model.fit(X, y)

# 打印模型学习到的系数和截距
print("岭回归系数 (coef_):", ridge_model.coef_)
print("岭回归截距 (intercept_):", ridge_model.intercept_)
```

为了评估模型效果，我们可以与生成数据时使用的真实参数进行对比。

```python
# 打印真实的权重和截距以供对比
print("\n真实权重 w:", w.T)
print("真实截距 b:", b)
```

## 与其他回归算法对比

为了更清晰地展示岭回归的特性，我们将其与使用L2正则化的随机梯度下降回归以及普通线性回归进行对比。

上一节我们介绍了岭回归的训练，本节中我们来看看它与其他算法的差异。

### 1. 带L2正则化的随机梯度下降回归

以下是使用SGDRegressor并启用L2惩罚项的训练过程：

```python
from sklearn.linear_model import SGDRegressor

![](img/2cc972680a49e7dcef5e22af9f105253_10.png)

# 创建SGD回归模型，并设置惩罚项为L2正则化，正则化强度alpha同样设为1.0
sgd_model = SGDRegressor(penalty='l2', alpha=1.0, max_iter=1000, tol=1e-3)

![](img/2cc972680a49e7dcef5e22af9f105253_12.png)

# SGDRegressor要求目标y是一维数组，因此需要将y展平
sgd_model.fit(X, y.ravel())

![](img/2cc972680a49e7dcef5e22af9f105253_14.png)

print("SGD回归系数 (coef_):", sgd_model.coef_)
print("SGD回归截距 (intercept_):", sgd_model.intercept_)
```

### 2. 普通线性回归

![](img/2cc972680a49e7dcef5e22af9f105253_16.png)

![](img/2cc972680a49e7dcef5e22af9f105253_18.png)

![](img/2cc972680a49e7dcef5e22af9f105253_19.png)

最后，我们使用不包含任何正则化的普通线性回归作为基线模型。

```python
from sklearn.linear_model import LinearRegression

![](img/2cc972680a49e7dcef5e22af9f105253_21.png)

# 创建普通线性回归模型
linear_model = LinearRegression()
linear_model.fit(X, y)

print("\n普通线性回归系数 (coef_):", linear_model.coef_)
print("普通线性回归截距 (intercept_):", linear_model.intercept_)
```

## 结果分析与总结

![](img/2cc972680a49e7dcef5e22af9f105253_23.png)

运行以上所有代码后，我们可以对比不同模型得到的系数。以下是核心观察点：

*   **系数缩减**：与普通线性回归或SGD回归的结果相比，岭回归学习到的权重系数（`coef_`）的绝对值通常会更小。这验证了L2正则化确实起到了约束模型复杂度、缩减系数的作用，其背后的数学原理是在梯度下降更新公式中增加了一项 `- η * α * w`，促使权重向零衰减。
*   **公式表示**：岭回归的损失函数可以表示为：
    **`J(w) = MSE(w) + α * ||w||₂²`**
    其中，`MSE(w)` 是均方误差，`α` 是正则化强度，`||w||₂²` 是权重的L2范数平方（即所有系数的平方和）。
*   **算法选择**：`solver='sag'` 指定了使用随机平均梯度下降法来优化损失函数，这对于大型数据集是高效的。Scikit-learn的`Ridge`类也支持其他求解器，如自动选择（`'auto'`）、奇异值分解（`'svd'`）等。

![](img/2cc972680a49e7dcef5e22af9f105253_25.png)

本节课中我们一起学习了如何在Scikit-learn中使用岭回归模型。我们通过实践了解到，通过调整`alpha`参数可以控制正则化的强度，从而在模型偏差和方差之间取得平衡，有效缓解过拟合问题。与普通线性回归的对比也直观展示了L2正则化导致系数缩减的效果。