# 机器学习实战：P185：决策树鸢尾花分类案例 🌸

![](img/65c768ea420a3ba19e6ece194a54dbe3_0.png)

![](img/65c768ea420a3ba19e6ece194a54dbe3_2.png)

在本节课中，我们将学习如何使用决策树算法对经典的鸢尾花数据集进行分类。我们将从数据加载开始，逐步完成模型训练、评估和可视化，并解释决策树预测结果的特点。

![](img/65c768ea420a3ba19e6ece194a54dbe3_4.png)

---

## 数据准备与加载

首先，我们需要导入必要的库并加载鸢尾花数据集。

![](img/65c768ea420a3ba19e6ece194a54dbe3_6.png)

```python
import numpy as np
from sklearn import datasets
from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier
import graphviz
```

![](img/65c768ea420a3ba19e6ece194a54dbe3_8.png)

以下是加载数据的具体步骤：

1.  使用 `datasets.load_iris()` 加载数据集。
2.  将数据特征赋值给变量 `X`。
3.  将数据标签（目标值）赋值给变量 `y`。
4.  获取特征名称，用于后续的可视化。

```python
# 加载鸢尾花数据集
iris = datasets.load_iris()
X = iris.data
y = iris.target
feature_names = iris.feature_names
```

## 划分训练集与测试集

为了评估模型的泛化能力，我们需要将数据划分为训练集和测试集。

上一节我们加载了原始数据，本节中我们来看看如何将其划分为训练集和测试集。我们使用 `train_test_split` 函数，将 80% 的数据用于训练，20% 用于测试，并设置一个随机种子以确保结果可复现。

```python
# 划分数据集，80%训练，20%测试
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=256)
```

## 训练决策树模型

数据准备就绪后，我们可以创建并训练决策树分类器。

以下是创建和训练决策树模型的步骤：

1.  实例化一个 `DecisionTreeClassifier` 对象。
2.  使用训练数据 `X_train` 和 `y_train` 来训练（拟合）模型。

```python
# 创建决策树分类器并训练
model = DecisionTreeClassifier()
model.fit(X_train, y_train)
```

## 模型评估与预测

模型训练完成后，我们需要评估其在未见过的测试数据上的表现。

现在模型已经训练好，本节中我们来看看它的预测准确度如何。我们将使用测试集进行预测，并计算模型的准确率得分。

```python
# 在测试集上评估模型
score = model.score(X_test, y_test)
print(“测试数据得分：”, score)

![](img/65c768ea420a3ba19e6ece194a54dbe3_10.png)

# 进行预测
y_pred = model.predict(X_test)
print(“算法预测结果：”, y_pred)
print(“真实结果：”, y_test)
```

执行代码后，可以看到模型在测试集上的准确率（例如 0.96），并对比预测结果与真实标签，观察哪些样本被错误分类。

![](img/65c768ea420a3ba19e6ece194a54dbe3_12.png)

## 理解决策树的预测概率

![](img/65c768ea420a3ba19e6ece194a54dbe3_14.png)

决策树的一个特点是其预测概率的输出方式。

上一节我们评估了模型的整体性能，本节中我们深入探讨一个细节：决策树预测的概率值。我们使用 `predict_proba` 方法来查看模型对每个测试样本属于各个类别的概率估计。

![](img/65c768ea420a3ba19e6ece194a54dbe3_16.png)

```python
# 查看预测概率
probabilities = model.predict_proba(X_test)
print(“预测概率：\n”, probabilities)
```

![](img/65c768ea420a3ba19e6ece194a54dbe3_18.png)

![](img/65c768ea420a3ba19e6ece194a54dbe3_20.png)

你会发现，决策树输出的概率值通常是极端的（例如 0.0 或 1.0），而不是像逻辑回归那样平滑的概率（如 0.8、0.2）。这是因为：

![](img/65c768ea420a3ba19e6ece194a54dbe3_22.png)

![](img/65c768ea420a3ba19e6ece194a54dbe3_24.png)

> 决策树在划分时，每个样本最终都会落到一个确定的叶子节点上。该叶子节点所代表的类别就被认为是该样本 100% 所属的类别，因此其预测概率非 0 即 1。

![](img/65c768ea420a3ba19e6ece194a54dbe3_26.png)

## 可视化决策树

![](img/65c768ea420a3ba19e6ece194a54dbe3_28.png)

为了更直观地理解模型的决策过程，我们可以将训练好的决策树可视化。

![](img/65c768ea420a3ba19e6ece194a54dbe3_30.png)

![](img/65c768ea420a3ba19e6ece194a54dbe3_31.png)

理解了这个概念后，让我们直观地看看这棵决策树长什么样。我们使用 `graphviz` 库来生成决策树的结构图。

![](img/65c768ea420a3ba19e6ece194a54dbe3_33.png)

```python
# 可视化决策树
dot_data = tree.export_graphviz(model,
                                out_file=None,
                                feature_names=feature_names,
                                class_names=iris.target_names,
                                filled=True,
                                rounded=True)
graph = graphviz.Source(dot_data)
graph.render(“iris_decision_tree”) # 保存为PDF文件
graph # 在Jupyter Notebook中显示
```

生成的树形图会展示从根节点到叶子节点的完整决策路径，包括每个节点使用的特征、划分阈值、基尼系数或信息熵、样本数量以及最终的类别分布。这有助于我们理解模型是如何做出分类决定的。

![](img/65c768ea420a3ba19e6ece194a54dbe3_35.png)

---

![](img/65c768ea420a3ba19e6ece194a54dbe3_37.png)

![](img/65c768ea420a3ba19e6ece194a54dbe3_39.png)

本节课中我们一起学习了使用决策树进行鸢尾花分类的完整流程。我们从数据加载和划分开始，训练了决策树模型，并评估了其性能。特别地，我们探讨了决策树预测概率的特点，并通过可视化理解了其内部的决策逻辑。决策树是一种直观且强大的分类工具，适合作为机器学习入门的第一个实战案例。