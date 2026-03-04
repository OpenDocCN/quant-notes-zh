# 机器学习：P200：4-GBDT梯度提升分类树使用 🚀

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_1.png)

在本节课中，我们将学习如何使用梯度提升决策树（GBDT）进行分类任务。我们将从导入必要的库开始，逐步完成数据准备、模型训练、预测和评估的完整流程。

---

## 概述 📋

GBDT是一种强大的集成学习算法，它通过组合多个弱学习器（通常是决策树）来构建一个强学习器。在分类任务中，GBDT通过迭代地拟合残差来提升模型性能。本节课将使用Python的`scikit-learn`库来实现一个GBDT分类器。

---

## 数据准备与导入 📊

首先，我们需要准备数据并导入必要的Python库。我们将使用`scikit-learn`中的`GradientBoostingClassifier`。

```python
# 导入必要的库
from sklearn.ensemble import GradientBoostingClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
import pandas as pd
```

上一节我们介绍了课程目标，本节中我们来看看如何准备数据。通常，数据来源于文件或数据库。以下是加载数据的步骤：

1.  使用`pandas`库读取数据文件（例如CSV格式）。
2.  检查数据的基本信息，如形状和前几行。
3.  将数据划分为特征（X）和目标变量（y）。

假设我们有一个名为`data.csv`的数据文件，加载过程如下：
```python
# 加载数据
data = pd.read_csv('data.csv')
# 假设最后一列是目标变量
X = data.iloc[:, :-1]  # 特征
y = data.iloc[:, -1]   # 目标
```

---

## 划分训练集与测试集 🔀

为了评估模型的泛化能力，我们需要将数据集划分为训练集和测试集。

以下是使用`train_test_split`函数进行划分的方法：
```python
# 划分数据集，80%用于训练，20%用于测试
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```
参数`test_size=0.2`表示测试集占20%，`random_state=42`用于确保每次运行代码时划分结果一致。

---

## 创建并训练GBDT模型 🌲

现在，我们可以创建GBDT分类器并开始训练。

核心概念是初始化`GradientBoostingClassifier`类。以下是一些关键参数：
*   `n_estimators`: 弱学习器（树）的数量。
*   `learning_rate`: 每棵树的贡献权重。
*   `max_depth`: 每棵决策树的最大深度。

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_3.png)

```python
# 创建GBDT分类器实例
gbdt_model = GradientBoostingClassifier(n_estimators=100, learning_rate=0.1, max_depth=3, random_state=42)

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_5.png)

# 在训练集上训练模型
gbdt_model.fit(X_train, y_train)
```
模型会迭代地构建100棵最大深度为3的决策树，学习率为0.1。

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_7.png)

---

## 进行预测与评估 📈

模型训练完成后，我们可以用它对测试集进行预测，并评估其性能。

评估分类模型常用的指标是准确率（Accuracy），即正确预测的样本占总样本的比例。公式为：
**Accuracy = (正确预测数) / (总预测数)**

以下是预测和计算准确率的代码：
```python
# 对测试集进行预测
y_pred = gbdt_model.predict(X_test)

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_9.png)

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_10.png)

# 计算准确率
accuracy = accuracy_score(y_test, y_pred)
print(f"模型在测试集上的准确率为：{accuracy:.4f}")
```

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_12.png)

---

## 模型参数调优 ⚙️

为了获得更好的性能，我们通常需要调整模型的超参数。上一节我们完成了模型的初步训练和评估，本节中我们来看看如何进行参数调优。

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_14.png)

常见的调优方法包括网格搜索（Grid Search）和随机搜索（Random Search）。以下是使用网格搜索的示例步骤：

1.  定义想要尝试的参数组合。
2.  使用`GridSearchCV`进行交叉验证搜索。
3.  找到最佳参数组合并训练最终模型。

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_16.png)

```python
from sklearn.model_selection import GridSearchCV

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_18.png)

# 定义参数网格
param_grid = {
    'n_estimators': [50, 100, 200],
    'learning_rate': [0.01, 0.1, 0.2],
    'max_depth': [3, 4, 5]
}

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_20.png)

# 创建网格搜索对象
grid_search = GridSearchCV(estimator=gbdt_model, param_grid=param_grid, cv=5, scoring='accuracy')
grid_search.fit(X_train, y_train)

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_22.png)

# 输出最佳参数
print("最佳参数组合：", grid_search.best_params_)
print("最佳交叉验证分数：", grid_search.best_score_)
```

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_24.png)

---

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_26.png)

## 总结 🎯

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_28.png)

本节课中我们一起学习了使用GBDT进行二元分类的完整流程。

我们首先完成了数据准备和库的导入，然后将数据划分为训练集和测试集。接着，我们创建并训练了一个基础的GBDT分类器，并用它对测试集进行预测和准确率评估。最后，我们探讨了通过网格搜索进行模型参数调优的方法，以追求更佳的模型性能。

![](img/a6dcb1d93d7ec76615a1faa572f97f0f_30.png)

通过掌握这些步骤，你已经能够应用GBDT模型来解决基本的分类问题了。