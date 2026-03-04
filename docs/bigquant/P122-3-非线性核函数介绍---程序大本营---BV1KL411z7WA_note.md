# 机器学习：P122：非线性核函数介绍 🧠

![](img/17966b97b41bdc36440c6f4734088363_1.png)

在本节课中，我们将学习支持向量机（SVM）中的非线性核函数。我们将通过代码演示，直观地理解当数据线性不可分时，如何通过不同的核函数（如多项式核和高斯核）来提升模型的分类能力。

![](img/17966b97b41bdc36440c6f4734088363_3.png)

---

## 核函数与模型评估 🔍

![](img/17966b97b41bdc36440c6f4734088363_5.png)

上一节我们介绍了支持向量机的基本概念。本节中，我们来看看如何为SVM选择合适的核函数。

在训练模型时，我们通过模型评估来确定最佳参数。例如，使用 `accuracy_score` 函数可以计算模型的准确率。分数越高，表明该组参数（包括核函数）在当前任务上表现越好。

对于葡萄酒分类的案例，线性核函数（`kernel='linear'`）的得分高达0.97，因此被选为最佳核函数。这表明不同的核函数各有其适用场景，需要通过调整和评估来找到最优解。

![](img/17966b97b41bdc36440c6f4734088363_7.png)

![](img/17966b97b41bdc36440c6f4734088363_9.png)

---

![](img/17966b97b41bdc36440c6f4734088363_11.png)

## 非线性核函数简介 🔄

![](img/17966b97b41bdc36440c6f4734088363_13.png)

![](img/17966b97b41bdc36440c6f4734088363_15.png)

线性核函数对应线性方程，易于理解。但当数据无法用一条直线分开时，我们就需要非线性核函数。

![](img/17966b97b41bdc36440c6f4734088363_17.png)

观察下图，红色和蓝色两类点混杂在一起，无法用一条直线将其完全分开。

![](img/17966b97b41bdc36440c6f4734088363_19.png)

![](img/17966b97b41bdc36440c6f4734088363_7.png)

![](img/17966b97b41bdc36440c6f4734088363_21.png)

此时，就需要借助非线性核函数，如多项式核函数或高斯核函数，来找到一个更复杂的决策边界。

---

## 代码实践：创建非线性数据 🛠️

为了演示非线性核函数的作用，我们首先在代码中创建一组模拟的非线性可分数据。

![](img/17966b97b41bdc36440c6f4734088363_23.png)

我们使用 `sklearn.datasets.make_circles` 方法生成100个呈环形分布的数据点，并添加少量噪声。

```python
from sklearn import datasets
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.colors import ListedColormap

# 创建环形数据
X, y = datasets.make_circles(n_samples=100, factor=0.7, noise=0.03)
# 添加噪声
X += np.random.randn(100, 2) * 0.03

# 设置图形大小
plt.figure(figsize=(5, 5))
# 定义颜色映射
cmap = ListedColormap(['blue', 'red'])
# 绘制散点图
plt.scatter(X[:, 0], X[:, 1], c=y, cmap=cmap)
plt.show()
```

执行上述代码后，我们将得到两圈颜色不同的数据点，直观上无法用直线分隔。

---

## 使用线性核函数 📉

首先，我们尝试使用线性核函数（`kernel='linear'`）来拟合这组数据。

```python
from sklearn.svm import SVC
from sklearn.metrics import accuracy_score

# 使用线性核函数
svc_linear = SVC(kernel='linear')
svc_linear.fit(X, y)
score_linear = svc_linear.score(X, y)
print(f"线性核函数准确率: {score_linear}")
```

![](img/17966b97b41bdc36440c6f4734088363_25.png)

运行后，准确率大约只有0.51。这证实了线性模型无法有效处理此类非线性数据。

---

## 使用多项式核函数 📈

接下来，我们尝试多项式核函数（`kernel='poly'`）。它的核心思想是“升维”，即将数据映射到更高维度的空间，以期在高维空间中变得线性可分。关键参数 `degree` 控制多项式的次数。

以下是使用不同 `degree` 值的示例：

```python
# 使用多项式核函数，默认degree=3
svc_poly_default = SVC(kernel='poly')
svc_poly_default.fit(X, y)
score_poly_default = svc_poly_default.score(X, y)
print(f"多项式核函数(degree=3)准确率: {score_poly_default}")

# 使用二次多项式核函数
svc_poly_2 = SVC(kernel='poly', degree=2)
svc_poly_2.fit(X, y)
score_poly_2 = svc_poly_2.score(X, y)
print(f"多项式核函数(degree=2)准确率: {score_poly_2}")
```

![](img/17966b97b41bdc36440c6f4734088363_27.png)

运行结果可能显示：
*   `degree=3` 时，准确率约为0.69。
*   `degree=2` 时，准确率可能达到1.0（完美分类）。

**为什么二次多项式效果极佳？**
因为我们的数据呈环形分布。一个圆的方程可以表示为 **f(x, y) = x² + y² = r²**，这正是一个二次方程。因此，二次多项式核函数能天然地拟合数据的分布结构，实现完美分割。而 `degree=1` 时，多项式核退化为线性核，效果与之前相同。

---

![](img/17966b97b41bdc36440c6f4734088363_29.png)

## 使用高斯核函数 ⚡

![](img/17966b97b41bdc36440c6f4734088363_31.png)

最后，我们尝试高斯核函数（Radial Basis Function, `kernel='rbf'`）。这是最常用的非线性核函数之一，它通过计算样本间的“距离”来构造决策边界。

```python
# 使用高斯核函数
svc_rbf = SVC(kernel='rbf')
svc_rbf.fit(X, y)
score_rbf = svc_rbf.score(X, y)
print(f"高斯核函数准确率: {score_rbf}")
```

对于本例的环形数据，高斯核函数通常也能取得很高的准确率（接近1.0），因为它能灵活地拟合复杂的边界形状。

![](img/17966b97b41bdc36440c6f4734088363_33.png)

---

![](img/17966b97b41bdc36440c6f4734088363_35.png)

## 总结 📚

本节课中我们一起学习了支持向量机的非线性核函数：
1.  **线性核函数** 在处理线性可分数据时有效，但对非线性数据（如环形数据）无能为力。
2.  **多项式核函数** 通过升维寻找决策边界，其 `degree` 参数至关重要，需要根据数据特征调整。在本例中，二次多项式完美匹配了数据的圆形分布。
3.  **高斯核函数** 具有很强的灵活性，能适应各种复杂的非线性模式，是实践中常用的选择。

![](img/17966b97b41bdc36440c6f4734088363_37.png)

通过这个案例，我们清晰地看到，**非线性核函数对于处理非线性可分数据起着至关重要的作用**。在实际应用中，应通过交叉验证和模型评估来为具体问题选择最合适的核函数及其参数。