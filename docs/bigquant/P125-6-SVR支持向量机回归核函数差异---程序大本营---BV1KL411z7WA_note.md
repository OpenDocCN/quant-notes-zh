# 机器学习：P125：支持向量机回归与核函数差异 🧠

![](img/d06c46983d74ae82d42052a80cfdc608_0.png)

![](img/d06c46983d74ae82d42052a80cfdc608_2.png)

在本节课中，我们将学习如何使用支持向量机（SVR）解决回归问题，并比较不同核函数（线性、多项式、高斯核）在拟合非线性数据（如正弦波）时的效果差异。我们将通过代码实践，直观地展示每种核函数的拟合能力。

## 概述
支持向量机不仅可以用于分类（SVC），也可以用于回归（SVR）。在回归任务中，核函数的选择至关重要，它决定了模型如何理解和拟合数据的潜在规律。本节我们将创建一个正弦波数据，并尝试用不同核函数的SVR模型进行拟合。

## 环境准备与数据创建
首先，我们需要导入必要的库并创建用于实验的数据集。

```python
import numpy as np
from sklearn.svm import SVR
import matplotlib.pyplot as plt
```

![](img/d06c46983d74ae82d42052a80cfdc608_4.png)

接下来，我们生成一个正弦波作为回归目标。

```python
# 创建特征数据X
x = np.linspace(0, 2 * np.pi, 50)
# 创建目标数据y（正弦函数）
y = np.sin(x)
# 将X的形状从一维转换为二维（n_samples, n_features），以满足SVR的输入要求
x = x.reshape(-1, 1)
# 绘制原始数据的散点图
plt.scatter(x, y)
plt.show()
```

## 线性核函数（Linear Kernel）
上一节我们准备好了数据，本节中我们首先尝试使用线性核函数进行拟合。线性核假设数据间的关系是线性的。

以下是使用线性核函数 `kernel=‘linear’` 的建模步骤：

![](img/d06c46983d74ae82d42052a80cfdc608_6.png)

```python
# 创建SVR模型，指定核函数为线性
svr_linear = SVR(kernel=‘linear’)
# 训练模型，注意使用 y.ravel() 将y转换为一维数组以避免警告
svr_linear.fit(x, y.ravel())
# 使用模型进行预测
y_pred_linear = svr_linear.predict(x)

![](img/d06c46983d74ae82d42052a80cfdc608_8.png)

# 可视化结果
plt.scatter(x, y, label=‘Original Data’)
plt.plot(x, y_pred_linear, color=‘red’, label=‘Linear Kernel Prediction’)
plt.legend()
plt.show()
```

执行代码后，你会发现红色的预测线是一条直线，无法捕捉正弦波的波动规律。这说明线性核函数不适合拟合此类非线性数据。

## 多项式核函数（Polynomial Kernel）
线性核拟合失败后，我们来看看多项式核函数。多项式核可以捕捉数据间更复杂的高阶关系。

以下是使用多项式核函数 `kernel=‘poly’` 的建模步骤，我们尝试了不同的阶数（degree）：

![](img/d06c46983d74ae82d42052a80cfdc608_10.png)

```python
# 创建SVR模型，指定核函数为多项式，默认阶数为3
svr_poly = SVR(kernel=‘poly’, degree=3)
svr_poly.fit(x, y.ravel())
y_pred_poly = svr_poly.predict(x)

![](img/d06c46983d74ae82d42052a80cfdc608_12.png)

# 可视化结果
plt.scatter(x, y, label=‘Original Data’)
plt.plot(x, y_pred_poly, color=‘green’, label=‘Polynomial Kernel (degree=3)’)
plt.legend()
plt.show()
```

![](img/d06c46983d74ae82d42052a80cfdc608_14.png)

将 `degree` 参数改为2或其他值后，拟合曲线依然无法很好地匹配正弦波。这表明正弦波的本质并非多项式关系。

![](img/d06c46983d74ae82d42052a80cfdc608_16.png)

![](img/d06c46983d74ae82d42052a80cfdc608_17.png)

## 径向基函数/高斯核（RBF Kernel）
最后，我们尝试径向基函数核，也称为高斯核。它在处理复杂的非线性模式时通常表现良好。

![](img/d06c46983d74ae82d42052a80cfdc608_19.png)

以下是使用高斯核函数 `kernel=‘rbf’` 的建模步骤：

![](img/d06c46983d74ae82d42052a80cfdc608_21.png)

```python
# 创建SVR模型，指定核函数为RBF（高斯核）
svr_rbf = SVR(kernel=‘rbf’)
svr_rbf.fit(x, y.ravel())
y_pred_rbf = svr_rbf.predict(x)

![](img/d06c46983d74ae82d42052a80cfdc608_23.png)

# 可视化结果
plt.scatter(x, y, label=‘Original Data’)
plt.plot(x, y_pred_rbf, color=‘orange’, label=‘RBF Kernel Prediction’)
plt.legend()
plt.show()
```

执行后，橙色预测曲线能够紧密地跟随正弦波的起伏，拟合效果显著优于前两种核函数。

![](img/d06c46983d74ae82d42052a80cfdc608_25.png)

![](img/d06c46983d74ae82d42052a80cfdc608_27.png)

为了进一步验证，我们可以增加数据的复杂度，例如生成两个周期的正弦波：

```python
# 创建更复杂的数据（两个正弦波周期）
x_complex = np.linspace(0, 4 * np.pi, 100).reshape(-1, 1)
y_complex = np.sin(x_complex).ravel()

# 使用RBF核函数拟合复杂数据
svr_rbf_complex = SVR(kernel=‘rbf’)
svr_rbf_complex.fit(x_complex, y_complex)
y_pred_rbf_complex = svr_rbf_complex.predict(x_complex)

![](img/d06c46983d74ae82d42052a80cfdc608_29.png)

![](img/d06c46983d74ae82d42052a80cfdc608_31.png)

# 可视化结果
plt.scatter(x_complex, y_complex, label=‘Original Complex Data’)
plt.plot(x_complex, y_pred_rbf_complex, color=‘purple’, label=‘RBF Kernel Fit’)
plt.legend()
plt.show()
```

![](img/d06c46983d74ae82d42052a80cfdc608_33.png)

![](img/d06c46983d74ae82d42052a80cfdc608_35.png)

可以看到，即使对于更复杂的波形，RBF核函数依然能够提供良好的拟合。

![](img/d06c46983d74ae82d42052a80cfdc608_37.png)

![](img/d06c46983d74ae82d42052a80cfdc608_39.png)

## 核心概念与选择策略
*   **核函数公式**：高斯核（RBF）的公式为 $K(x_i, x_j) = \exp(-\gamma \|x_i - x_j\|^2)$，它通过衡量样本点之间的“距离”来构造非线性决策边界。
*   **核函数选择**：数据往往是“黑盒”，我们无法预先知道其内部结构。最实用的方法是进行尝试和比较。
*   **模型评估**：对于无法可视化的情况，应依赖模型评价指标（如R²分数、均方误差）来客观比较不同核函数模型的得分，选择得分最高的模型。

![](img/d06c46983d74ae82d42052a80cfdc608_41.png)

## 总结
本节课中我们一起学习了支持向量机回归的应用。我们通过拟合正弦波的例子，实践并比较了线性核、多项式核和高斯核（RBF）三种核函数。结果表明，对于此类非线性、周期性数据，高斯核（RBF）表现出最佳的拟合能力。这启示我们，在实际建模中，当数据关系未知时，应通过实验对比来选择最合适的核函数。