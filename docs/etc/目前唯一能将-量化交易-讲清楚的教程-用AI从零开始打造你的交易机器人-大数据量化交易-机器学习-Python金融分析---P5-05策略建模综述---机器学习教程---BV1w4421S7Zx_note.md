# 量化交易：P5：策略建模综述

## 概述
在本节课中，我们将学习如何将原始的金融数据转化为机器学习模型能够识别的训练集，并介绍几种核心的预测模型。这是从数据处理迈向策略构建的关键一步。

---

## 从原始数据到训练集

上一节我们学习了如何获取和存储原始金融数据。然而，这些原始数据（如开盘价、收盘价）并不能直接用于模型训练。本节中，我们来看看如何将这些“原材料”转化为监督学习框架下的训练集。

核心任务是将时间序列数据转化为特征矩阵 `X` 和目标向量 `Y`。对于每一个时间点 `T0`，我们需要：
*   基于 `T0` 时刻及之前的信息，计算出一组特征 `X1, X2, ..., XN`。
*   定义 `T0` 时刻之后我们想要预测的目标 `Y`（例如，`T0+1` 时刻的涨跌或价格）。

![](img/5aa0de5c381fc063e1e52a2f15d3f086_1.png)

![](img/5aa0de5c381fc063e1e52a2f15d3f086_3.png)

这个过程可以用以下公式表示：
```
X_train = [特征(T0), 特征(T0-1), ..., 特征(T0-N)]
Y_train = [目标(T0+1), 目标(T0), ..., 目标(T0-N+1)]
```

**以下是构建训练集的关键步骤：**
1.  **确定预测目标 (Y)**：可以是分类问题（如次日涨跌），也可以是回归问题（如次日收益率或价格）。
2.  **特征工程 (X)**：基于对金融市场的理解，从历史数据中提取可能影响未来价格的指标作为特征。

---

## 特征（因子）的选择

特征（或称因子）的选择是整个量化建模过程中最具创造性和决定性的环节。它高度依赖于建模者对市场逻辑的理解，没有绝对正确的理论，需要通过大量实验来验证。

**以下是几种常见的特征类型：**

*   **时间滞后特征 (Time Lags)**：直接使用过去若干个时间点的价格或成交量作为特征。例如，过去5天的收盘价。
    ```
    X1 = Price(T-1), X2 = Price(T-2), ..., X5 = Price(T-5)
    ```
*   **技术指标变换**：对原始价格序列进行数学变换，生成新的指标。这是技术分析领域的常见方法。
    *   **移动平均线 (MA)**：`MA(T, λ) = (Price(T) + ... + Price(T-λ+1)) / λ`
    *   **价格变化率 (ROC)**：`ROC(T) = (Price(T) - Price(T-n)) / Price(T-n)`
    *   **布林带 (Bollinger Bands)**：利用均值和标准差构造通道。
    *   **相对强弱指数 (RSI)、顺势指标 (CCI)** 等。
*   **外部关联特征**：引入其他可能相关的金融数据作为特征，例如汇率、其他指数、大宗商品价格等。
*   **统计量特征**：计算过去一段时间窗口内的统计量，如最高价、最低价、波动率等。

![](img/5aa0de5c381fc063e1e52a2f15d3f086_5.png)

**核心原则**：在初选特征时，应遵循“多多益善”的原则，尽可能将你认为所有相关的因子都纳入。后续可以通过特征选择方法进行筛选和优化。

---

![](img/5aa0de5c381fc063e1e52a2f15d3f086_7.png)

![](img/5aa0de5c381fc063e1e52a2f15d3f086_9.png)

![](img/5aa0de5c381fc063e1e52a2f15d3f086_11.png)

## 核心预测模型介绍

当我们拥有了特征矩阵 `X` 和目标向量 `Y` 后，就可以构建预测模型了。一个好的预测模型是盈利策略的基础，但模型本身不等于交易策略。

### 1. 集成学习模型

集成学习通过组合多个“弱学习器”来获得一个更强大、更稳定的“强学习器”，是金融预测中非常有效的方法。

**随机森林 (Random Forest)**
*   **核心思想**：并行地训练多棵决策树，每棵树在随机抽取的样本子集和特征子集上训练，最终通过投票或平均得到预测结果。
*   **优点**：泛化能力强，不易过拟合，可以输出特征重要性。
*   **代码示例 (Scikit-learn)**：
    ```python
    from sklearn.ensemble import RandomForestClassifier
    model = RandomForestClassifier(n_estimators=100, max_depth=5, random_state=42)
    model.fit(X_train, y_train)
    predictions = model.predict(X_test)
    ```

**梯度提升树 (Gradient Boosting, 如 XGBoost)**
*   **核心思想**：串行地训练多棵决策树，每一棵树学习的是之前所有树预测结果的残差，通过不断修正错误来提升模型。
*   **优点**：预测精度通常很高，在许多数据科学竞赛中表现出色。
*   **代码示例 (XGBoost)**：
    ```python
    import xgboost as xgb
    model = xgb.XGBClassifier(n_estimators=100, learning_rate=0.1, max_depth=3)
    model.fit(X_train, y_train)
    predictions = model.predict(X_test)
    ```

### 2. 线性与广义线性模型

这类模型结构简单，解释性强，常作为性能基准。

**逻辑回归 (Logistic Regression)**
*   **用途**：主要用于二分类问题（涨/跌）。
*   **公式**：`P(Y=1|X) = 1 / (1 + exp(-(W*X + b)))`
*   **代码示例**：
    ```python
    from sklearn.linear_model import LogisticRegression
    model = LogisticRegression()
    model.fit(X_train, y_train)
    ```

**支持向量机 (SVM) / 支持向量回归 (SVR)**
*   **用途**：可用于分类和回归问题，通过寻找最大间隔超平面来实现。
*   **代码示例**：
    ```python
    from sklearn import svm
    model = svm.SVC(kernel='rbf') # 用于分类
    # model = svm.SVR(kernel='rbf') # 用于回归
    model.fit(X_train, y_train)
    ```

### 3. 神经网络 (Neural Networks)

神经网络，特别是多层感知机，能够自动学习特征之间的复杂非线性关系。

**多层感知机 (MLP)**
*   **核心结构**：包含输入层、多个隐藏层和输出层。每层由神经元组成，神经元之间有权重连接。
*   **激活函数**：常用 ReLU (`f(x)=max(0,x)`)。
*   **防止过拟合**：常使用 Dropout 技术，随机在训练过程中“关闭”一部分神经元，本质是一种集成学习。
*   **代码示例 (Keras)**：
    ```python
    from keras.models import Sequential
    from keras.layers import Dense, Dropout

    model = Sequential()
    model.add(Dense(512, activation='relu', input_dim=X_train.shape[1]))
    model.add(Dropout(0.2))
    model.add(Dense(256, activation='relu'))
    model.add(Dropout(0.2))
    model.add(Dense(1, activation='sigmoid')) # 二分类输出层

    model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])
    model.fit(X_train, y_train, epochs=10, batch_size=128, validation_data=(X_test, y_test))
    ```

---

## 模型评估与超参数调优

![](img/5aa0de5c381fc063e1e52a2f15d3f086_13.png)

**交叉验证 (Cross-Validation)**
*   **目的**：为了更可靠地评估模型性能，避免因数据划分偶然性导致的偏差。同时用于寻找模型的最佳超参数。
*   **方法**：将训练集分成K份，轮流将其中一份作为验证集，其余作为训练集，重复K次，取平均性能。

**超参数调优**
*   **网格搜索 (Grid Search)**：暴力枚举所有超参数组合。
*   **随机搜索 (Random Search)**：在超参数空间中随机采样。
*   **贝叶斯优化 (Bayesian Optimization)**：基于已有评估结果，智能地选择下一个可能更优的超参数组合进行尝试，效率更高。
*   **代码示例 (Scikit-learn GridSearchCV)**：
    ```python
    from sklearn.model_selection import GridSearchCV
    param_grid = {'n_estimators': [50, 100, 200], 'max_depth': [3, 5, 7]}
    grid_search = GridSearchCV(RandomForestClassifier(), param_grid, cv=5)
    grid_search.fit(X_train, y_train)
    best_model = grid_search.best_estimator_
    ```

---

![](img/5aa0de5c381fc063e1e52a2f15d3f086_15.png)

## 总结

本节课中我们一起学习了量化策略建模的核心流程：
1.  **数据转换**：将原始金融时间序列数据转化为监督学习所需的 `(X, Y)` 训练集。
2.  **特征工程**：基于领域知识，从数据中提取和构造可能影响预测目标的特征因子。这是量化研究的核心竞争力之一。
3.  **模型构建**：介绍了随机森林、梯度提升树、逻辑回归、支持向量机和神经网络等几类在量化领域常用的预测模型。
4.  **模型优化**：强调了使用交叉验证进行模型评估和超参数调优的重要性。

![](img/5aa0de5c381fc063e1e52a2f15d3f086_17.png)

记住，得到一个预测准确的模型只是第一步。下一节课，我们将学习如何从大量特征中筛选出最有效的子集（特征选择），并开始探讨如何将预测模型与事件驱动的交易系统结合，进行策略回测。