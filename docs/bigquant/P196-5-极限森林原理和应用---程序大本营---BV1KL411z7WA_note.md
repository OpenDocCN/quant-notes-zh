# 机器学习：P196：极限森林原理与应用 🌲

![](img/66cdcfd79f5050256109a3af0591a822_1.png)

![](img/66cdcfd79f5050256109a3af0591a822_3.png)

![](img/66cdcfd79f5050256109a3af0591a822_5.png)

在本节课中，我们将学习集成学习中的极限森林算法。极限森林是随机森林的一种变体，它在随机性的应用上更进一步。我们将通过对比随机森林，理解其核心原理，并使用代码进行实践，最后评估其性能。

## 极限森林的核心原理 🧠

上一节我们介绍了随机森林，本节中我们来看看极限森林。极限森林在随机森林的基础上，对特征分裂的方式引入了更强的随机性。

在sklearn的官方文档中，极限森林被描述为“goes one step further”。这意味着它在计算分裂节点时，向前更进了一步。具体来说，随机森林在选择最佳分裂特征时，会基于信息增益等指标进行评估；而极限森林则在这个选择过程中加入了随机性，随机地为每个候选特征生成分裂阈值。

这种设计类似于社会选拔人才不仅看单一标准（如高考分数），而是进行更综合的评判。通过增加分裂的随机性，极限森林中的每棵树差异可能更大，集成后的模型可能具有更好的泛化能力。

## 代码实践：使用极限森林 📊

![](img/66cdcfd79f5050256109a3af0591a822_7.png)

理解了原理后，我们通过代码来实践。我们将使用与之前相同的葡萄酒数据集，对比极限森林与随机森林、单棵决策树的表现。

首先，导入必要的库并加载数据。

```python
from sklearn.ensemble import ExtraTreesClassifier
from sklearn.model_selection import train_test_split
from sklearn import datasets

# 加载葡萄酒数据集
wine = datasets.load_wine()
X = wine.data
y = wine.target
```

接下来，我们使用极限森林进行训练和评估。以下是核心步骤：

1.  分割数据集为训练集和测试集。
2.  创建极限森林分类器。
3.  进行多次训练以获取平均得分，确保结果稳定。

```python
# 初始化总得分
total_score = 0
n_trials = 100

![](img/66cdcfd79f5050256109a3af0591a822_9.png)

for i in range(n_trials):
    # 分割数据
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3)

    # 创建并训练极限森林模型
    clf_et = ExtraTreesClassifier(n_estimators=100)
    clf_et.fit(X_train, y_train)

    # 累计得分
    total_score += clf_et.score(X_test, y_test)

![](img/66cdcfd79f5050256109a3af0591a822_11.png)

# 计算平均得分
average_score_et = total_score / n_trials
print(f"极限森林的平均得分是：{average_score_et}")
```

运行代码后，我们得到极限森林的平均得分约为 **0.984**。对比之前的结果（随机森林：0.98，单棵决策树：0.907），可以看到极限森林的性能有轻微提升。在已经很高的基准上实现提升，证明了其“更进一步”的价值。

![](img/66cdcfd79f5050256109a3af0591a822_13.png)

## 模型可视化与参数探索 👀

为了更直观地理解极限森林，我们可以将其中的决策树可视化。同时，我们探索一个重要参数 `max_depth`（树的最大深度）的影响。

![](img/66cdcfd79f5050256109a3af0591a822_15.png)

以下是可视化及控制树深度的代码：

![](img/66cdcfd79f5050256109a3af0591a822_17.png)

```python
from sklearn.tree import export_graphviz
import graphviz

![](img/66cdcfd79f5050256109a3af0591a822_19.png)

# 1. 可视化第一棵树（未限制深度）
dot_data = export_graphviz(clf_et.estimators_[0], filled=True, rounded=True)
graph = graphviz.Source(dot_data)
graph.render("et_tree_0")  # 保存为文件
graph  # 在Jupyter Notebook中显示

![](img/66cdcfd79f5050256109a3af0591a822_21.png)

# 2. 创建深度限制为3的极限森林模型
clf_et_limited = ExtraTreesClassifier(n_estimators=100, max_depth=3)
clf_et_limited.fit(X_train, y_train)

![](img/66cdcfd79f5050256109a3af0591a822_23.png)

![](img/66cdcfd79f5050256109a3af0591a822_25.png)

# 可视化深度受限后的第一棵树
dot_data_limited = export_graphviz(clf_et_limited.estimators_[0], filled=True, rounded=True)
graph_limited = graphviz.Source(dot_data_limited)
graph_limited.render("et_tree_0_limited")
graph_limited
```

![](img/66cdcfd79f5050256109a3af0591a822_27.png)

通过可视化对比，我们可以发现两个关键点：
1.  极限森林默认会使用**所有的训练样本**来构建每棵树，这与随机森林的Bootstrap抽样不同。
2.  设置 `max_depth=3` 后，所有树的深度都被有效限制，模型结构更简单，可能有助于防止过拟合，但得分可能会略微下降。

![](img/66cdcfd79f5050256109a3af0591a822_29.png)

![](img/66cdcfd79f5050256109a3af0591a822_31.png)

## 关于多分类问题的说明 🔢

![](img/66cdcfd79f5050256109a3af0591a822_33.png)

![](img/66cdcfd79f5050256109a3af0591a822_35.png)

![](img/66cdcfd79f5050256109a3af0591a822_37.png)

我们的葡萄酒数据集是一个三分类问题。无论是决策树、随机森林还是极限森林，处理多分类问题时，核心的评估公式（如基尼系数或信息熵）在原理上保持不变。算法会自动处理多个类别。

![](img/66cdcfd79f5050256109a3af0591a822_39.png)

![](img/66cdcfd79f5050256109a3af0591a822_41.png)

我们可以通过以下代码快速查看数据集的类别分布：

![](img/66cdcfd79f5050256109a3af0591a822_43.png)

![](img/66cdcfd79f5050256109a3af0591a822_45.png)

```python
import numpy as np
unique, counts = np.unique(y, return_counts=True)
print("类别标签及数量：", dict(zip(unique, counts)))
```

![](img/66cdcfd79f5050256109a3af0591a822_47.png)

## 总结 📝

![](img/66cdcfd79f5050256109a3af0591a822_49.png)

![](img/66cdcfd79f5050256109a3af0591a822_51.png)

本节课中我们一起学习了极限森林算法。

![](img/66cdcfd79f5050256109a3af0591a822_53.png)

![](img/66cdcfd79f5050256109a3af0591a822_55.png)

*   **原理**：极限森林在随机森林的基础上，进一步在**特征分裂点**的选择上引入随机性（`goes one step further`），并使用**全部样本**进行训练，旨在增加基学习器的多样性，提升集成效果。
*   **实践**：我们使用 `ExtraTreesClassifier` 实现了极限森林，其在葡萄酒数据集上的表现略优于随机森林。
*   **探索**：我们通过可视化观察了树的结构，并了解了 `max_depth` 参数对模型复杂度的控制作用。
*   **应用**：极限森林适用于各类分类与回归任务，尤其在希望进一步提升模型性能或增强鲁棒性时是一个很好的选择。

![](img/66cdcfd79f5050256109a3af0591a822_57.png)

至此，我们完成了对集成学习中随机森林与极限森林的介绍。理解它们如何在“集体决策”中通过不同的随机策略获得优于单一模型的力量，是掌握集成学习的关键。