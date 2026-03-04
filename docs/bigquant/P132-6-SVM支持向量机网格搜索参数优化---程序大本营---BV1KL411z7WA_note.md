# 机器学习：P132：SVM支持向量机网格搜索参数优化 📊

![](img/2913454af0346aef9da69f68b0cb8532_1.png)

![](img/2913454af0346aef9da69f68b0cb8532_3.png)

![](img/2913454af0346aef9da69f68b0cb8532_5.png)

![](img/2913454af0346aef9da69f68b0cb8532_7.png)

在本节课中，我们将学习如何使用网格搜索（Grid Search）与交叉验证（Cross-Validation）来优化支持向量机（SVM）模型的参数，从而提升模型的预测准确率。

![](img/2913454af0346aef9da69f68b0cb8532_9.png)

![](img/2913454af0346aef9da69f68b0cb8532_11.png)

---

![](img/2913454af0346aef9da69f68b0cb8532_13.png)

上一节我们介绍了SVM的基本原理和代码实现。本节中，我们来看看如何通过系统化的方法寻找模型的最优参数。

## 数据加载与预处理

![](img/2913454af0346aef9da69f68b0cb8532_15.png)

![](img/2913454af0346aef9da69f68b0cb8532_17.png)

![](img/2913454af0346aef9da69f68b0cb8532_19.png)

首先，我们需要加载训练数据和测试数据。数据文件位于当前目录下，分别是 `train_data` 和 `test_data`。我们将定义一个函数来读取这些数据。

![](img/2913454af0346aef9da69f68b0cb8532_21.png)

![](img/2913454af0346aef9da69f68b0cb8532_23.png)

```python
import numpy as np
from sklearn.svm import SVC

def load_data(file_path):
    """
    从指定路径加载数据。
    文件格式：每行是一个样本，最后一列是目标值。
    """
    with open(file_path, 'r') as file:
        lines = file.readlines()
    data = [line.strip().split() for line in lines]
    data = np.array(data, dtype=float)
    X = data[:, :-1]  # 特征
    y = data[:, -1]   # 目标值
    return X, y
```

以下是数据加载的具体步骤：
1.  使用 `open` 函数打开数据文件。
2.  使用 `readlines` 方法读取所有行。
3.  使用列表生成式去除每行首尾空格并按空格分割。
4.  将列表转换为NumPy数组，并分离特征 `X` 和目标值 `y`。

![](img/2913454af0346aef9da69f68b0cb8532_25.png)

现在，我们调用这个函数来加载数据。

![](img/2913454af0346aef9da69f68b0cb8532_27.png)

```python
# 加载训练数据和测试数据
X_train, y_train = load_data('./data/train_data')
X_test, y_test = load_data('./data/test_data')

# 查看数据形状和类别
print(f"训练数据形状: {X_train.shape}")
print(f"测试数据形状: {X_test.shape}")
print(f"目标值类别: {np.unique(y_train)}")
```
执行代码后，我们可以看到训练数据有500个样本，测试数据有100个样本，目标值为0和1两类。

## 基础SVM模型训练

在优化参数之前，我们先使用默认参数训练一个基础的SVM模型，作为性能基准。

```python
# 创建SVM模型（使用默认参数）
svm_model = SVC()
# 训练模型
svm_model.fit(X_train, y_train.ravel())
# 在测试集上评估
score = svm_model.score(X_test, y_test.ravel())
print(f"默认参数模型准确率: {score}")
```
使用默认参数（如 `C=1.0`, `kernel='rbf'`）时，模型准确率可能已经很高（例如达到1.0），但这并不意味着参数是最优的。我们可以尝试手动调整参数，例如改变惩罚系数 `C` 或核函数 `kernel`，观察准确率的变化。

```python
# 尝试不同的参数
svm_model_c_small = SVC(C=0.001)
svm_model_c_small.fit(X_train, y_train.ravel())
print(f"C=0.001时准确率: {svm_model_c_small.score(X_test, y_test.ravel())}")

svm_model_linear = SVC(kernel='linear')
svm_model_linear.fit(X_train, y_train.ravel())
print(f"kernel='linear'时准确率: {svm_model_linear.score(X_test, y_test.ravel())}")
```
手动调整效率低下且不系统。接下来，我们将使用网格搜索来自动化这个过程。

![](img/2913454af0346aef9da69f68b0cb8532_29.png)

## 网格搜索与交叉验证

![](img/2913454af0346aef9da69f68b0cb8532_31.png)

网格搜索（`GridSearchCV`）可以系统地遍历多种参数组合，并通过交叉验证评估每种组合的性能，最终找到最优参数。

### 理解交叉验证（CV）

交叉验证是评估模型泛化能力的重要方法。以5折交叉验证（`cv=5`）为例：
1.  将训练数据随机分成5个大小相似的子集（折）。
2.  依次将其中1个子集作为验证集，其余4个作为训练集，进行5次训练和验证。
3.  计算5次验证结果的平均值，作为该参数组合的性能指标。
这种方法充分利用了数据，使评估结果更稳健。

![](img/2913454af0346aef9da69f68b0cb8532_33.png)

### 实施网格搜索

![](img/2913454af0346aef9da69f68b0cb8532_35.png)

![](img/2913454af0346aef9da69f68b0cb8532_37.png)

我们将使用 `GridSearchCV` 来优化SVM的 `C`（惩罚系数）和 `tol`（容忍度/收敛阈值）参数。

![](img/2913454af0346aef9da69f68b0cb8532_39.png)

![](img/2913454af0346aef9da69f68b0cb8532_41.png)

![](img/2913454af0346aef9da69f68b0cb8532_43.png)

首先，导入必要的模块并定义参数网格。

![](img/2913454af0346aef9da69f68b0cb8532_45.png)

```python
from sklearn.model_selection import GridSearchCV

# 定义要搜索的参数网格
param_grid = {
    'C': np.logspace(-3, 3, 50),  # C的取值范围：10^-3 到 10^3，生成50个等比数列值
    'tol': [1e-4, 1e-3, 1e-2, 1e-1, 1]  # tol的候选值列表
}
```
**公式说明**：`np.logspace(-3, 3, 50)` 生成一个等比数列，首项为 $10^{-3}$，末项为 $10^{3}$，共50项。这涵盖了从0.001到1000的一个广泛范围。

![](img/2913454af0346aef9da69f68b0cb8532_47.png)

![](img/2913454af0346aef9da69f68b0cb8532_49.png)

然后，创建 `GridSearchCV` 对象并进行拟合。

```python
# 创建GridSearchCV对象
# estimator: 要优化的模型（SVC）
# param_grid: 参数网格
# cv: 交叉验证折数，默认为5
# n_jobs: 并行运行的作业数，-1表示使用所有处理器
grid_search = GridSearchCV(estimator=SVC(),
                           param_grid=param_grid,
                           cv=5,
                           n_jobs=-1)

![](img/2913454af0346aef9da69f68b0cb8532_51.png)

# 在训练数据上执行网格搜索
grid_search.fit(X_train, y_train.ravel())
```
拟合过程会遍历 `C`（50个值）和 `tol`（5个值）的所有组合（共250种），并对每种组合进行5折交叉验证，总共训练1250次模型。

### 获取最优结果

搜索完成后，我们可以查看找到的最优参数和对应的最佳模型。

![](img/2913454af0346aef9da69f68b0cb8532_53.png)

```python
# 输出最优参数组合
print("找到的最优参数:", grid_search.best_params_)
# 输出最优模型在交叉验证中的平均分数
print("交叉验证最佳分数:", grid_search.best_score_)

![](img/2913454af0346aef9da69f68b0cb8532_55.png)

![](img/2913454af0346aef9da69f68b0cb8532_57.png)

# 使用最优模型在测试集上评估
best_model = grid_search.best_estimator_
test_score = best_model.score(X_test, y_test.ravel())
print(f"最优模型在测试集上的准确率: {test_score}")
```
通过对比可以发现，经过网格搜索优化后的模型，其测试准确率相较于手动调整或默认参数模型，通常会有显著提升（例如从0.83提升到0.98）。

---

本节课中我们一起学习了如何使用网格搜索（`GridSearchCV`）对支持向量机模型进行参数优化。关键步骤包括：
1.  **定义参数网格**：明确需要优化的参数及其候选值范围。
2.  **配置交叉验证**：通过交叉验证确保参数评估的稳健性。
3.  **执行搜索与评估**：让 `GridSearchCV` 自动完成参数组合的遍历、训练和验证，并找到最优解。

![](img/2913454af0346aef9da69f68b0cb8532_59.png)

这种方法将我们从繁琐的手动调参中解放出来，是机器学习实践中提高模型性能的标准化且高效的工具。