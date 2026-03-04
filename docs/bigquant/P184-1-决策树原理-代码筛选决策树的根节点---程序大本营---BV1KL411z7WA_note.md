# 决策树：P184-1：决策树原理与根节点筛选代码实现 🎯

![](img/9ef235191df0367e8f0707659ab7f92d_1.png)

![](img/9ef235191df0367e8f0707659ab7f92d_3.png)

在本节课中，我们将学习决策树的基本原理，并通过编写代码来手动筛选决策树的根节点。我们将从理解决策树的结构开始，逐步深入到如何使用信息熵和基尼系数作为划分标准，最终通过代码复现决策树选择最佳分裂条件的过程。

![](img/9ef235191df0367e8f0707659ab7f92d_5.png)

## 决策树结构概述 🌳

决策树是一种树形结构，用于分类和回归任务。它模拟人类做决策的过程，通过一系列的判断规则对数据进行划分。

上一节我们介绍了决策树的基本概念，本节中我们来看看它的具体结构是如何构建的。

一棵决策树包含以下几种节点：
*   **根节点**：树的起始点，包含所有训练数据，代表第一个用于划分数据的特征。
*   **子节点/内部节点**：在根节点之后，代表基于某个特征值进一步划分数据的决策点。
*   **叶节点**：树的终端节点，代表最终的分类或回归结果。

在算法中，决策树的生长方向与真实树木相反，是**从上向下（Top-Down）** 构建的。我们从根节点开始，根据某个特征将数据分成子集，每个子集形成一个子节点，这个过程递归进行，直到满足停止条件（如节点纯度足够高或达到最大深度），最终形成叶节点。

在我们的示例中，决策树使用“好友密度”作为根节点进行第一次划分。

## 代码实现：筛选最佳根节点 💻

理解了结构后，我们面临一个核心问题：如何从多个特征（如“日志密度”、“好友密度”、“是否使用真实头像”）中选择最优的那个作为根节点？接下来，我们将用代码来揭示这个选择过程。

首先，我们需要准备数据并训练一个基础的决策树模型，以明确我们的目标。

```python
import pandas as pd
from sklearn.tree import DecisionTreeClassifier, export_graphviz
import graphviz

# 1. 创建数据
data = {
    ‘日志密度‘: [‘高‘, ‘高‘, ‘中‘, ‘低‘, ‘低‘, ‘低‘, ‘中‘, ‘低‘, ‘中‘, ‘中‘, ‘中‘, ‘高‘, ‘中‘],
    ‘好友密度‘: [‘高‘, ‘低‘, ‘高‘, ‘高‘, ‘低‘, ‘高‘, ‘低‘, ‘低‘, ‘高‘, ‘高‘, ‘低‘, ‘高‘, ‘中‘],
    ‘真实头像‘: [‘是‘, ‘是‘, ‘是‘, ‘否‘, ‘是‘, ‘是‘, ‘否‘, ‘否‘, ‘是‘, ‘是‘, ‘是‘, ‘是‘, ‘否‘],
    ‘是否付费‘: [‘是‘, ‘否‘, ‘是‘, ‘否‘, ‘是‘, ‘否‘, ‘否‘, ‘否‘, ‘是‘, ‘是‘, ‘是‘, ‘是‘, ‘否‘]
}
df = pd.DataFrame(data)

# 2. 将字符串数据映射为数值，以便计算
mapping_dict = {‘高‘: 2, ‘中‘: 1, ‘低‘: 0, ‘是‘: 1, ‘否‘: 0}
df = df.applymap(lambda x: mapping_dict.get(x, x))

# 3. 准备特征X和目标y
X = df.iloc[:, :-1]  # 取前三列作为特征
y = df.iloc[:, -1]   # 取最后一列作为目标

# 4. 使用sklearn的决策树进行训练和可视化
model = DecisionTreeClassifier(criterion=‘entropy‘) # 使用信息熵作为划分标准
model.fit(X, y)

# 可视化决策树
dot_data = export_graphviz(model, out_file=None,
                           feature_names=X.columns.tolist(),
                           class_names=[‘否‘, ‘是‘],
                           filled=True, rounded=True)
graph = graphviz.Source(dot_data)
graph
```
运行上述代码，可以得到一棵决策树。可视化结果显示，根节点确实是“好友密度”。我们的目标就是通过手动计算，验证为何“好友密度”被选为根节点。

## 手动计算最佳分裂特征 🔍

现在，我们抛开`sklearn`，自己编写代码来为每个特征寻找最佳分裂点，并计算分裂后的“不纯度”（使用信息熵或基尼系数）。不纯度越低，说明分裂效果越好。

核心思路是遍历每个特征，遍历该特征所有可能的分裂点，计算以该点分裂后子集的加权不纯度，选择不纯度最低的特征和分裂点作为根节点。

以下是手动计算的步骤分解：

1.  **遍历每个特征**：对于数据集中的每一个特征（列），我们都将其视为候选的根节点。
2.  **寻找分裂点**：对于一个特征，其取值可能为[0, 1, 2]。分裂点通常取相邻值的**中点**，例如在0和1之间取0.5，在1和2之间取1.5。
3.  **计算分裂后的不纯度**：对于每个分裂点（如0.5），将数据划分为两部分（特征值≤0.5和>0.5）。分别计算左右两部分数据关于目标变量（是否付费）的**信息熵**或**基尼系数**。
4.  **计算加权不纯度**：将左右两部分的不纯度，按照各自数据量占总数据的比例进行加权求和，得到以该点分裂的总体不纯度。
    *   信息熵公式：**Entropy = -Σ(p_i * log₂(p_i))**
    *   基尼系数公式：**Gini = 1 - Σ(p_i²)**
    *   其中 **p_i** 是第i类样本在节点中的比例。
5.  **记录最佳选择**：比较所有特征在所有分裂点上的总体不纯度，记录下不纯度最低的特征和对应的分裂点。

以下是实现上述逻辑的完整代码：

```python
import numpy as np

# 定义计算信息熵的函数
def calculate_entropy(p):
    return -np.sum(p * np.log2(p + 1e-10)) # 加一个小数避免log(0)

# 定义计算基尼系数的函数
def calculate_gini(p):
    return 1 - np.sum(p**2)

best_feature = None
best_split = None
lowest_impurity = float(‘inf‘) # 初始化为无穷大
criterion = ‘entropy‘ # 可以选择 ‘entropy‘ 或 ‘gini‘

# 遍历每个特征
for col in X.columns:
    # 获取该特征唯一值并排序
    unique_vals = np.sort(X[col].unique())
    
    # 遍历所有可能的分裂点（相邻值的中间点）
    for i in range(len(unique_vals) - 1):
        split_val = (unique_vals[i] + unique_vals[i + 1]) / 2.0
        
        # 根据分裂点将数据分为左右两部分
        left_mask = X[col] <= split_val
        right_mask = X[col] > split_val
        
        left_y = y[left_mask]
        right_y = y[right_mask]
        
        # 计算左右两部分的权重（样本占比）
        w_left = len(left_y) / len(y)
        w_right = len(right_y) / len(y)
        
        # 计算左右两部分关于目标变量的概率分布
        # value_counts(normalize=True) 直接得到概率
        left_probs = left_y.value_counts(normalize=True).values
        right_probs = right_y.value_counts(normalize=True).values
        
        # 根据选定的标准计算不纯度
        if criterion == ‘entropy‘:
            impurity_left = calculate_entropy(left_probs)
            impurity_right = calculate_entropy(right_probs)
        else: # ‘gini‘
            impurity_left = calculate_gini(left_probs)
            impurity_right = calculate_gini(right_probs)
        
        # 计算加权后的总不纯度
        total_impurity = w_left * impurity_left + w_right * impurity_right
        
        # 更新最佳分裂条件
        if total_impurity < lowest_impurity:
            lowest_impurity = total_impurity
            best_feature = col
            best_split = split_val

![](img/9ef235191df0367e8f0707659ab7f92d_7.png)

print(f“使用‘{criterion}‘作为标准：“)
print(f“最佳分裂特征：{best_feature}“)
print(f“最佳分裂点：{best_split}“)
print(f“分裂后的最小不纯度：{lowest_impurity:.4f}“)
```
运行这段代码，无论选择`criterion=‘entropy‘`（信息熵）还是`criterion=‘gini‘`（基尼系数），输出结果都会显示：
*   **最佳分裂特征：好友密度**
*   **最佳分裂点：0.5**

![](img/9ef235191df0367e8f0707659ab7f92d_9.png)

![](img/9ef235191df0367e8f0707659ab7f92d_11.png)

这验证了`sklearn`的决策树选择“好友密度”和分裂点0.5作为根节点的原因——它能在第一次划分时最大程度地降低系统的不纯度。

## 总结与核心要点 📝

本节课中我们一起学习了决策树的构建原理，并完成了一个重要的实践：**手动编写代码筛选决策树的根节点**。

我们掌握了以下核心内容：
1.  **决策树结构**：理解了根节点、内部节点、叶节点以及树自上而下的构建过程。
2.  **分裂标准**：学习了使用**信息熵（Entropy）** 和**基尼系数（Gini）** 来衡量节点纯度（不纯度）。它们的核心公式是：
    *   **信息熵：Entropy = -Σ(p_i * log₂(p_i))**
    *   **基尼系数：Gini = 1 - Σ(p_i²)**
3.  **最佳特征选择**：决策树选择根节点（以及后续节点）的本质是：**遍历所有特征和所有可能的分裂点，计算分裂后的加权不纯度，选择能使不纯度降低最多的特征和分裂点**。
4.  **代码实现**：通过手动实现的代码，我们揭示了`DecisionTreeClassifier`等库函数内部的核心计算逻辑，即通过多层循环比较找到最优划分。

![](img/9ef235191df0367e8f0707659ab7f92d_13.png)

这段手动计算的代码是理解决策树乃至许多机器学习算法“黑箱”的关键。虽然实际库函数的实现为了效率会进行大量优化（如使用更高效的数据结构、并行计算等），但其根本思想与此一致。理解了这个过程，你就能更深刻地掌握决策树的工作原理。