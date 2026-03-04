# 机器学习：P178：决策树的应用和可视化 🌳

在本节课中，我们将学习如何使用Python的Scikit-learn库来构建一个决策树分类模型，并将其可视化。我们将通过一个判断社交媒体账号真伪的实例，演示从数据准备、模型训练到结果可视化的完整流程。

## 加载数据 📊

首先，我们需要导入必要的库并加载数据。数据包含三个特征：`日志密度`、`好友密度`、`是否使用真实头像`，以及一个目标值`账号是否真实`。

```python
import numpy as np
import pandas as pd
from sklearn.tree import DecisionTreeClassifier

# 创建示例数据
data = {
    '日志密度': ['s', 's', 'm', 'm', 'm', 'l', 'l', 'l'],
    '好友密度': ['s', 's', 's', 'm', 'm', 'l', 'l', 'l'],
    '真实头像': ['n', 'y', 'n', 'n', 'y', 'y', 'n', 'y'],
    '真实用户': ['no', 'no', 'yes', 'yes', 'yes', 'no', 'no', 'yes']
}
df = pd.DataFrame(data)
```

## 数据转换 🔄

上一节我们加载了数据，但数据是文本格式，无法直接用于数学计算。本节中，我们需要将文本数据转换为数值。

以下是数据转换的步骤，我们将字符串特征映射为数字：

```python
# 将文本数据转换为数值
df['日志密度'] = df['日志密度'].map({'s': 0, 'm': 1, 'l': 2})
df['好友密度'] = df['好友密度'].map({'s': 0, 'm': 1, 'l': 2})
df['真实头像'] = df['真实头像'].map({'n': 0, 'y': 1})

# 分离特征和目标值
X = df[['日志密度', '好友密度', '真实头像']]
y = df['真实用户']
```

## 建模和预测 🤖

数据准备就绪后，我们就可以创建决策树模型并进行训练了。

以下是创建和训练模型的代码：

```python
# 创建决策树分类器模型
model = DecisionTreeClassifier()
# 使用数据训练模型
model.fit(X, y)
# 评估模型在训练集上的准确率
score = model.score(X, y)
print(f"模型准确率: {score}")
```

## 决策树可视化 🎨

模型训练完成后，我们可能想知道这棵“树”具体长什么样。本节中，我们来看看如何将决策树的结构可视化。

以下是可视化决策树的步骤：

```python
import matplotlib.pyplot as plt
from sklearn import tree

# 设置中文字体和图形尺寸
plt.rcParams['font.family'] = ['SimHei']
plt.figure(figsize=(12, 16))

# 获取特征名称
feature_names = X.columns.tolist()

![](img/0e74722d391d4fec70481c614bf0f3a0_1.png)

![](img/0e74722d391d4fec70481c614bf0f3a0_3.png)

![](img/0e74722d391d4fec70481c614bf0f3a0_5.png)

# 绘制决策树
tree.plot_tree(model,
               filled=True,          # 填充颜色
               feature_names=feature_names)
plt.title("决策树结构")
plt.show()

![](img/0e74722d391d4fec70481c614bf0f3a0_7.png)

![](img/0e74722d391d4fec70481c614bf0f3a0_9.png)

# 保存决策树图片
plt.savefig('./决策树.png', dpi=100)
```

![](img/0e74722d391d4fec70481c614bf0f3a0_11.png)

![](img/0e74722d391d4fec70481c614bf0f3a0_13.png)

![](img/0e74722d391d4fec70481c614bf0f3a0_15.png)

在生成的决策树图中，每个节点都显示了分裂条件（例如“好友密度 <= 0.5”）以及当前节点的基尼不纯度或熵值。颜色深度通常表示节点的“纯度”，颜色越深，该节点中样本属于同一类别的比例越高。

## 理解分裂标准 📐

在可视化时，我们看到了节点上的“gini”或“entropy”值。这对应着创建决策树时的一个关键参数：`criterion`（分裂标准）。

以下是关于分裂标准的说明：
*   **基尼不纯度 (gini)**：`criterion='gini'`。衡量从数据集中随机选取两个样本，其类别标签不一致的概率。值越小，节点的纯度越高。
    *   **公式**：$Gini(p) = 1 - \sum_{i=1}^{k} p_i^2$，其中 $p_i$ 是第 $i$ 个类别在节点中的比例。
*   **熵 (entropy)**：`criterion='entropy'`。源自信息论，表示系统的混乱程度。熵值越小，节点的纯度越高。
    *   **公式**：$Entropy(p) = -\sum_{i=1}^{k} p_i \log_2(p_i)$

在创建模型时，可以通过参数指定：
```python
# 使用熵作为分裂标准
model_entropy = DecisionTreeClassifier(criterion='entropy')
model_entropy.fit(X, y)
```
通过对比不同标准下生成的树，可以加深对算法工作原理的理解。

---

![](img/0e74722d391d4fec70481c614bf0f3a0_17.png)

![](img/0e74722d391d4fec70481c614bf0f3a0_18.png)

本节课中我们一起学习了决策树分类器的完整应用流程。我们从加载和预处理文本数据开始，然后训练了一个决策树模型，并最终将模型的结构可视化出来。通过可视化图形，我们能够直观地理解决策树是如何根据特征（如好友密度、日志密度）一步步对样本进行分类的。这为后续深入理解决策树的工作原理（如信息增益计算）打下了坚实的基础。