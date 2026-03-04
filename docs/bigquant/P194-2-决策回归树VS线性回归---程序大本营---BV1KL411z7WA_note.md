# 机器学习：P194：2-决策回归树VS线性回归 🧮🌲

![](img/076b2ad47ffdb446b8f619cdfe089db4_1.png)

在本节课中，我们将学习如何使用决策树回归模型，并将其与线性回归模型进行对比。我们将通过一个糖尿病数据集的实际案例，来观察两种模型在回归任务上的表现差异，并理解过拟合现象。

![](img/076b2ad47ffdb446b8f619cdfe089db4_3.png)

## 概述

上一节我们介绍了决策树回归的原理和分裂标准（MSE），并手动计算了最佳分裂条件。本节中，我们将在代码中实际应用决策树回归，并与线性回归模型进行性能对比，以直观地理解两种算法的特点。

## 代码实现与对比

以下是实现模型对比的步骤，我们将使用Scikit-learn库加载糖尿病数据集，分别训练线性回归模型和决策树回归模型，并评估它们的表现。

### 1. 导入必要的库

首先，我们需要导入所有必要的Python库。

```python
from sklearn import tree
from sklearn.linear_model import LinearRegression
from sklearn import datasets
from sklearn.model_selection import train_test_split
import numpy as np
import matplotlib.pyplot as plt
```

### 2. 加载并理解数据

我们使用Scikit-learn内置的糖尿病数据集。这是一个经典的回归问题数据集。

```python
# 加载糖尿病数据集
X, y = datasets.load_diabetes(return_X_y=True)
```
该数据集包含多个生理指标特征（如年龄、性别、BMI、血压、血液指标等），目标值`y`是表示糖尿病进展的定量指标。数值越大，表示病情可能越严重。由于目标值是连续变量，因此这是一个回归问题。

### 3. 划分训练集与测试集

我们将数据随机划分为训练集和测试集，以评估模型的泛化能力。

```python
# 划分训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=911)
```
参数`random_state`用于固定随机种子，确保每次运行代码时数据划分一致，使结果可复现。

### 4. 训练并评估线性回归模型

接下来，我们使用线性回归模型进行建模，并查看其在训练集和测试集上的得分（R²分数）。

```python
# 创建并训练线性回归模型
linear_model = LinearRegression()
linear_model.fit(X_train, y_train)

# 评估模型
train_score_linear = linear_model.score(X_train, y_train)
test_score_linear = linear_model.score(X_test, y_test)

print(f"线性回归 - 训练数据得分: {train_score_linear:.3f}")
print(f"线性回归 - 测试数据得分: {test_score_linear:.3f}")
```
理论上，模型在训练集上的得分应高于或等于在测试集上的得分。由于数据划分的随机性，偶尔可能出现测试集得分略高的情况，但在数据量足够大时，训练集得分更高是普遍规律。

### 5. 训练并评估决策树回归模型

现在，我们使用决策树回归模型。为了观察模型复杂度（这里以树的最大深度`max_depth`表示）对性能的影响，我们将训练一系列不同深度的树。

以下是核心代码步骤：
```python
# 设置图形显示中文字体
plt.rcParams['font.family'] = ['KaiTi']

# 尝试不同的树深度
max_depths = range(1, 16)
test_scores = []
train_scores = []

for d in max_depths:
    # 创建决策树模型
    tree_model = tree.DecisionTreeRegressor(max_depth=d)
    tree_model.fit(X_train, y_train)
    
    # 记录模型在测试集和训练集上的得分
    test_scores.append(tree_model.score(X_test, y_test))
    train_scores.append(tree_model.score(X_train, y_train))

# 绘制得分随树深度变化的曲线
plt.figure(figsize=(12, 6))
plt.plot(max_depths, test_scores, 'ro-', label='测试数据得分')
plt.plot(max_depths, train_scores, 'g*-', label='训练数据得分')
plt.xlabel('决策树最大深度', fontsize=18)
plt.ylabel('模型得分 (R²)', fontsize=18)
plt.title('决策树回归：模型复杂度与性能关系', fontsize=18)
plt.legend(fontsize=18)
plt.grid(True)
plt.show()

# 输出决策树在测试集上的最高得分
print(f"决策树回归 - 测试集最高得分: {max(test_scores):.3f}")
```

## 结果分析与核心概念

运行上述代码后，我们将得到类似下图的曲线：

![](img/076b2ad47ffdb446b8f619cdfe089db4_5.png)

通过分析图表和输出结果，我们可以得出以下结论：

1.  **模型表现对比**：对于本案例的糖尿病数据集，线性回归模型（得分约0.5）的表现优于决策树回归模型（最高得分约0.25）。这表明该数据的内在规律可能更接近线性关系，适合用线性方程进行拟合。
2.  **过拟合现象**：决策树的曲线清晰地展示了**过拟合**。
    *   **训练数据得分（绿色线）**：随着树深度的增加，模型对训练数据的拟合程度近乎完美，得分接近1.0。这是因为更深的树能创建更多叶节点，几乎可以“记住”每一个训练样本。
    *   **测试数据得分（红色线）**：随着树深度增加，模型在未见过的测试数据上的表现先升后降，最终甚至可能变为负值（R²分数为负表示模型预测比简单使用均值还要差）。这说明复杂的模型丧失了**泛化能力**。
3.  **核心概念：过拟合与泛化**：
    *   **过拟合**：模型在训练数据上表现过于优秀，以至于学习了数据中的噪声和细节，导致在新数据上表现不佳。就像学生死记硬背了所有习题答案，但遇到新题型就不会解答。
    *   **泛化能力**：模型对未知新数据做出准确预测的能力。一个稳健的模型，其训练集和测试集的表现应尽可能接近。
    *   **模型选择**：我们应选择在测试集上表现稳定且良好的模型，而非在训练集上得分最高的模型。这类似于衡量一个学生，应看重其高考（测试集）成绩，而非仅看模拟考（训练集）成绩。

## 总结

本节课中我们一起学习了如何将决策树回归应用于实际问题，并与线性回归模型进行了对比。我们通过可视化发现，决策树模型容易随着深度增加而产生过拟合，而线性回归在本案例中表现出了更好的稳定性和泛化能力。

![](img/076b2ad47ffdb446b8f619cdfe089db4_5.png)

需要强调的是，这并不意味着决策树回归算法不如线性回归。决策树回归在捕捉非线性关系、无需特征缩放等方面有其优势。此外，我们目前使用的是基础决策树，后续课程中我们将学习如随机森林、梯度提升树等更强大的集成树模型，它们能有效缓解过拟合问题，提升模型性能。选择合适的模型始终依赖于具体的数据和问题场景。