# 机器学习算法对比：P186：1-决策树与不同算法综合对比 🧠

![](img/755b508f22e1bec303130219937f8d58_1.png)

![](img/755b508f22e1bec303130219937f8d58_3.png)

在本节课中，我们将学习决策树、逻辑斯蒂回归和支持向量机这三种分类算法的核心差异。我们将通过代码实践，对比它们对数据归一化的敏感性、模型性能以及是否能够提供特征重要性分析。

![](img/755b508f22e1bec303130219937f8d58_5.png)

---

![](img/755b508f22e1bec303130219937f8d58_7.png)

## 数据准备与预处理 📊

首先，我们需要导入必要的库并加载数据集。我们将使用葡萄酒数据集，它包含多个特征，用于预测葡萄酒的等级。

![](img/755b508f22e1bec303130219937f8d58_9.png)

```python
import numpy as np
from sklearn import tree
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC
from sklearn import datasets
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
import warnings
warnings.filterwarnings('ignore')

# 加载葡萄酒数据集
wine = datasets.load_wine()
X = wine.data
y = wine.target
```

![](img/755b508f22e1bec303130219937f8d58_11.png)

### 进行归一化处理

![](img/755b508f22e1bec303130219937f8d58_13.png)

![](img/755b508f22e1bec303130219937f8d58_15.png)

数据归一化对于某些算法至关重要。我们将使用 `StandardScaler` 进行Z-score归一化。

![](img/755b508f22e1bec303130219937f8d58_17.png)

```python
# 数据归一化
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)
```

![](img/755b508f22e1bec303130219937f8d58_19.png)

归一化后的数据将消除不同特征间量纲的影响，使模型训练更稳定。

---

## 逻辑斯蒂回归模型应用 🔢

逻辑斯蒂回归是一种线性分类模型。我们将通过多次训练来评估其平均性能。

以下是逻辑斯蒂回归模型的训练与评估步骤：

```python
# 逻辑斯蒂回归模型应用
scores_lr = 0
for i in range(100):
    X_train, X_test, y_train, y_test = train_test_split(X_scaled, y, test_size=0.2)
    model_lr = LogisticRegression()
    model_lr.fit(X_train, y_train)
    score = model_lr.score(X_test, y_test)
    scores_lr += score
avg_score_lr = scores_lr / 100
print(f"逻辑回归多次运算平均得分: {avg_score_lr}")
```

逻辑斯蒂回归在归一化数据上表现稳定，平均得分约为98%。

---

## 支持向量机模型应用 ⚙️

![](img/755b508f22e1bec303130219937f8d58_21.png)

支持向量机通过寻找最优超平面来分隔数据。接下来，我们看看它在相同数据集上的表现。

以下是支持向量机模型的训练与评估步骤：

![](img/755b508f22e1bec303130219937f8d58_23.png)

![](img/755b508f22e1bec303130219937f8d58_25.png)

```python
# 支持向量机模型应用
scores_svc = 0
for i in range(100):
    X_train, X_test, y_train, y_test = train_test_split(X_scaled, y, test_size=0.2)
    model_svc = SVC()
    model_svc.fit(X_train, y_train)
    score = model_svc.score(X_test, y_test)
    scores_svc += score
avg_score_svc = scores_svc / 100
print(f"支持向量机多次运算平均得分: {avg_score_svc}")
```

支持向量机在归一化数据上的平均得分约为98.36%，略高于逻辑斯蒂回归。

---

![](img/755b508f22e1bec303130219937f8d58_27.png)

## 决策树模型应用 🌳

决策树通过一系列规则对数据进行分类。现在，我们测试决策树模型的性能。

以下是决策树模型的训练与评估步骤：

```python
# 决策树模型应用
scores_dt = 0
for i in range(100):
    X_train, X_test, y_train, y_test = train_test_split(X_scaled, y, test_size=0.2)
    model_dt = tree.DecisionTreeClassifier()
    model_dt.fit(X_train, y_train)
    score = model_dt.score(X_test, y_test)
    scores_dt += score
avg_score_dt = scores_dt / 100
print(f"决策树多次运算平均得分: {avg_score_dt}")
```

决策树在相同数据上的平均得分约为90.6%，低于前两种算法。

---

![](img/755b508f22e1bec303130219937f8d58_29.png)

## 算法差异对比总结 📝

上一节我们分别应用了三种算法，本节我们来总结它们的关键差异。

以下是不同算法的主要对比点：

1.  **对数据归一化的敏感性**
    *   决策树对数据是否归一化**不敏感**。
    *   逻辑斯蒂回归若不进行归一化，准确率会**降低**，运行时间会**增加**。
    *   支持向量机若不进行归一化，准确率会**大幅降低**。

2.  **特征重要性分析**
    *   决策树训练后可直接通过 `model.feature_importances_` 获取特征重要性。
    *   逻辑斯蒂回归的系数（`model.coef_`）主要用于定义分类边界，**不能直接表示**特征重要性。
    *   支持向量机（特别是使用RBF等非线性核时）**没有**提供特征重要性属性。

3.  **核心原理差异**
    *   逻辑斯蒂回归和支持向量机的优化过程依赖于**梯度下降**，受特征量纲影响大。
    *   决策树基于信息增益或基尼不纯度进行划分，与梯度下降无关，因此不受量纲影响。

4.  **在回归模型中的补充说明**
    在线性回归、岭回归等回归模型中，系数的绝对值大小可以在一定程度上表示特征的重要性。

---

![](img/755b508f22e1bec303130219937f8d58_31.png)

![](img/755b508f22e1bec303130219937f8d58_33.png)

## 本节课总结 🎯

![](img/755b508f22e1bec303130219937f8d58_35.png)

本节课中我们一起学习了决策树、逻辑斯蒂回归和支持向量机三种分类算法的综合对比。

我们通过代码实践发现：
*   决策树能直接提供特征重要性，且对数据尺度不敏感，但在此数据集上准确率相对较低。
*   逻辑斯蒂回归和支持向量机对数据归一化要求高，归一化后性能优异，但它们无法像决策树那样直接输出特征重要性。
*   选择算法时，需根据数据特点、是否需要特征解释性以及对计算效率的要求进行权衡。

![](img/755b508f22e1bec303130219937f8d58_37.png)

理解这些差异有助于我们在实际项目中为不同任务选择合适的机器学习模型。