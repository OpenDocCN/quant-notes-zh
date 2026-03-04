# 机器学习：P188：决策回归树算法示例演示 🧮

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_0.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_2.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_4.png)

在本节课中，我们将通过一个具体的代码示例，学习如何使用决策树进行回归任务。我们将创建一组数据，分别用线性回归、支持向量机回归和决策树回归进行预测，并直观地比较它们的效果，从而理解决策树回归的工作原理和优势。

## 导入必要的库 📦

首先，我们需要导入实现算法和可视化所需的Python库。

```python
from sklearn import tree
from sklearn.tree import DecisionTreeRegressor
from sklearn import linear_model
from sklearn.svm import SVR
import numpy as np
import matplotlib.pyplot as plt
import graphviz
```

## 创建示例数据 📊

上一节我们导入了必要的工具，本节中我们来看看如何创建用于演示的数据。我们将生成一个基于正弦和余弦函数的圆形数据集。

以下是创建训练和测试数据的步骤：
*   使用 `np.linspace` 生成一个从0到2π的等差数列作为特征 `x_train`，并将其形状重塑为 `(-1, 1)` 以满足机器学习算法对二维输入的要求。
*   通过计算 `x_train` 的正弦和余弦值，并将它们在最后一个轴（axis=-1）上拼接，得到目标值 `y_train`。这构成了一个圆形数据。
*   为了验证模型泛化能力，我们生成一个更密集的测试集 `x_test`（256个点）。

```python
# 创建训练数据
x_train = np.linspace(0, 2 * np.pi, 40).reshape(-1, 1)
y_train = np.concatenate([np.sin(x_train), np.cos(x_train)], axis=-1)

# 创建测试数据
x_test = np.linspace(0, 2 * np.pi, 256).reshape(-1, 1)
```

我们可以通过散点图可视化 `y_train`，可以看到它形成了一个圆形。

```python
plt.figure(figsize=(4, 4))
plt.scatter(y_train[:, 0], y_train[:, 1])
plt.axis('equal') # 设置横纵坐标轴比例相等，以显示正圆
plt.show()
```

## 尝试线性回归模型 📉

在引入决策树之前，我们先使用线性回归模型来尝试拟合这个非线性关系，观察其局限性。

以下是线性回归模型的构建与预测过程：
1.  从 `sklearn.linear_model` 导入 `LinearRegression`。
2.  实例化模型并使用 `x_train` 和 `y_train` 进行训练（`fit`）。
3.  使用训练好的模型对 `x_test` 进行预测（`predict`），得到 `y_predict_linear`。
4.  可视化预测结果。

```python
# 线性回归
from sklearn.linear_model import LinearRegression
model_linear = LinearRegression()
model_linear.fit(x_train, y_train)
y_predict_linear = model_linear.predict(x_test)

plt.figure(figsize=(4, 4))
plt.scatter(y_predict_linear[:, 0], y_predict_linear[:, 1])
plt.axis('equal')
plt.title("Linear Regression Prediction")
plt.show()
```

运行代码后会发现，预测结果是一条直线，无法拟合圆形数据。这是因为线性回归模型本质是寻找特征的线性组合，难以捕捉复杂的非线性模式。

## 尝试支持向量机回归（SVR）🔧

接下来，我们看看另一种常用于回归的算法——支持向量机回归（SVR）在此问题上的表现。需要注意的是，SVR 要求目标值 `y` 是一维的，而我们的 `y_train` 是二维的，因此需要分别对两个维度进行建模或选择其他方法，这里直接尝试会引发错误。

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_6.png)

```python
# 支持向量机回归 (此示例会因y的维度问题而报错，仅作演示)
# from sklearn.svm import SVR
# model_svr = SVR(kernel='rbf') # 尝试使用高斯核
# model_svr.fit(x_train, y_train) # 这里会报错：y必须是1维的
```

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_8.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_10.png)

这表明 SVR 对于当前形式的数据处理起来不太方便。

## 使用决策树回归 🌳

现在，让我们使用本节课的主角——决策树回归模型。决策树通过递归地划分特征空间来构建模型，非常适合处理非线性关系。

以下是决策树回归的建模与预测过程：
1.  实例化 `DecisionTreeRegressor`。关键参数 `max_depth` 用于控制树的最大深度，防止过拟合。
2.  使用训练数据拟合模型。
3.  用模型预测测试数据。
4.  可视化预测结果。

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_12.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_14.png)

```python
# 决策树回归，设置最大深度为3
model_dt = DecisionTreeRegressor(max_depth=3)
model_dt.fit(x_train, y_train)
y_predict_dt = model_dt.predict(x_test)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_16.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_18.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_20.png)

plt.figure(figsize=(4, 4))
plt.scatter(y_predict_dt[:, 0], y_predict_dt[:, 1])
plt.axis('equal')
plt.title(f"Decision Tree Regression (max_depth={model_dt.get_depth()})")
plt.show()
```

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_22.png)

可以看到，当 `max_depth=3` 时，预测的点形成了8个簇，开始近似圆形，效果远好于线性回归。

## 可视化决策树结构 🖼️

决策树的一个显著优点是模型可解释性强。我们可以将构建好的树结构可视化出来。

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_24.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_26.png)

以下是可视化决策树的代码：
```python
# 导出决策树图形
dot_data = tree.export_graphviz(model_dt, out_file=None,
                                filled=True, rounded=True)
graph = graphviz.Source(dot_data)
graph
```
执行后，你会看到一棵深度为3的树形图，每个节点显示了划分条件、样本数、MSE（均方误差）值以及当前节点的预测值。叶节点（最终预测值）的数量是 $2^{3} = 8$ 个，这与散点图中看到的8个簇相对应。

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_28.png)

## 调整决策树深度 🔍

树的深度是控制模型复杂度的关键参数。深度太小可能导致欠拟合（无法捕捉数据规律），深度太大可能导致过拟合（过度记忆训练数据噪声）。

以下是不同深度设置的对比实验：
*   **深度为4**：`max_depth=4`。此时叶节点数为 $2^{4}=16$ 个，预测点形成16个簇，图形更接近圆形。
*   **不限制深度**：不设置 `max_depth`（或设为 `None`），让树完全生长直到“纯净”（每个叶节点内样本的目标值方差为0）。对于40个训练样本，树最终深度为6，产生 $2^{6}=64$ 个可能的叶节点（实际未完全使用），在训练集上得分（R²分数）为1.0，表示完美拟合了训练数据。

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_30.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_32.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_34.png)

```python
# 深度为4的树
model_dt_4 = DecisionTreeRegressor(max_depth=4)
model_dt_4.fit(x_train, y_train)
y_predict_dt_4 = model_dt_4.predict(x_test)
# ... 可视化代码

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_36.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_38.png)

# 不限制深度（完全生长）
model_dt_full = DecisionTreeRegressor()
model_dt_full.fit(x_train, y_train)
print(f"模型深度：{model_dt_full.get_depth()}")
print(f"训练集得分（R²）: {model_dt_full.score(x_train, y_train)}")
# ... 可视化代码
```

通过调整深度，我们可以直观地理解模型复杂度与拟合效果之间的权衡。

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_40.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_42.png)

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_44.png)

## 总结 📝

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_46.png)

本节课中我们一起学习了决策树回归算法的实际应用。我们通过一个圆形数据集的示例，演示了以下内容：
1.  **数据准备**：创建了非线性的训练和测试数据集。
2.  **模型对比**：尝试了线性回归和SVR，发现它们在此非线性问题上存在局限。
3.  **决策树回归**：使用 `DecisionTreeRegressor` 成功拟合了数据，并通过调整 `max_depth` 参数控制了模型复杂度。
4.  **模型可视化**：利用 `graphviz` 库将决策树结构图形化，增强了模型的可解释性。
5.  **核心概念**：决策树通过**递归划分特征空间**来工作，使用**均方误差（MSE）** 等指标选择最佳划分点。其预测结果是每个**叶节点**内所有样本目标值的**平均值**。

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_48.png)

关键公式：在回归树中，常用**均方误差（MSE）** 作为节点划分的标准：
$$MSE = \frac{1}{n}\sum_{i=1}^{n}(y_i - \hat{y})^2$$
其中，$\hat{y}$ 是该节点中所有样本 $y_i$ 的平均值。算法会选择能使划分后子节点MSE总和降低最多的特征和阈值。

![](img/19687cd5b1ad49ce5fa5ee2f5fe0e402_50.png)

这个示例清晰地展示了决策树如何处理复杂的、非线性的回归问题，并强调了通过剪枝（如设置最大深度）来防止过拟合的重要性。