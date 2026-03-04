# 机器学习：P109：3-逻辑回归OVR建模与概率预测 🧠

![](img/cbc7f812383caa57f361446599d188c9_0.png)

![](img/cbc7f812383caa57f361446599d188c9_2.png)

在本节课中，我们将学习如何使用逻辑回归的“一对多”策略进行多分类建模，并理解模型如何输出每个类别的预测概率。

***

## 加载数据 📊

首先，我们需要导入必要的库并加载数据集。

```python
import numpy as np
from sklearn.linear_model import LogisticRegression
from sklearn import datasets
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

![](img/cbc7f812383caa57f361446599d188c9_4.png)

# 加载鸢尾花数据集
X, y = datasets.load_iris(return_X_y=True)
```

***

![](img/cbc7f812383caa57f361446599d188c9_6.png)

## 拆分数据集 ✂️

![](img/cbc7f812383caa57f361446599d188c9_8.png)

![](img/cbc7f812383caa57f361446599d188c9_10.png)

为了评估模型性能，我们需要将数据分为训练集和测试集。`train_test_split` 函数可以帮我们完成这个任务。

以下是 `train_test_split` 函数中两个关键参数的作用：
*   `test_size=0.2`：指定测试集占总数据的比例为20%。
*   `random_state=1024`：设置随机种子，确保每次运行代码时数据划分的结果都相同，便于复现。

![](img/cbc7f812383caa57f361446599d188c9_12.png)

![](img/cbc7f812383caa57f361446599d188c9_14.png)

![](img/cbc7f812383caa57f361446599d188c9_16.png)

```python
# 拆分数据集，80%用于训练，20%用于测试
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=1024)

![](img/cbc7f812383caa57f361446599d188c9_18.png)

# 查看数据形状
print(f"训练集特征形状: {X_train.shape}")
print(f"测试集特征形状: {X_test.shape}")
print(f"训练集标签形状: {y_train.shape}")
print(f"测试集标签形状: {y_test.shape}")
```

![](img/cbc7f812383caa57f361446599d188c9_20.png)

![](img/cbc7f812383caa57f361446599d188c9_22.png)

***

## 使用OVR策略建模 🏗️

上一节我们准备好了数据，本节中我们来看看如何使用逻辑回归的“一对多”策略进行建模。

![](img/cbc7f812383caa57f361446599d188c9_24.png)

在创建逻辑回归模型时，通过设置 `multi_class='ovr'` 参数，我们明确指定使用“一对多”策略来处理多分类问题。模型默认使用L2正则化来防止过拟合。

```python
# 创建逻辑回归模型，使用‘一对多’策略
model = LogisticRegression(multi_class='ovr', penalty='l2')
# 使用训练数据拟合模型
model.fit(X_train, y_train)
```

![](img/cbc7f812383caa57f361446599d188c9_26.png)

模型训练完成后，我们可以用它来对测试集进行预测。

```python
# 使用模型进行预测
y_pred = model.predict(X_test)

# 查看前10个预测结果和真实标签
print("真实标签 (前10个):", y_test[:10])
print("预测标签 (前10个):", y_pred[:10])
```

***

![](img/cbc7f812383caa57f361446599d188c9_28.png)

## 评估模型准确率 📈

![](img/cbc7f812383caa57f361446599d188c9_30.png)

![](img/cbc7f812383caa57f361446599d188c9_32.png)

模型预测完成后，我们需要评估其性能。最直观的指标是准确率。

![](img/cbc7f812383caa57f361446599d188c9_34.png)

我们可以通过三种方式计算准确率：
1.  使用模型自带的 `score` 方法。
2.  使用 `sklearn.metrics` 中的 `accuracy_score` 函数。
3.  手动计算预测正确的样本比例。

![](img/cbc7f812383caa57f361446599d188c9_36.png)

```python
# 方法1：使用模型自带的score方法
accuracy_model = model.score(X_test, y_test)
print(f"模型score方法准确率: {accuracy_model}")

![](img/cbc7f812383caa57f361446599d188c9_38.png)

# 方法2：使用accuracy_score函数
accuracy_metric = accuracy_score(y_test, y_pred)
print(f"accuracy_score函数准确率: {accuracy_metric}")

# 方法3：手动计算
accuracy_manual = (y_pred == y_test).mean()
print(f"手动计算准确率: {accuracy_manual}")
```

![](img/cbc7f812383caa57f361446599d188c9_40.png)

这三种方法得到的结果是完全一致的，其核心原理都是比较预测值与真实值，然后计算相等的比例。

![](img/cbc7f812383caa57f361446599d188c9_42.png)

***

![](img/cbc7f812383caa57f361446599d188c9_44.png)

![](img/cbc7f812383caa57f361446599d188c9_46.png)

## 理解概率预测 🎲

![](img/cbc7f812383caa57f361446599d188c9_48.png)

![](img/cbc7f812383caa57f361446599d188c9_49.png)

逻辑回归不仅能预测类别，还能输出样本属于每个类别的概率。这对于理解模型的“信心”程度非常有用。

![](img/cbc7f812383caa57f361446599d188c9_51.png)

![](img/cbc7f812383caa57f361446599d188c9_53.png)

![](img/cbc7f812383caa57f361446599d188c9_55.png)

我们使用 `predict_proba` 方法来获取概率。对于一个三分类问题，每个样本会得到三个概率值，分别对应属于类别0、1、2的可能性，这三个概率之和为1。

![](img/cbc7f812383caa57f361446599d188c9_57.png)

```python
# 获取测试集样本属于每个类别的概率
y_proba = model.predict_proba(X_test)

# 设置numpy打印格式，避免科学计数法
np.set_printoptions(suppress=True)

![](img/cbc7f812383caa57f361446599d188c9_59.png)

# 查看前10个样本的概率
print("前10个样本的预测概率:\n", y_proba[:10])
print("\n前10个样本的预测类别:", y_pred[:10])
```

![](img/cbc7f812383caa57f361446599d188c9_61.png)

![](img/cbc7f812383caa57f361446599d188c9_63.png)

![](img/cbc7f812383caa57f361446599d188c9_65.png)

观察输出结果，模型最终预测的类别，就是概率值最大的那个类别。例如，对于第一个样本，其三个概率为 `[0.001, 0.332, 0.667]`，最大值 `0.667` 对应类别2，因此预测结果为2。

![](img/cbc7f812383caa57f361446599d188c9_67.png)

***

![](img/cbc7f812383caa57f361446599d188c9_69.png)

本节课中我们一起学习了逻辑回归“一对多”策略的完整建模流程。我们从加载和拆分数据开始，使用 `multi_class='ovr'` 参数创建并训练了模型。接着，我们通过多种方式评估了模型的准确率。最后，我们深入了解了 `predict_proba` 方法，它揭示了模型做出决策背后的概率依据，这是逻辑回归模型一个非常有价值的特性。