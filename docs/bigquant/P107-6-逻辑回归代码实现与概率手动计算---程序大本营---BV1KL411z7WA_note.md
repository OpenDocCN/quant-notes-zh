# 机器学习：P107：逻辑回归代码实现与概率手动计算 📊

![](img/07465c6c17bb6326d2c3449821a8ac53_1.png)

![](img/07465c6c17bb6326d2c3449821a8ac53_3.png)

![](img/07465c6c17bb6326d2c3449821a8ac53_5.png)

在本节课中，我们将学习逻辑回归的代码实现，并手动计算其预测概率，以深入理解模型背后的数学原理。

![](img/07465c6c17bb6326d2c3449821a8ac53_7.png)

## 加载并拆分数据

上一节我们介绍了逻辑回归的损失函数，本节中我们来看看如何用代码实现它。首先，我们需要准备数据。

```python
import numpy as np
from sklearn.linear_model import LogisticRegression
from sklearn import datasets
from sklearn.model_selection import train_test_split

# 加载鸢尾花数据集
iris = datasets.load_iris(return_X_y=True)
X, y = iris

# 筛选数据，只保留类别0和1，转换为二分类问题
cond = y != 2
X = X[cond]
y = y[cond]

# 拆分数据集为训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
```

## 训练模型与预测

数据准备完成后，我们可以使用Scikit-learn库训练逻辑回归模型并进行预测。

```python
# 创建并训练逻辑回归模型
model = LogisticRegression()
model.fit(X_train, y_train)

# 使用模型进行预测
y_predict = model.predict(X_test)

# 获取预测的概率值
y_proba = model.predict_proba(X_test)

![](img/07465c6c17bb6326d2c3449821a8ac53_9.png)

print(“预测结果：”, y_predict)
print(“\n预测概率：\n”, y_proba)
print(“\n真实类别：”, y_test)
```

模型输出的`y_proba`是一个概率数组。在二分类中，第一列代表类别0的概率，第二列代表类别1的概率。最终的预测类别`y_predict`就是根据这些概率转换而来的：如果类别1的概率大于0.5，则预测为1，否则预测为0。

![](img/07465c6c17bb6326d2c3449821a8ac53_11.png)

以下是概率转换为类别的过程：
```python
# 使用argmax找到每行概率最大值的索引，即为预测类别
predicted_class = np.argmax(y_proba, axis=1)
```

![](img/07465c6c17bb6326d2c3449821a8ac53_13.png)

![](img/07465c6c17bb6326d2c3449821a8ac53_15.png)

## 手动计算概率

现在，我们来探讨模型内部是如何计算这些概率的。逻辑回归的核心是Sigmoid函数。

逻辑回归的预测概率计算公式为：
**`P(y=1|x) = 1 / (1 + e^(-z))`**
其中，**`z = w·x + b`**，`w`是权重系数，`b`是截距。

以下是手动实现该计算的过程：

```python
# 定义Sigmoid函数
def sigmoid(x):
    return 1 / (1 + np.exp(-x))

# 从训练好的模型中获取系数和截距
w = model.coef_[0]  # 权重系数
b = model.intercept_[0]  # 截距

# 计算线性部分 z
z = X_test.dot(w) + b

# 将z代入Sigmoid函数，得到类别1的概率
p = sigmoid(z)

# 手动组合概率矩阵：第一列为类别0的概率(1-p)，第二列为类别1的概率(p)
manual_proba = np.column_stack((1-p, p))

# 比较手动计算与模型计算的前5个结果
print(“手动计算概率（前5个）：\n”, manual_proba[:5])
print(“\n模型计算概率（前5个）：\n”, y_proba[:5])
```

通过比较可以发现，手动计算得到的概率值与模型`predict_proba`方法输出的结果完全一致。这验证了逻辑回归的预测过程就是基于Sigmoid函数对线性方程结果进行的转换。

## 总结

![](img/07465c6c17bb6326d2c3449821a8ac53_17.png)

本节课中我们一起学习了逻辑回归的完整代码实现流程。我们从加载和预处理数据开始，使用Scikit-learn训练了模型并做出了预测。更重要的是，我们通过手动实现Sigmoid函数和线性方程计算，一步步推导出了模型的预测概率，并验证了其与库函数结果的一致性。这个过程清晰地展示了逻辑回归如何将线性回归的结果映射到[0,1]区间，从而解决分类问题。理解这个计算过程对于掌握逻辑回归模型至关重要。