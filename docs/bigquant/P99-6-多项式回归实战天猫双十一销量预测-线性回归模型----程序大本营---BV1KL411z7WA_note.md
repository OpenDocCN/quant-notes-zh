# 机器学习实战：6：多项式回归实战天猫双十一销量预测

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_0.png)

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_2.png)

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_4.png)

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_6.png)

在本节课中，我们将要学习如何运用多项式回归模型，对天猫双十一历年销售额数据进行拟合与预测。我们将从数据准备开始，逐步完成升维、模型训练与评估，最终得到一个能够描述销量增长规律的数学模型。

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_8.png)

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_10.png)

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_12.png)

## 数据背景与目标

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_14.png)

上一节我们介绍了多项式回归的基本原理，本节中我们来看看如何将其应用于一个真实的数据集。

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_16.png)

天猫双十一购物节自2009年首次举办以来，销售额呈现爆发式增长。这些增长数据背后是否存在某种数学规律？我们希望通过多项式回归模型来寻找答案。

以下是历年天猫双十一的销售额数据（单位：亿元）：
*   2009年：0.5
*   2010年：9.36
*   2011年：52
*   2012年：191
*   2013年：350
*   2014年：571
*   2015年：912
*   2016年：1207
*   2017年：1682
*   2018年：2135
*   2019年：2684

我们的目标是使用2009年至2019年这11年的数据，拟合一个多项式模型，并尝试理解其增长趋势。

## 数据预处理与初步探索

首先，我们需要导入必要的库并加载数据。

```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.preprocessing import PolynomialFeatures
from sklearn.linear_model import LinearRegression

# 定义年份和销售额数据
x = np.array([2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018, 2019])
y = np.array([0.5, 9.36, 52, 191, 350, 571, 912, 1207, 1682, 2135, 2684])

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_18.png)

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_20.png)

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_22.png)

# 绘制原始数据散点图
plt.scatter(x, y)
plt.title('Tmall Double 11 Sales (2009-2019)')
plt.xlabel('Year')
plt.ylabel('Sales (100 million yuan)')
plt.show()
```

运行代码后，我们可以看到销售额随年份增长的曲线非常平滑，这提示我们多项式回归可能是一个合适的拟合工具。

## 关键步骤：特征工程与数据缩放

在直接使用年份数据进行多项式拟合前，我们需要解决一个关键问题：原始年份数值过大（如2009, 2010）。这会导致低次项（如一次项 `x`）与高次项（如 `x^2`）的数值尺度差异巨大，使得模型难以稳定收敛。

解决方案是对年份特征进行缩放。一个简单有效的方法是让所有年份减去一个基准值（例如2008），从而将特征值转换为从1开始的序列。

```python
# 对年份特征进行缩放，减去2008
x_scaled = x - 2008
print("Scaled year features:", x_scaled)
# 输出: [ 1  2  3  4  5  6  7  8  9 10 11]
```

经过缩放，特征值变为1到11，代表了第1年到第11年。这样，不同次幂之间的数值尺度更加均衡，有利于模型训练。

## 构建与训练多项式回归模型

接下来，我们对缩放后的特征进行多项式升维，然后使用线性回归模型进行拟合。

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_24.png)

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_26.png)

以下是构建和训练模型的核心步骤：

1.  **多项式特征转换**：使用 `PolynomialFeatures` 将一维的年份特征转换为多项式特征（例如，包含 `x`, `x^2`, `x^3` 等）。
2.  **创建线性回归模型**：我们使用 `LinearRegression`，并设置 `fit_intercept=False`。因为在多项式特征中，我们已经包含了常数项（`include_bias=True` 时），无需模型再计算截距。
3.  **模型训练**：使用升维后的特征和对应的销售额数据训练模型。

```python
# 1. 多项式特征升维 (以2次多项式为例)
poly = PolynomialFeatures(degree=2, include_bias=True)
x_poly = poly.fit_transform(x_scaled.reshape(-1, 1)) # 注意: 输入需要是二维数组

# 2. 创建并训练线性回归模型
model = LinearRegression(fit_intercept=False)
model.fit(x_poly, y)

# 3. 查看模型系数
print("Model coefficients:", model.coef_)
# 系数对应 [常数项, x, x^2] 的系数
```

## 模型预测与可视化

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_28.png)

模型训练完成后，我们可以生成一个平滑的曲线来观察拟合效果，并与原始数据点进行对比。

```python
# 生成用于绘制平滑曲线的测试数据点 (年份从2009到2020)
x_test = np.linspace(2009, 2020, 50)
x_test_scaled = x_test - 2008 # 同样需要进行缩放
x_test_poly = poly.transform(x_test_scaled.reshape(-1, 1)) # 升维

# 使用模型进行预测
y_pred = model.predict(x_test_poly)

# 可视化结果
plt.scatter(x, y, label='Actual Data')
plt.plot(x_test, y_pred, 'r-', label='Polynomial Fit (degree=2)')
plt.title('Tmall Double 11 Sales Prediction')
plt.xlabel('Year')
plt.ylabel('Sales (100 million yuan)')
plt.legend()
plt.grid(True)
plt.show()
```

运行代码后，我们将看到一条红色的多项式拟合曲线很好地穿过了蓝色的历史数据点，清晰地展示了销售额的增长趋势。

## 模型结果与方程

通过打印模型的系数，我们可以得到拟合出的多项式方程。例如，当 `degree=2` 时，我们可能得到如下形式的方程：

**拟合方程示例：**
`销售额(y) ≈ 30.23 * (年份-2008)^2 - 95.53 * (年份-2008) + 72.66`

这个方程量化了天猫双十一销售额与时间（以2008年为基准）之间的二次关系。你可以尝试调整 `degree` 参数（如设为3），观察三次多项式拟合的效果。根据奥卡姆剃刀原则，在拟合效果相近时，我们应优先选择更简单的模型（次数更低）。

## 总结

![](img/1a1daf512e65c3ed0f94ef9289bcacaa_30.png)

本节课中我们一起学习了如何将多项式回归应用于实际预测问题。我们以天猫双十一销售额预测为例，完整经历了数据加载、特征工程（关键的年份缩放）、多项式升维、模型训练、预测与可视化的全过程。关键在于理解对于像年份这类数值较大的特征，进行适当的缩放（如减去一个基准值）是保证多项式模型稳定训练的重要步骤。通过这个实战案例，希望你能够掌握多项式回归解决非线性拟合问题的基本流程。