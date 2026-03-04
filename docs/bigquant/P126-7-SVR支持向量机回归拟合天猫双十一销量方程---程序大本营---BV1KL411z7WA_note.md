# 机器学习实战：P126：SVR支持向量机回归拟合天猫双十一销量方程 📈

![](img/e5852ce41176b046343c1b115ebda7a6_1.png)

在本节课中，我们将学习如何使用支持向量机回归模型，来拟合天猫双十一历年销售额数据，并预测其变化趋势。我们将从数据准备开始，逐步完成模型训练、参数调整和结果可视化。

![](img/e5852ce41176b046343c1b115ebda7a6_3.png)

---

![](img/e5852ce41176b046343c1b115ebda7a6_5.png)

## 数据准备与可视化 📊

上一节我们介绍了天猫双十一的销量数据。本节中，我们来看看如何创建和可视化这些数据。

首先，我们创建代表年份的x轴数据。数据从2009年开始，到2020年结束。为了简化计算，我们将所有年份减去2008。

```python
import numpy as np
import matplotlib.pyplot as plt

# 创建年份数据 (2009-2019)
x = np.arange(2009, 2020) - 2008
```

接下来，我们定义对应的销售额数据y。

```python
# 天猫双十一历年销售额（单位：亿）
y = np.array([0.5, 9.36, 52, 191, 350, 571, 912, 1207, 1682, 2135, 2684])
```

![](img/e5852ce41176b046343c1b115ebda7a6_7.png)

现在，我们可以将数据点绘制在图上，以便观察其分布规律。

```python
plt.scatter(x, y, color='red')
plt.show()
```

![](img/e5852ce41176b046343c1b115ebda7a6_9.png)

执行代码后，我们可以看到数据点呈现出一条明显的上升曲线。观察其形状，它类似于一个二次或三次函数的曲线，这意味着我们可以尝试用多项式来拟合它。

![](img/e5852ce41176b046343c1b115ebda7a6_11.png)

---

## 使用SVR进行数据拟合 🤖

![](img/e5852ce41176b046343c1b115ebda7a6_13.png)

在观察了数据分布后，我们尝试使用支持向量回归模型进行拟合。首先，我们使用线性核函数。

以下是使用线性核函数`linear`的SVR模型代码：

```python
from sklearn.svm import SVR

# 尝试使用线性核函数
svr_linear = SVR(kernel='linear')
# 注意：sklearn的fit方法要求X是二维数组
x_reshaped = x.reshape(-1, 1)
svr_linear.fit(x_reshaped, y)
```

运行上述代码会报错，提示输入数据需要是二维的。根据错误提示，我们需要使用`.reshape(-1, 1)`将一维数组x转换为二维数组。

修正后，模型可以成功训练。为了绘制平滑的预测曲线，我们需要创建更密集的测试数据点。

```python
# 创建更密集的测试数据用于画图
x_test = np.linspace(2009, 2019, 100).reshape(-1, 1) - 2008
y_pred_linear = svr_linear.predict(x_test)

# 可视化结果
plt.scatter(x, y, color='red', label='Actual Data')
plt.plot(x_test.ravel(), y_pred_linear, color='green', label='Linear SVR Prediction')
plt.legend()
plt.show()
```

执行后，我们发现线性核函数的拟合效果是一条斜线，无法捕捉数据的曲线趋势，效果不理想。

---

## 尝试不同的核函数 🔄

上一节我们使用线性核函数效果不佳。本节中，我们来看看其他核函数的表现。

首先，我们尝试高斯径向基核函数`rbf`。

```python
# 使用高斯核函数 (rbf)
svr_rbf = SVR(kernel='rbf')
svr_rbf.fit(x_reshaped, y)
y_pred_rbf = svr_rbf.predict(x_test)

plt.scatter(x, y, color='red', label='Actual Data')
plt.plot(x_test.ravel(), y_pred_rbf, color='blue', label='RBF SVR Prediction')
plt.legend()
plt.show()
```

![](img/e5852ce41176b046343c1b115ebda7a6_15.png)

`rbf`核函数的拟合效果同样不理想，因为它更适合处理局部特征，而我们的数据是整体的多项式趋势。

![](img/e5852ce41176b046343c1b115ebda7a6_17.png)

接下来，我们尝试多项式核函数`poly`。根据之前观察，数据很像多项式曲线。

![](img/e5852ce41176b046343c1b115ebda7a6_19.png)

```python
# 使用多项式核函数
svr_poly = SVR(kernel='poly')
svr_poly.fit(x_reshaped, y)
y_pred_poly = svr_poly.predict(x_test)

plt.scatter(x, y, color='red', label='Actual Data')
plt.plot(x_test.ravel(), y_pred_poly, color='orange', label='Poly SVR Prediction')
plt.legend()
plt.show()
```

![](img/e5852ce41176b046343c1b115ebda7a6_21.png)

使用多项式核函数后，拟合效果显著提升，曲线基本通过了所有数据点。

---

## 调整模型参数 ⚙️

![](img/e5852ce41176b046343c1b115ebda7a6_23.png)

我们看到多项式核函数效果不错，但仍有优化空间。SVR的多项式核函数有一些关键参数可以调整。

多项式核函数的公式可以表示为：
`K(x, x') = (coef0 + γ * <x, x'>)^degree`

其中：
*   `degree` 控制多项式的幂次。
*   `coef0` 代表公式中的常数项`c`，它会影响曲线的截距和形状。

![](img/e5852ce41176b046343c1b115ebda7a6_25.png)

在代码中，我们可以通过`coef0`参数来调整拟合效果。

```python
# 调整多项式核函数的coef0参数
svr_poly_tuned = SVR(kernel='poly', coef0=200)
svr_poly_tuned.fit(x_reshaped, y)
y_pred_poly_tuned = svr_poly_tuned.predict(x_test)

![](img/e5852ce41176b046343c1b115ebda7a6_27.png)

plt.scatter(x, y, color='red', label='Actual Data')
plt.plot(x_test.ravel(), y_pred_poly_tuned, color='purple', label='Tuned Poly SVR')
plt.legend()
plt.show()
```

![](img/e5852ce41176b046343c1b115ebda7a6_29.png)

将`coef0`从默认值0调整为200后，拟合曲线更好地穿过了下方的几个数据点，整体拟合效果更优。

![](img/e5852ce41176b046343c1b115ebda7a6_31.png)

需要注意的是，`coef0`这个系数仅在多项式核函数中有效。对于线性核函数，模型有另一个属性`.coef_`来获取权重系数；而对于`rbf`和`poly`核函数，则没有`.coef_`属性。

![](img/e5852ce41176b046343c1b115ebda7a6_33.png)

```python
# 获取线性模型的系数
print(svr_linear.coef_)
# 尝试获取多项式模型的系数会报错
# print(svr_poly.coef_)
```

![](img/e5852ce41176b046343c1b115ebda7a6_35.png)

---

## 总结 📝

本节课中我们一起学习了如何使用支持向量机回归模型解决实际问题。

我们首先准备了天猫双十一的销售额数据，并通过可视化确认其符合多项式趋势。接着，我们尝试了SVR的三种不同核函数：
1.  **线性核函数**：只能拟合直线，不适合本数据。
2.  **高斯核函数**：更适合局部拟合，整体效果不佳。
3.  **多项式核函数**：成功捕捉了数据的曲线趋势，拟合效果良好。

![](img/e5852ce41176b046343c1b115ebda7a6_37.png)

最后，我们通过调整多项式核函数的`coef0`参数，进一步优化了模型的拟合效果，使预测曲线更贴近所有真实数据点。这个案例表明，选择合适的核函数并调整其参数，对于SVR模型成功解决回归问题至关重要。