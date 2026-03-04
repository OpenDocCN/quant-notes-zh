# 机器学习入门：P56：使用scikit-learn进行线性回归演示 🚀

![](img/062874503bf923233087cc361ab28ade_1.png)

![](img/062874503bf923233087cc361ab28ade_3.png)

在本节课中，我们将学习如何使用Python的`scikit-learn`库来快速实现线性回归模型。我们将通过一个简单的案例，演示如何调用库中的工具来求解线性回归问题，并与之前手动计算的结果进行对比。

![](img/062874503bf923233087cc361ab28ade_5.png)

---

![](img/062874503bf923233087cc361ab28ade_7.png)

## scikit-learn库简介

上一节我们介绍了线性回归的基本原理和公式推导。本节中，我们来看看如何利用现成的工具库来简化这一过程。

`scikit-learn`是一个基于Python的开源机器学习库。其官网描述如下：

*   **简单高效的工具**：用于预测性数据分析。
*   **易于使用且可复用**：每个人都可以使用，并可在各种场景下复用。
*   **构建于NumPy、SciPy和matplotlib之上**：与我们之前学习的知识体系一脉相承。
*   **开源商用**：采用BSD许可证，可在商业项目中免费使用，无版权问题。

`scikit-learn`能够解决多种机器学习任务，以下是其主要功能分类：
*   **分类**
*   **回归**
*   **聚类**
*   **降维**
*   **模型选择**
*   **数据预处理**

---

## 简单线性回归实战

![](img/062874503bf923233087cc361ab28ade_9.png)

现在，我们使用`scikit-learn`中的`LinearRegression`模块来操作一个简单线性回归（一元一次）案例。

![](img/062874503bf923233087cc361ab28ade_11.png)

以下是实现步骤：
1.  导入必要的库。
2.  准备数据。
3.  创建模型并进行训练。
4.  获取模型的斜率和截距。
5.  绘制回归直线。

![](img/062874503bf923233087cc361ab28ade_13.png)

```python
# 导入库
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression

# 准备数据
X = np.array([1, 2, 3, 4, 5]).reshape(-1, 1)  # 特征数据，需要转换为二维数组
y = np.array([2, 4, 6, 8, 10])                 # 目标值

# 创建模型并训练
model = LinearRegression()
model.fit(X, y)

# 获取斜率和截距
slope = model.coef_[0]  # 斜率
intercept = model.intercept_  # 截距
print(f"斜率: {slope:.2f}")
print(f"截距: {intercept:.2f}")

# 预测并绘图
y_pred = model.predict(X)
plt.scatter(X, y, color='blue', label='原始数据')
plt.plot(X, y_pred, color='red', label='回归直线')
plt.legend()
plt.show()
```

![](img/062874503bf923233087cc361ab28ade_15.png)

运行上述代码，求解出的斜率约为`2.00`，截距约为`0.00`，这与我们预期的`y = 2x`完美符合。

![](img/062874503bf923233087cc361ab28ade_17.png)

---

## 多元线性回归实战

接下来，我们看一个更复杂的例子——多元线性回归（以二元为例）。其流程与简单线性回归类似。

以下是实现多元线性回归的步骤：
1.  导入库并准备包含多个特征的数据。
2.  创建模型并训练。
3.  获取模型参数（多个特征的系数和截距）。

```python
# 导入库
import numpy as np
import warnings
from sklearn.linear_model import LinearRegression
warnings.filterwarnings('ignore')  # 忽略警告信息

# 准备数据：两个特征
X = np.array([[1, 1], [1, 2], [2, 2], [2, 3]])
y = np.dot(X, np.array([1, 2])) + 3  # 假设真实关系为 y = 1*x1 + 2*x2 + 3

![](img/062874503bf923233087cc361ab28ade_19.png)

# 创建模型并训练
model = LinearRegression()
model.fit(X, y)

![](img/062874503bf923233087cc361ab28ade_21.png)

# 获取系数和截距
coefficients = model.coef_  # 系数，对应每个特征
intercept = model.intercept_  # 截距
print(f"特征系数: {coefficients}")
print(f"截距: {intercept}")
```

![](img/062874503bf923233087cc361ab28ade_23.png)

使用`scikit-learn`计算出的结果，与我们之前使用正规方程公式手动计算的结果是一致的。这验证了工具的正确性和便捷性。

![](img/062874503bf923233087cc361ab28ade_25.png)

---

## 总结

本节课中我们一起学习了如何使用`scikit-learn`库实现线性回归。
*   我们首先了解了`scikit-learn`库的特点和用途。
*   然后，我们通过一个简单线性回归案例，演示了从数据准备、模型训练到结果可视化的完整流程。
*   接着，我们将方法扩展到多元线性回归，并验证了其结果的正确性。

![](img/062874503bf923233087cc361ab28ade_27.png)

掌握原理如同掌握了芯片的设计技术，而熟练使用`scikit-learn`这样的工具则如同能够利用芯片制造出高性能产品。两者结合，才能在实践中游刃有余。