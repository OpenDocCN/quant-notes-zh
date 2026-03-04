# 逻辑回归：P112：代码实现逻辑回归Softmax概率计算 📊

在本节课中，我们将学习如何使用代码实现逻辑回归中的Softmax概率计算。我们将从加载数据开始，训练一个多分类逻辑回归模型，并手动计算Softmax概率，以深入理解其背后的数学原理。

## 数据准备与模型训练

首先，我们需要导入必要的库并加载数据。这里我们使用鸢尾花数据集，它是一个经典的三分类数据集。

![](img/e2570b5dfb6dc99b78b9fb2d94eeec7a_1.png)

![](img/e2570b5dfb6dc99b78b9fb2d94eeec7a_3.png)

```python
import numpy as np
from sklearn import datasets
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split

![](img/e2570b5dfb6dc99b78b9fb2d94eeec7a_5.png)

![](img/e2570b5dfb6dc99b78b9fb2d94eeec7a_7.png)

# 加载鸢尾花数据集
data = datasets.load_iris(return_X_y=True)
X, y = data

![](img/e2570b5dfb6dc99b78b9fb2d94eeec7a_9.png)

![](img/e2570b5dfb6dc99b78b9fb2d94eeec7a_11.png)

# 划分训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=1024)
```

接下来，我们声明并训练一个逻辑回归模型。为了使用Softmax进行多分类，我们需要将 `multi_class` 参数设置为 `‘multinomial’`。

```python
# 创建逻辑回归模型，指定使用Softmax（multinomial）
model = LogisticRegression(multi_class='multinomial')

# 训练模型
model.fit(X_train, y_train)

# 评估模型在测试集上的准确率
accuracy = model.score(X_test, y_test)
print('算法预测的准确率：', accuracy)

# 预测测试数据的概率
probabilities = model.predict_proba(X_test[:5])
print('算法预测测试数据的概率是：\n', probabilities)
```

执行上述代码后，我们可以看到模型预测的准确率以及前五个测试样本属于各个类别的概率分布。

## 手动计算Softmax概率

上一节我们介绍了如何使用`sklearn`直接得到预测概率。本节中，我们来看看如何手动计算这些概率，以理解Softmax函数的运作机制。

首先，我们需要从训练好的模型中获取方程的系数（斜率）和截距。

```python
# 获取模型的系数（w）和截距（b）
w_ = model.coef_  # 形状为 (3, 4)，对应三条方程
b_ = model.intercept_  # 形状为 (3,)，对应三个截距
```

然后，我们定义Softmax函数。其核心公式如下：

**公式：** `softmax(z_i) = exp(z_i) / sum(exp(z))`

在代码中，我们可以用一行简洁地实现它：

![](img/e2570b5dfb6dc99b78b9fb2d94eeec7a_13.png)

```python
def softmax(z):
    return np.exp(z) / np.sum(np.exp(z), axis=1, keepdims=True)
```

![](img/e2570b5dfb6dc99b78b9fb2d94eeec7a_15.png)

**注意：** 参数 `axis=1` 表示对每一行（即每个样本）的分数分别进行求和，`keepdims=True` 是为了保持二维数组形状，便于后续的广播计算。

现在，我们手动计算测试数据经过模型方程后的分数 `z`，然后应用Softmax函数。

```python
# 计算线性方程的输出 z = X * w^T + b
z = X_test.dot(w_.T) + b_

# 应用Softmax函数得到概率
manual_probabilities = softmax(z)[:5]
print('手动计算的概率是：\n', manual_probabilities)
```

将手动计算的结果与之前模型直接输出的概率进行对比，你会发现它们是完全一致的。这验证了我们手动计算过程的正确性。

## 核心计算步骤详解

以下是手动计算过程中的关键步骤解析：

1.  **线性变换**：对于每个样本 `x` 和每个类别 `i`，计算其分数 `z_i = w_i · x + b_i`。这步通过矩阵运算 `X_test.dot(w_.T) + b_` 一次性完成。
2.  **指数运算**：对所有的分数 `z_i` 应用指数函数 `exp`，将其转换为正数。
3.  **归一化**：将每个样本的指数化分数，除以该样本所有类别指数化分数之和。这确保了每个样本的各类别概率之和为1。

通过这个过程，Softmax函数将原始的线性方程输出转换为了一个清晰、可解释的概率分布。

## 总结

![](img/e2570b5dfb6dc99b78b9fb2d94eeec7a_17.png)

本节课中我们一起学习了逻辑回归在多分类问题中Softmax概率计算的代码实现。我们从加载数据、训练模型开始，然后深入到手动计算Softmax概率的每一步，包括获取模型参数、进行线性计算以及应用Softmax归一化公式。通过对比模型输出与手动计算结果，我们不仅验证了计算过程的正确性，也加深了对Softmax函数如何将模型输出转化为概率分布的理解。掌握这一过程有助于我们更透彻地理解多分类逻辑回归模型的内部机制。