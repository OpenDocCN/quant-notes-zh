# 机器学习：P98：多项式回归升维实战（二）📈

![](img/622ea3d283652e712f90ad26b25fccac_1.png)

在本节课中，我们将学习如何使用Scikit-learn库中的`PolynomialFeatures`工具，自动化地实现多项式回归中的数据升维操作。我们将通过一个实战案例，演示如何将一维特征转换为高维特征，并利用线性回归模型进行拟合。

![](img/622ea3d283652e712f90ad26b25fccac_3.png)

---

![](img/622ea3d283652e712f90ad26b25fccac_5.png)

![](img/622ea3d283652e712f90ad26b25fccac_7.png)

## 导入必要的库

![](img/622ea3d283652e712f90ad26b25fccac_9.png)

首先，我们需要导入本教程中将要使用的所有Python库。

![](img/622ea3d283652e712f90ad26b25fccac_11.png)

![](img/622ea3d283652e712f90ad26b25fccac_13.png)

```python
from sklearn.linear_model import LinearRegression
from sklearn.preprocessing import PolynomialFeatures, StandardScaler
import numpy as np
```

![](img/622ea3d283652e712f90ad26b25fccac_15.png)

![](img/622ea3d283652e712f90ad26b25fccac_17.png)

上一节我们介绍了手动进行数据升维的方法，本节中我们来看看如何使用Scikit-learn提供的工具来简化这一过程。

![](img/622ea3d283652e712f90ad26b25fccac_19.png)

---

![](img/622ea3d283652e712f90ad26b25fccac_21.png)

## 理解PolynomialFeatures

![](img/622ea3d283652e712f90ad26b25fccac_23.png)

`PolynomialFeatures`是Scikit-learn中一个用于生成多项式特征的预处理工具。它的核心功能是自动将原始特征进行组合，生成新的多项式特征。

以下是`PolynomialFeatures`的两个关键参数：
*   **`degree`**：控制多项式的最高次数。例如，`degree=2`会生成所有二次项及以下的所有特征组合。
*   **`interaction_only`**：一个布尔值参数，用于控制是否只生成特征间的交互项（即不同特征相乘的项），而不生成特征自身的幂次项（如 `x^2`）。

![](img/622ea3d283652e712f90ad26b25fccac_25.png)

为了更清晰地理解这两个参数，我们通过一个简单的例子来演示。

![](img/622ea3d283652e712f90ad26b25fccac_27.png)

![](img/622ea3d283652e712f90ad26b25fccac_29.png)

```python
# 创建一个简单的二维数据
a = np.array([[1, 3]])

# 实例化PolynomialFeatures，设置degree=2, interaction_only=True
poly_interact = PolynomialFeatures(degree=2, interaction_only=True)
result_interact = poly_interact.fit_transform(a)
print(“interaction_only=True 的结果：”, result_interact)

![](img/622ea3d283652e712f90ad26b25fccac_31.png)

# 实例化PolynomialFeatures，设置degree=2, interaction_only=False
poly_full = PolynomialFeatures(degree=2, interaction_only=False)
result_full = poly_full.fit_transform(a)
print(“interaction_only=False 的结果：”, result_full)
```

运行上述代码，你会得到不同的输出：
*   当 `interaction_only=True` 时，输出为 `[[1, 1, 3, 3]]`。这包含了常数项1，以及特征1和特征3的交互项（1*3）。
*   当 `interaction_only=False` 时，输出为 `[[1, 1, 3, 1, 3, 9]]`。这包含了常数项1，每个特征的一次项（1, 3），以及每个特征的二次项（1^2=1, 3^2=9）和交互项（1*3）。

通过调整`degree`参数，我们可以控制生成特征的最高维度。例如，`degree=4`会生成所有四次项及以下的特征组合。

---

## 实战：使用PolynomialFeatures进行多项式回归

理解了`PolynomialFeatures`的基本原理后，我们现在将其应用于一个具体的回归问题。

### 第一步：创建模拟数据

![](img/622ea3d283652e712f90ad26b25fccac_33.png)

我们使用与上一节相同的数据集，其背后真实的模型是一个二次函数。

![](img/622ea3d283652e712f90ad26b25fccac_35.png)

```python
# 创建训练数据
np.random.seed(42)
m = 100
X = 6 * np.random.rand(m, 1) - 2
y = 0.5 * X**2 + X + 2 + np.random.randn(m, 1)

![](img/622ea3d283652e712f90ad26b25fccac_37.png)

# 创建用于绘制预测曲线的测试数据
X_test = np.linspace(-2, 12, 300).reshape(-1, 1)
```

![](img/622ea3d283652e712f90ad26b25fccac_39.png)

### 第二步：使用PolynomialFeatures升维

![](img/622ea3d283652e712f90ad26b25fccac_41.png)

![](img/622ea3d283652e712f90ad26b25fccac_43.png)

接下来，我们使用`PolynomialFeatures`对训练数据和测试数据进行升维转换。

![](img/622ea3d283652e712f90ad26b25fccac_45.png)

```python
# 实例化PolynomialFeatures，设置多项式次数为2
poly = PolynomialFeatures(degree=2)

# 对训练数据进行转换
X_poly = poly.fit_transform(X)
print(“升维后训练数据的形状：”, X_poly.shape) # 输出：(100, 3)

# 对测试数据进行转换（使用相同的转换器）
X_test_poly = poly.transform(X_test)
```

![](img/622ea3d283652e712f90ad26b25fccac_47.png)

转换后，`X`从形状`(100, 1)`的一维数据变成了`(100, 3)`的三维数据，这三个维度分别是：常数项1、`X`的一次项、`X`的二次项。

![](img/622ea3d283652e712f90ad26b25fccac_49.png)

### 第三步：训练线性回归模型并预测

![](img/622ea3d283652e712f90ad26b25fccac_51.png)

现在，我们使用升维后的数据来训练一个标准的线性回归模型。

![](img/622ea3d283652e712f90ad26b25fccac_53.png)

![](img/622ea3d283652e712f90ad26b25fccac_55.png)

```python
# 实例化并训练线性回归模型
model = LinearRegression()
model.fit(X_poly, y)

# 使用模型对测试数据进行预测
y_predict = model.predict(X_test_poly)
```

### 第四步：可视化结果

![](img/622ea3d283652e712f90ad26b25fccac_57.png)

最后，我们将原始数据点和模型的预测曲线绘制在一起，以直观地评估拟合效果。

![](img/622ea3d283652e712f90ad26b25fccac_59.png)

```python
import matplotlib.pyplot as plt

![](img/622ea3d283652e712f90ad26b25fccac_61.png)

![](img/622ea3d283652e712f90ad26b25fccac_63.png)

plt.scatter(X, y, label=“原始数据”)
plt.plot(X_test, y_predict, ‘r-‘, label=“多项式回归拟合线”)
plt.xlabel(“X”)
plt.ylabel(“y”)
plt.legend()
plt.show()
```

![](img/622ea3d283652e712f90ad26b25fccac_65.png)

![](img/622ea3d283652e712f90ad26b25fccac_67.png)

运行代码后，你将看到一条平滑的曲线很好地拟合了呈抛物线分布的数据点。

![](img/622ea3d283652e712f90ad26b25fccac_69.png)

![](img/622ea3d283652e712f90ad26b25fccac_71.png)

---

![](img/622ea3d283652e712f90ad26b25fccac_73.png)

## 探索不同维度的拟合效果

![](img/622ea3d283652e712f90ad26b25fccac_75.png)

![](img/622ea3d283652e712f90ad26b25fccac_77.png)

我们可以通过修改`PolynomialFeatures`的`degree`参数，来探索不同多项式次数下的拟合情况。

```python
degrees = [1, 2, 3, 5, 10]
plt.figure(figsize=(15, 3))

![](img/622ea3d283652e712f90ad26b25fccac_79.png)

![](img/622ea3d283652e712f90ad26b25fccac_81.png)

for i, degree in enumerate(degrees):
    poly = PolynomialFeatures(degree=degree)
    X_poly = poly.fit_transform(X)
    X_test_poly = poly.transform(X_test)

    model = LinearRegression()
    model.fit(X_poly, y)
    y_predict = model.predict(X_test_poly)

    plt.subplot(1, len(degrees), i+1)
    plt.scatter(X, y, s=10)
    plt.plot(X_test, y_predict, ‘r-‘)
    plt.title(f“Degree = {degree}”)
    plt.axis([-2, 12, -5, 30])

![](img/622ea3d283652e712f90ad26b25fccac_83.png)

plt.tight_layout()
plt.show()
```

观察不同`degree`的拟合结果，你会发现：
*   `degree=1`（线性）无法拟合曲线。
*   `degree=2`（二次）完美匹配了数据的真实分布。
*   `degree=3, 5, 10`等高次多项式虽然也能很好地穿过数据点，但模型的系数中，高次项的系数会变得非常小（例如`x^5`, `x^10`的系数接近0）。这意味着这些高次项对最终预测结果的贡献微乎其微。

![](img/622ea3d283652e712f90ad26b25fccac_85.png)

---

![](img/622ea3d283652e712f90ad26b25fccac_87.png)

![](img/622ea3d283652e712f90ad26b25fccac_89.png)

## 奥卡姆剃刀原则

![](img/622ea3d283652e712f90ad26b25fccac_91.png)

![](img/622ea3d283652e712f90ad26b25fccac_93.png)

在机器学习中，我们遵循 **奥卡姆剃刀原则**：**如无必要，勿增实体**。这个原则在模型选择中体现为：在保证模型性能的前提下，应选择最简单、特征最少的模型。

![](img/622ea3d283652e712f90ad26b25fccac_95.png)

![](img/622ea3d283652e712f90ad26b25fccac_97.png)

在我们的例子中，数据本身是由二次方程生成的，因此`degree=2`的模型不仅拟合效果好，而且最简单、最容易解释。使用更高次数的模型（如5次、10次）虽然在本训练集上可能看起来拟合得“更完美”，但这通常会导致**过拟合**，即模型过度学习了训练数据中的噪声，而在未见过的测试数据上表现变差。同时，复杂模型也缺乏可解释性。

![](img/622ea3d283652e712f90ad26b25fccac_99.png)

![](img/622ea3d283652e712f90ad26b25fccac_101.png)

因此，`degree=2`的二次多项式模型是本案例中的最优选择。

![](img/622ea3d283652e712f90ad26b25fccac_103.png)

![](img/622ea3d283652e712f90ad26b25fccac_105.png)

---

![](img/622ea3d283652e712f90ad26b25fccac_107.png)

本节课中我们一起学习了如何使用`PolynomialFeatures`自动化实现多项式回归的升维过程。我们理解了`degree`和`interaction_only`参数的作用，并通过实战看到了二次多项式如何完美拟合我们的数据。最后，我们强调了模型选择中奥卡姆剃刀原则的重要性，即优先选择简单且有效的模型。