# 机器学习：P195：随机森林原理与应用 🌲🌳

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_1.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_3.png)

在本节课中，我们将要学习集成学习中的一种重要算法——随机森林。我们将了解它的核心原理、两种关键的随机性，并通过代码实践来对比其与单一决策树的性能差异。

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_5.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_7.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_9.png)

## 概述

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_11.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_13.png)

集成算法的核心思想是“取长补短”。随机森林是其中一种代表算法，它结合了Bagging（套袋法）和决策树。Bagging方法通过对训练集进行有放回的随机抽样，并行训练多个模型，最后汇总结果。

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_15.png)

## 随机森林的“随机”性

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_17.png)

上一节我们介绍了集成学习的概念，本节中我们来看看随机森林具体“随机”在哪里。随机森林之所以称为“森林”，是因为它由多棵决策树组成。其“随机”性主要体现在两个方面：

根据scikit-learn官方文档的描述，随机森林包含以下两种随机性：

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_19.png)

1.  **随机抽样**：从训练集中有放回地随机抽取样本用于训练每棵树。这保证了每次抽样的样本集通常不同。
2.  **特征随机**：在构建每棵树的每个节点时，并非从所有特征中选择最优划分，而是从一个随机子集中选择。这增加了树的多样性。

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_21.png)

这两种随机性分别对应数据样本（行）的不同和特征（列）的不同，是随机森林减少模型方差、防止过拟合的关键。

## 随机森林的优势与工作流程

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_23.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_25.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_27.png)

随机森林通过引入上述随机性，有效降低了模型的方差，使预测结果更加稳定，从而防止过拟合。

以下是随机森林的主要工作步骤：
1.  从原始训练集中进行有放回的随机抽样，生成多个子训练集。
2.  为每个子训练集建立一棵决策树。在树节点分裂时，从所有特征中随机选取一个子集，并从中选择最佳分裂特征。
3.  让森林中的所有树对样本进行预测。
4.  对于分类任务，采用**投票法**汇总结果：统计每棵树预测的类别，票数最多的类别即为最终预测结果。其核心思想可以用“三个臭皮匠，顶个诸葛亮”来比喻。

## 代码实践：对比决策树与随机森林

理论介绍完毕，现在让我们通过代码来直观感受随机森林的效果。我们将使用scikit-learn库，对比单一决策树和随机森林在经典数据集上的表现。

首先，导入必要的库和模块。

```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.tree import DecisionTreeClassifier, export_graphviz
from sklearn import datasets
from sklearn.model_selection import train_test_split
import graphviz
```

加载数据并进行训练集/测试集划分。为了充分展示效果，我们使用特征维度更高的葡萄酒数据集。

```python
# 加载葡萄酒数据集
wine = datasets.load_wine()
X = wine.data
y = wine.target

# 划分训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.25, random_state=42)
```

### 单一决策树的表现

我们首先训练一棵决策树，并观察其准确率。

```python
# 创建并训练决策树模型
model_tree = DecisionTreeClassifier()
model_tree.fit(X_train, y_train)
score_tree = model_tree.score(X_test, y_test)
print(f"单一决策树得分: {score_tree:.4f}")
```

由于单次数据划分具有随机性，为了得到更稳定的评估，我们进行100次实验并计算平均得分。

```python
scores_tree = 0
for i in range(100):
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.25)
    model_tree = DecisionTreeClassifier()
    model_tree.fit(X_train, y_train)
    scores_tree += model_tree.score(X_test, y_test)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_29.png)

print(f"单一决策树平均得分: {scores_tree / 100:.4f}")
```

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_31.png)

### 随机森林的表现

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_33.png)

接下来，我们使用随机森林。关键参数 `n_estimators` 用于指定森林中树的数量。

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_35.png)

```python
# 创建并训练随机森林模型，包含100棵树
clf_rf = RandomForestClassifier(n_estimators=100)
clf_rf.fit(X_train, y_train)
score_rf = clf_rf.score(X_test, y_test)
print(f"随机森林得分: {score_rf:.4f}")
```

同样，我们进行100次实验来计算随机森林的平均得分，以确保比较的公平性。

```python
scores_rf = 0
for i in range(100):
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.25)
    clf_rf = RandomForestClassifier(n_estimators=100)
    clf_rf.fit(X_train, y_train)
    scores_rf += clf_rf.score(X_test, y_test)

print(f"随机森林平均得分: {scores_rf / 100:.4f}")
```

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_37.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_39.png)

**运行结果分析**：在葡萄酒数据集上，随机森林的平均得分通常会显著高于单一决策树。这是因为葡萄酒数据有13个特征，随机森林的“特征随机”机制能充分发挥作用，构建出多样性更强的树，从而提升整体性能。而在特征数较少的数据集上，两者的性能差距可能不明显。

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_41.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_43.png)

## 深入理解：概率输出与模型可视化

为了更深入地理解两者的区别，我们可以观察它们的预测概率输出。

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_45.png)

```python
# 决策树的预测概率（通常是0或1）
proba_tree = model_tree.predict_proba(X_test)
print("决策树预测概率示例（前5个样本）:\n", proba_tree[:5])

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_47.png)

# 随机森林的预测概率（由投票比例决定）
proba_rf = clf_rf.predict_proba(X_test)
print("随机森林预测概率示例（前5个样本）:\n", proba_rf[:5])
```

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_49.png)

随机森林的概率输出是各棵树投票结果的归一化比例，例如 `[0.91, 0.07, 0.02]` 表示100棵树中，有91票投给类别0，7票投给类别1，2票投给类别2。

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_51.png)

最后，我们可以可视化模型结构。

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_53.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_55.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_57.png)

```python
# 可视化单一决策树
dot_data_tree = export_graphviz(model_tree, out_file=None,
                                filled=True, rounded=True)
graph_tree = graphviz.Source(dot_data_tree)
graph_tree.render("decision_tree")  # 保存为PDF文件
graph_tree  # 在Jupyter Notebook中显示

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_59.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_61.png)

# 可视化随机森林中的某一棵树（例如第0棵）
dot_data_rf_0 = export_graphviz(clf_rf.estimators_[0], out_file=None,
                                filled=True, rounded=True)
graph_rf_0 = graphviz.Source(dot_data_rf_0)
graph_rf_0.render("random_forest_tree_0")
graph_rf_0
```

通过可视化可以发现，随机森林中每棵树的训练样本数可能少于原始训练集，这正是“随机抽样”的体现。同时，每棵树都是一棵独立的决策树。

## 总结

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_63.png)

本节课中我们一起学习了随机森林算法。我们了解到：

1.  **核心原理**：随机森林是Bagging与决策树的结合，通过构建多棵树并汇总结果来提升性能。
2.  **两种随机性**：
    *   **随机抽样**：行随机，保证样本多样性。
    *   **特征随机**：列随机，保证特征选择多样性。这两种随机性是降低方差、防止过拟合的关键。
3.  **工作流程**：并行构建多棵决策树，并通过投票法（分类）或平均法（回归）进行集成预测。
4.  **优势**：能有效处理高维数据，结果更稳定，且可以并行训练，效率较高。
5.  **实践对比**：通过代码验证，在特征丰富的数据集上，随机森林通常能获得比单一决策树更优且更稳定的表现。

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_65.png)

![](img/c8b2e23ed8b5c03d093b4e8ca398895d_67.png)

随机森林以其强大的性能和易于使用的特点，成为了机器学习中一个非常实用且流行的工具。