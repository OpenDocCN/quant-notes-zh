# 机器学习实战：P135：使用SVM支持向量机对LFW人脸数据集进行建模与参数选择

![](img/ee33ac7aea342325c94d5cdf4488c967_0.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_2.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_4.png)

在本节课中，我们将学习如何使用支持向量机（SVM）对LFW人脸数据集进行建模。我们将从数据加载开始，然后通过降维处理解决计算效率问题，接着训练一个基础的SVM模型，最后使用网格搜索技术来寻找最优的模型参数。整个过程将帮助你理解如何在实际项目中应用和优化SVM算法。

## 1：数据加载与初步探索

首先，我们需要导入必要的库并加载LFW人脸数据集。这个数据集包含了大量的人脸图像，我们将使用它来训练一个分类模型。

```python
# 导入必要的库
from sklearn.datasets import fetch_lfw_people
import matplotlib.pyplot as plt
from sklearn.decomposition import PCA
from sklearn.svm import SVC
from sklearn.model_selection import train_test_split, GridSearchCV
import numpy as np
import time

![](img/ee33ac7aea342325c94d5cdf4488c967_6.png)

# 加载LFW数据集
lfw_people = fetch_lfw_people(min_faces_per_person=70, resize=0.4)
X = lfw_people.data
y = lfw_people.target
target_names = lfw_people.target_names

![](img/ee33ac7aea342325c94d5cdf4488c967_8.png)

# 查看数据形状
print(f"数据集形状: {X.shape}")
print(f"目标类别数: {len(target_names)}")
```

![](img/ee33ac7aea342325c94d5cdf4488c967_10.png)

运行上述代码后，我们可以看到数据集的样本数量和特征维度。特征维度通常很高，因为每张图片都被展开成了一个很长的像素向量。

![](img/ee33ac7aea342325c94d5cdf4488c967_12.png)

为了对数据有一个直观的认识，我们可以显示数据集中的一些人脸图片。

![](img/ee33ac7aea342325c94d5cdf4488c967_14.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_16.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_18.png)

```python
# 显示部分人脸图片
fig, axes = plt.subplots(2, 5, figsize=(15, 8))
for i, ax in enumerate(axes.ravel()):
    ax.imshow(X[i].reshape(50, 37), cmap='gray')
    ax.set_title(target_names[y[i]])
    ax.axis('off')
plt.show()
```

## 2：数据预处理与降维

![](img/ee33ac7aea342325c94d5cdf4488c967_20.png)

上一节我们介绍了数据集的基本情况。本节中我们来看看如何通过降维来简化数据，从而提升模型训练效率。

原始数据的特征维度非常高（例如11750维），直接使用这些特征训练SVM模型会非常耗时。为了解决这个问题，我们使用主成分分析（PCA）来降低数据的维度，同时保留数据的主要信息。

![](img/ee33ac7aea342325c94d5cdf4488c967_22.png)

```python
# 使用PCA进行数据降维，保留95%的信息
pca = PCA(n_components=0.95, whiten=True)
%time X_pca = pca.fit_transform(X)

# 查看降维前后的数据形状
print(f"原始数据形状: {X.shape}")
print(f"降维后数据形状: {X_pca.shape}")
```

运行这段代码，你会发现计算时间大大缩短（例如几秒钟），并且特征数量从一万多减少到了几百个。这证明了降维在提升计算效率方面的巨大作用。

![](img/ee33ac7aea342325c94d5cdf4488c967_24.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_26.png)

## 3：基础SVM模型训练与评估

![](img/ee33ac7aea342325c94d5cdf4488c967_28.png)

在完成数据降维后，我们就可以开始训练模型了。本节我们将建立一个基础的SVM模型，并观察其性能。

首先，我们需要将数据集划分为训练集和测试集。

![](img/ee33ac7aea342325c94d5cdf4488c967_30.png)

```python
# 划分训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(X_pca, y, test_size=0.25, random_state=42)
```

![](img/ee33ac7aea342325c94d5cdf4488c967_32.png)

接下来，我们初始化一个SVM分类器并进行训练。这里我们暂时使用默认参数。

```python
# 初始化SVM分类器（使用默认参数C=1.0）
svc = SVC()

![](img/ee33ac7aea342325c94d5cdf4488c967_34.png)

# 记录训练时间并训练模型
%time svc.fit(X_train, y_train)

# 评估模型在训练集和测试集上的表现
train_score = svc.score(X_train, y_train)
test_score = svc.score(X_test, y_test)

![](img/ee33ac7aea342325c94d5cdf4488c967_36.png)

print(f"训练数据得分: {train_score:.3f}")
print(f"测试数据得分: {test_score:.3f}")
```

![](img/ee33ac7aea342325c94d5cdf4488c967_38.png)

运行后，你会得到模型的准确率。通常，训练集得分会高于测试集得分，如果差距过大，则可能出现了过拟合现象。

## 4：理解SVM参数C与过拟合

上一节我们训练了一个基础模型。本节中我们来看看SVM中一个关键参数 `C` 的作用，以及它与过拟合的关系。

参数 `C` 是SVM的正则化参数。它的值控制着模型对分类错误的容忍度。
*   **C值越大**：模型对错误的惩罚越强，会尽可能将所有训练样本正确分类，这容易导致模型过于复杂，在训练集上表现很好，但在未知数据上表现变差，即**过拟合**。
*   **C值越小**：模型对错误的容忍度越高，决策边界会更平滑，泛化能力可能更强，但可能无法很好地拟合训练数据，即**欠拟合**。

我们可以通过调整 `C` 的值来观察模型表现的变化。

```python
# 尝试不同的C值
for C_value in [0.1, 1, 10, 100]:
    svc_temp = SVC(C=C_value)
    svc_temp.fit(X_train, y_train)
    print(f"C={C_value:4} -> 训练得分: {svc_temp.score(X_train, y_train):.3f}, 测试得分: {svc_temp.score(X_test, y_test):.3f}")
```

运行代码，你会发现随着 `C` 增大，训练得分通常会上升，但测试得分可能先升后降。选择一个合适的 `C` 值对模型性能至关重要。

## 5：使用网格搜索进行自动化参数调优

手动尝试不同参数组合效率很低。本节我们将使用网格搜索（GridSearchCV）来自动寻找SVM的最佳参数组合。

网格搜索会为我们指定的所有参数组合进行交叉验证，并返回表现最好的那一组。

![](img/ee33ac7aea342325c94d5cdf4488c967_40.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_42.png)

以下是需要优化的关键参数：
*   **`C`**: 惩罚系数。
*   **`kernel`**: 核函数类型，如线性（`linear`）、多项式（`poly`）、径向基（`rbf`）。
*   **`tol`**: 优化算法的停止容差。

```python
# 定义参数网格
param_grid = {
    'C': np.logspace(-3, 3, 7),  # 生成[0.001, 0.01, 0.1, 1, 10, 100, 1000]
    'kernel': ['rbf', 'poly', 'linear'],
    'tol': [1e-3, 1e-4]
}

![](img/ee33ac7aea342325c94d5cdf4488c967_44.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_46.png)

# 初始化网格搜索对象
grid_search = GridSearchCV(SVC(), param_grid, cv=5, n_jobs=-1)

# 执行网格搜索（此步骤可能耗时较长）
%time grid_search.fit(X_pca, y)

![](img/ee33ac7aea342325c94d5cdf4488c967_48.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_50.png)

# 输出最佳参数和最佳得分
print("最佳参数组合:", grid_search.best_params_)
print("交叉验证最佳得分:", grid_search.best_score_)

# 使用最佳参数在测试集上评估最终模型
best_svc = grid_search.best_estimator_
final_test_score = best_svc.score(X_test, y_test)
print(f"使用最佳参数的模型在测试集上的得分: {final_test_score:.3f}")
```

**注意**：网格搜索需要尝试 `C`的7种取值 × `kernel`的3种取值 × `tol`的2种取值 = 42种组合，并对每种组合进行5折交叉验证，总共需要训练42*5=210个模型。因此运行时间会显著长于单次训练，这是寻找最优模型必要的计算成本。

![](img/ee33ac7aea342325c94d5cdf4488c967_52.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_54.png)

## 总结

本节课中我们一起学习了使用SVM处理LFW人脸数据集的完整流程。

![](img/ee33ac7aea342325c94d5cdf4488c967_56.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_58.png)

1.  **数据加载与探索**：我们加载了数据集并查看了其基本结构和样本图片。
2.  **数据预处理**：面对高维特征，我们使用PCA进行降维，在保留绝大部分信息（95%）的同时，将特征数量从一万多降至几百，极大提升了后续计算效率。
3.  **基础建模**：我们训练了一个使用默认参数的SVM模型，并评估了其在训练集和测试集上的表现，初步了解了模型性能。
4.  **参数理解**：我们深入探讨了正则化参数 `C` 的含义，理解了它如何影响模型的复杂度和过拟合倾向。
5.  **自动化调优**：最后，我们使用网格搜索（GridSearchCV）自动化地遍历了 `C`、`kernel`、`tol` 等参数的不同组合，通过交叉验证找到了针对当前数据集的最优参数，从而构建出性能更优的最终模型。

![](img/ee33ac7aea342325c94d5cdf4488c967_60.png)

![](img/ee33ac7aea342325c94d5cdf4488c967_62.png)

这个流程（数据准备 -> 特征工程 -> 基础建模 -> 参数调优）是解决机器学习实际问题的通用范式，熟练掌握它将帮助你有效地应用各种算法到不同的数据集上。