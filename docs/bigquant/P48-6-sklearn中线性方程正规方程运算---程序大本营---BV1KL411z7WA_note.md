# 机器学习入门：P48：sklearn中正规方程运算 🧮

在本节课中，我们将学习如何使用Python的`sklearn`库来简化线性回归中正规方程的运算。我们将看到如何封装一个函数，直接输出方程的解，从而避免手动计算的繁琐和潜在错误。

---

## 封装正规方程函数

上一节我们介绍了正规方程的手动计算。本节中，我们来看看如何将这个过程封装成一个函数，以便重复使用。

我们可以创建一个函数，直接输入数据`x`和目标值`y`，输出方程的解。这样做的好处是代码更简洁，且不易出错。

以下是封装函数的步骤：

1.  导入必要的库。
2.  定义函数，接收`x`和`y`作为参数。
3.  在函数内部实现正规方程的计算逻辑。
4.  返回计算出的系数（即方程的解）。

**代码示例：**
```python
import numpy as np

def normal_equation(x, y):
    """
    使用正规方程求解线性回归系数。
    参数:
        x: 特征数据，二维数组。
        y: 目标值，一维数组。
    返回:
        theta: 线性回归的系数。
    """
    # 正规方程公式: theta = (X^T * X)^(-1) * X^T * y
    theta = np.linalg.inv(x.T.dot(x)).dot(x.T).dot(y)
    return theta
```

---

## 使用sklearn的LinearRegression

虽然我们可以自己封装函数，但更高效的方法是使用现成的、经过充分测试的库。`sklearn`（Scikit-learn）就是一个强大的机器学习库，其中包含了线性回归的实现。

我们从`sklearn.linear_model`模块中导入`LinearRegression`类。这个类的名称直接翻译就是“线性回归”，其背后的核心原理之一正是我们之前学习的正规方程。

**代码示例：**
```python
from sklearn.linear_model import LinearRegression
```

导入后，我们可以创建一个线性回归模型对象。

**代码示例：**
```python
model = LinearRegression()
```

---

## 拟合模型与获取系数

创建模型后，我们需要用数据来“训练”或“拟合”它。这个过程就是让模型根据我们提供的`x`（特征数据）和`y`（目标值）来学习它们之间的关系。

这里有一个重要的细节：在机器学习中，输入数据`x`通常要求是二维数组。这是因为`x`可能包含多个样本（例如多个方程），而每个样本又包含多个特征（例如方程中的多个变量）。二维结构能很好地组织这种数据。

**代码示例：**
```python
# 假设x_train是二维特征数据，y_train是对应的目标值
model.fit(x_train, y_train)
```

训练完成后，我们可以通过`model.coef_`属性来获取模型学习到的系数（即每个特征对应的权重`w`或`θ`）。

**代码示例：**
```python
coefficients = model.coef_
print("模型系数（斜率）:", coefficients)
```

---

## 处理截距项

有时我们会发现，使用`sklearn`的`LinearRegression`计算出的系数，与我们手动用正规方程算出的结果不一致。这通常是因为`LinearRegression`默认会计算一个截距项（`intercept`）。

![](img/6042ad0db4eb90ad8622a77dcfc2b252_1.png)

*   **截距（Intercept）**：在直线方程 **`y = wx + b`** 中，`b`就是截距。
*   我们之前手动计算的八元一次方程组，其对应的线性模型形式是 **`y = w1*x1 + w2*x2 + ... + w8*x8`**，其中没有单独的常数项`b`，或者说其截距为0。

![](img/6042ad0db4eb90ad8622a77dcfc2b252_3.png)

`LinearRegression`类有一个参数叫`fit_intercept`，用于控制是否计算截距。
*   当`fit_intercept=True`（默认值）时，模型会计算截距`b`。
*   当`fit_intercept=False`时，模型强制截距为0，此时计算出的系数`w`就会与手动正规方程（针对无截距模型）的结果完全一致。

**代码示例：**
```python
# 创建不计算截距的线性回归模型
model_no_intercept = LinearRegression(fit_intercept=False)
model_no_intercept.fit(x_train, y_train)

# 此时获取的系数应与手动正规方程结果一致
coefficients_no_intercept = model_no_intercept.coef_
print("无截距模型的系数:", coefficients_no_intercept)

# 可以查看截距值，此时应为0
intercept_value = model_no_intercept.intercept_
print("模型截距:", intercept_value)
```

---

## 总结

本节课中我们一起学习了如何利用`sklearn`库简化线性回归运算。

1.  我们首先了解了将正规方程封装成函数的思想。
2.  接着，我们重点学习了使用`sklearn.linear_model`中的`LinearRegression`类，这是更标准、更高效的做法。
3.  我们掌握了用`.fit()`方法训练模型，并用`.coef_`属性获取模型系数。
4.  最后，我们解释了`fit_intercept`参数的作用，明白了当原方程无截距时，需要将该参数设为`False`，才能得到与手动正规方程一致的结果。

![](img/6042ad0db4eb90ad8622a77dcfc2b252_5.png)

通过使用`sklearn`，我们得以“站在巨人的肩膀上”，直接应用成熟、优化的算法，从而更专注于解决问题本身。