# 机器学习入门：P60：3-模型评估

![](img/3960f332b271b484790eabfb9cc0c60b_0.png)

![](img/3960f332b271b484790eabfb9cc0c60b_2.png)

在本节课中，我们将学习如何评估一个机器学习模型的好坏。我们将介绍两种常用的评估指标：决定系数（R²）和均方误差（MSE），并通过代码演示如何计算它们。

---

![](img/3960f332b271b484790eabfb9cc0c60b_4.png)

## 为什么需要模型评估？🤔

![](img/3960f332b271b484790eabfb9cc0c60b_6.png)

![](img/3960f332b271b484790eabfb9cc0c60b_8.png)

上一节我们介绍了如何使用线性回归模型进行预测。模型预测的结果，例如预测房价为20.54万，而实际房价为24.1万，两者之间存在差距。仅凭肉眼观察这些数值的接近程度来判断模型好坏是不规范的。

![](img/3960f332b271b484790eabfb9cc0c60b_10.png)

就像比较不同国家的货币价值需要汇率一样，评估模型也需要统一的、量化的指标。这些指标能告诉我们模型预测的准确度，判断模型是否合适、是否优秀。

![](img/3960f332b271b484790eabfb9cc0c60b_12.png)

## 评估指标一：决定系数（R² Score）📈

![](img/3960f332b271b484790eabfb9cc0c60b_14.png)

Scikit-learn的模型对象通常提供一个`.score()`方法用于快速评估。对于线性回归模型，该方法返回的是**决定系数**（Coefficient of Determination），即 **R²**。

![](img/3960f332b271b484790eabfb9cc0c60b_16.png)

![](img/3960f332b271b484790eabfb9cc0c60b_18.png)

以下是使用`.score()`方法评估模型的代码：
```python
# 使用测试集评估模型
score = model.score(X_test, y_test)
print(score)  # 输出例如 0.6564
```

![](img/3960f332b271b484790eabfb9cc0c60b_20.png)

**R²** 的取值范围和含义：
*   **最大值是1**。值越接近1，说明模型对数据的解释能力越强，模型越优秀。
*   **可以小于0**。如果模型预测效果极差，甚至不如直接用目标值的平均值来预测，那么R²可能为负数。

### R² 的计算公式

![](img/3960f332b271b484790eabfb9cc0c60b_22.png)

R² 的计算公式如下：
$$
R^2 = 1 - \frac{u}{v}
$$
其中：
*   **u** 是残差平方和（Residual Sum of Squares, RSS），计算公式为：
    `u = sum((y_true - y_pred) ** 2)`
*   **v** 是总平方和（Total Sum of Squares, TSS），计算公式为：
    `v = sum((y_true - y_mean) ** 2)`

![](img/3960f332b271b484790eabfb9cc0c60b_24.png)

我们可以手动计算来验证`.score()`方法的结果：

![](img/3960f332b271b484790eabfb9cc0c60b_26.png)

```python
# 计算预测值
y_pred = model.predict(X_test)
# 定义真实值
y_true = y_test

![](img/3960f332b271b484790eabfb9cc0c60b_28.png)

![](img/3960f332b271b484790eabfb9cc0c60b_30.png)

# 手动计算 R²
u = ((y_true - y_pred) ** 2).sum()
v = ((y_true - y_true.mean()) ** 2).sum()
r2_manual = 1 - u / v
print(r2_manual)  # 输出结果应与 model.score(X_test, y_test) 一致
```

![](img/3960f332b271b484790eabfb9cc0c60b_32.png)

![](img/3960f332b271b484790eabfb9cc0c60b_34.png)

![](img/3960f332b271b484790eabfb9cc0c60b_36.png)

### 训练集与测试集的得分差异

![](img/3960f332b271b484790eabfb9cc0c60b_38.png)

一个有趣的现象是，用训练数据评估模型通常会得到更高的分数。

![](img/3960f332b271b484790eabfb9cc0c60b_40.png)

![](img/3960f332b271b484790eabfb9cc0c60b_42.png)

```python
# 使用训练集评估模型
train_score = model.score(X_train, y_train)
print(train_score)  # 这个分数通常高于测试集得分
```

![](img/3960f332b271b484790eabfb9cc0c60b_44.png)

![](img/3960f332b271b484790eabfb9cc0c60b_46.png)

**原因解释**：这好比学生参加考试。
*   **训练集** 相当于平时做过的练习题。模型在这些题目上反复练习，因此得分高。
*   **测试集** 相当于从未见过的新考题（高考题）。模型在新题目上的表现更能反映其真实能力和泛化水平。

一个优秀的模型，其训练集和测试集上的表现应该尽可能接近，这说明模型找到了普遍规律，而不仅仅是“死记硬背”训练数据。

![](img/3960f332b271b484790eabfb9cc0c60b_48.png)

![](img/3960f332b271b484790eabfb9cc0c60b_50.png)

---

![](img/3960f332b271b484790eabfb9cc0c60b_52.png)

![](img/3960f332b271b484790eabfb9cc0c60b_54.png)

## 评估指标二：均方误差（MSE）📉

![](img/3960f332b271b484790eabfb9cc0c60b_56.png)

除了R²，另一个常用指标是**均方误差**。在Scikit-learn中，我们可以从`metrics`模块导入它。

以下是导入和使用MSE的代码：
```python
from sklearn.metrics import mean_squared_error

![](img/3960f332b271b484790eabfb9cc0c60b_58.png)

![](img/3960f332b271b484790eabfb9cc0c60b_60.png)

# 计算测试集上的均方误差
mse_test = mean_squared_error(y_true, y_pred)
print(mse_test)  # 输出例如 26.69
```

![](img/3960f332b271b484790eabfb9cc0c60b_62.png)

![](img/3960f332b271b484790eabfb9cc0c60b_64.png)

**MSE** 的含义：
*   它计算的是预测值与真实值之间差异的平方的平均值。
*   **MSE的值越小越好**，表示预测误差越小。

![](img/3960f332b271b484790eabfb9cc0c60b_66.png)

![](img/3960f332b271b484790eabfb9cc0c60b_68.png)

与R²类似，我们也可以计算模型在训练集上的MSE：

![](img/3960f332b271b484790eabfb9cc0c60b_70.png)

![](img/3960f332b271b484790eabfb9cc0c60b_72.png)

```python
# 计算训练集上的预测值和MSE
y_train_pred = model.predict(X_train)
mse_train = mean_squared_error(y_train, y_train_pred)
print(mse_train)  # 这个误差通常小于测试集上的误差
```

![](img/3960f332b271b484790eabfb9cc0c60b_74.png)

同样，由于模型是在训练集上“学习”的，所以训练集上的误差（MSE）通常会比测试集上的误差小。机器学习工程师的目标之一就是通过优化模型，让模型在测试集（新数据）上的表现尽可能接近在训练集上的表现。

![](img/3960f332b271b484790eabfb9cc0c60b_76.png)

---

![](img/3960f332b271b484790eabfb9cc0c60b_78.png)

![](img/3960f332b271b484790eabfb9cc0c60b_80.png)

## 总结 🎯

![](img/3960f332b271b484790eabfb9cc0c60b_82.png)

![](img/3960f332b271b484790eabfb9cc0c60b_84.png)

![](img/3960f332b271b484790eabfb9cc0c60b_86.png)

本节课中我们一起学习了模型评估的两个核心指标：

![](img/3960f332b271b484790eabfb9cc0c60b_88.png)

1.  **决定系数**：通过 `model.score()` 计算，其**公式**为 $R^2 = 1 - \frac{\sum(y_{true} - y_{pred})^2}{\sum(y_{true} - \bar{y}_{true})^2}$。**值越接近1，模型越好**。
2.  **均方误差**：通过 `mean_squared_error()` 函数计算。**值越小，模型越好**。

![](img/3960f332b271b484790eabfb9cc0c60b_90.png)

![](img/3960f332b271b484790eabfb9cc0c60b_92.png)

我们了解到，模型在训练数据上通常表现更好，但评估模型的真正标准是其在未知测试数据上的表现。一个强大且实用的模型，应该能够将从训练数据中学到的规律很好地推广到新数据上。