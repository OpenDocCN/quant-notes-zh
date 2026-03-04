# 机器学习：P121：支持向量机分类不同核函数差异

![](img/ef6493659c3fa7d801d665d9946600a9_0.png)

![](img/ef6493659c3fa7d801d665d9946600a9_2.png)

在本节课中，我们将学习支持向量机中不同核函数的作用与差异。我们将通过一个葡萄酒分类的实例，演示如何使用线性、多项式、高斯和Sigmoid核函数，并比较它们对模型性能的影响。

## 导包

首先，我们需要导入必要的Python库。

```python
import numpy as np
from sklearn import datasets
from sklearn.svm import SVC
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
```

## 加载数据

接下来，我们加载一个经典的数据集——葡萄酒数据集。这个数据集包含了178个样本，每个样本有13个化学特征，用于将葡萄酒分为三个品质等级（0， 1， 2）。这些特征包括酒精度、苹果酸、灰分、黄酮类化合物含量等。

```python
# 加载葡萄酒数据集
X, y = datasets.load_wine(return_X_y=True)
# 查看数据形状
print(X.shape)  # 输出: (178, 13)
```

## 划分数据集

在建模之前，我们需要将数据划分为训练集和测试集，以便评估模型的泛化能力。

```python
# 划分数据集，80%用于训练，20%用于测试
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
print(X_train.shape)  # 输出: (142, 13)
```

## 建模与核函数比较

上一节我们准备好了数据，本节中我们来看看如何使用支持向量机并比较不同的核函数。核函数决定了支持向量机如何在高维空间中构造决策边界。

### 线性核函数

线性核函数是最简单的形式，它假设数据是线性可分的。其决策函数形式为：**f(x) = w^T * x + b**。

以下是使用线性核函数的步骤：

1.  初始化支持向量机分类器，指定核函数为 `‘linear’`。
2.  使用训练数据拟合模型。
3.  使用训练好的模型对测试数据进行预测。
4.  计算预测的准确率。

```python
# 使用线性核函数
svc_linear = SVC(kernel=‘linear’)
svc_linear.fit(X_train, y_train)
y_predict_linear = svc_linear.predict(X_test)
score_linear = accuracy_score(y_test, y_predict_linear)
print(‘使用核函数 linear 的得分是:’, score_linear)
# 可以查看线性模型的系数和截距
print(‘系数形状:’, svc_linear.coef_.shape)  # 输出: (3, 13)
print(‘截距:’, svc_linear.intercept_)
```

### 多项式核函数

多项式核函数通过将数据映射到更高维的特征空间来处理非线性问题。其公式为：**K(x, y) = (γ * x^T * y + r)^d**。

以下是使用多项式核函数的步骤：

1.  初始化支持向量机分类器，指定核函数为 `‘poly’`。
2.  使用训练数据拟合模型。
3.  使用训练好的模型对测试数据进行预测。
4.  计算预测的准确率。

```python
# 使用多项式核函数
svc_poly = SVC(kernel=‘poly’)
svc_poly.fit(X_train, y_train)
y_predict_poly = svc_poly.predict(X_test)
score_poly = accuracy_score(y_test, y_predict_poly)
print(‘使用核函数 poly 的得分是:’, score_poly)
```

### 高斯核函数

高斯核函数，也称为径向基函数，是支持向量机默认的核函数。它擅长处理复杂的非线性关系。其公式为：**K(x, y) = exp(-γ * ||x - y||^2)**。

以下是使用高斯核函数的步骤：

1.  初始化支持向量机分类器，指定核函数为 `‘rbf’`。
2.  使用训练数据拟合模型。
3.  使用训练好的模型对测试数据进行预测。
4.  计算预测的准确率。

```python
# 使用高斯核函数
svc_rbf = SVC(kernel=‘rbf’)
svc_rbf.fit(X_train, y_train)
y_predict_rbf = svc_rbf.predict(X_test)
score_rbf = accuracy_score(y_test, y_predict_rbf)
print(‘使用核函数 rbf 的得分是:’, score_rbf)
```

### Sigmoid核函数

Sigmoid核函数来源于神经网络中的激活函数，其形式为：**K(x, y) = tanh(γ * x^T * y + r)**。

以下是使用Sigmoid核函数的步骤：

1.  初始化支持向量机分类器，指定核函数为 `‘sigmoid’`。
2.  使用训练数据拟合模型。
3.  使用训练好的模型对测试数据进行预测。
4.  计算预测的准确率。

```python
# 使用Sigmoid核函数
svc_sigmoid = SVC(kernel=‘sigmoid’)
svc_sigmoid.fit(X_train, y_train)
y_predict_sigmoid = svc_sigmoid.predict(X_test)
score_sigmoid = accuracy_score(y_test, y_predict_sigmoid)
print(‘使用核函数 sigmoid 的得分是:’, score_sigmoid)
```

## 总结

![](img/ef6493659c3fa7d801d665d9946600a9_4.png)

本节课中我们一起学习了支持向量机中四种常见核函数的使用和比较。通过葡萄酒分类的实例，我们发现对于该数据集，线性核函数取得了最高的准确率，而多项式、高斯和Sigmoid核函数的表现则相对较差。这个案例说明，不同的核函数适用于不同类型的数据分布，选择合适的核函数是机器学习模型调优的关键步骤之一。同时，我们了解到只有线性核函数可以直接输出模型的系数，其他非线性核函数则不具备这个特性。在实际应用中，需要通过实验来为特定数据选择最合适的核函数。