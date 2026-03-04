# 线性代数：P163-2：特征值与奇异值分解 🔢

![](img/40987d330529cbbb9d45a3b7425969ac_0.png)

![](img/40987d330529cbbb9d45a3b7425969ac_2.png)

在本节课中，我们将学习线性代数中两个核心概念：特征值/特征向量分解（EVD）与奇异值分解（SVD）。我们将通过具体的代码示例，直观地展示它们的数学关系与计算方法，并理解如何从分解结果重构原始矩阵。

---

## 数据准备与协方差计算 📊

首先，我们加载鸢尾花数据集并进行预处理，计算其协方差矩阵。协方差矩阵反映了数据不同属性（特征）之间的相关性。

![](img/40987d330529cbbb9d45a3b7425969ac_4.png)

以下是具体步骤：
1.  导入数据集并进行中心化处理（减去均值）。
2.  计算中心化后数据的协方差矩阵。这里我们按列（属性）计算。

![](img/40987d330529cbbb9d45a3b7425969ac_6.png)

```python
from sklearn import datasets
import numpy as np

![](img/40987d330529cbbb9d45a3b7425969ac_8.png)

# 加载鸢尾花数据
X, y = datasets.load_iris(return_X_y=True)
# 数据去中心化
A = X - X.mean(axis=0)
# 计算协方差矩阵，rowvar=False表示列代表属性
B = np.cov(A, rowvar=False)
print(B)
```

![](img/40987d330529cbbb9d45a3b7425969ac_10.png)

执行上述代码后，我们得到了一个4x4的协方差矩阵 `B`。

---

![](img/40987d330529cbbb9d45a3b7425969ac_12.png)

![](img/40987d330529cbbb9d45a3b7425969ac_14.png)

## 计算特征值与特征向量 🧮

![](img/40987d330529cbbb9d45a3b7425969ac_16.png)

上一节我们准备好了协方差矩阵 `B`，本节中我们来看看如何计算它的特征值与特征向量。特征值与特征向量是理解矩阵结构的关键。

我们可以直接使用 `numpy.linalg.eig` 函数进行计算。

```python
# 计算矩阵B的特征值和特征向量
eigenvalues, eigenvectors = np.linalg.eig(B)
print("特征值 (eigenvalues):\n", eigenvalues)
print("特征向量 (eigenvectors):\n", eigenvectors)
```

![](img/40987d330529cbbb9d45a3b7425969ac_18.png)

![](img/40987d330529cbbb9d45a3b7425969ac_20.png)

函数返回两个值：`eigenvalues`（特征值数组）和 `eigenvectors`（特征向量矩阵，每一列是一个特征向量）。

---

## 理解特征值分解公式 🔗

现在我们已经得到了特征值和特征向量，接下来我们探讨它们与原始矩阵 `B` 之间的数学关系。这个关系由特征值分解的核心公式定义。

对于一个矩阵 **A**，如果存在一个非零向量 **v** 和一个标量 **λ**，满足以下公式：
**A · v = λ · v**
那么，**v** 就是 **A** 的特征向量，**λ** 是其对应的特征值。

让我们用代码验证这个关系。我们将用矩阵 `B` 乘以它的第一个特征向量，并与对应的特征值乘以该特征向量的结果进行比较。

![](img/40987d330529cbbb9d45a3b7425969ac_22.png)

```python
# 验证公式 A·v = λ·v
# 取第一个特征值和对应的特征向量
lambda_0 = eigenvalues[0]
v_0 = eigenvectors[:, 0]

# 计算 B · v_0
left_side = B.dot(v_0)
# 计算 λ_0 · v_0
right_side = lambda_0 * v_0

![](img/40987d330529cbbb9d45a3b7425969ac_24.png)

print("B · v_0:\n", left_side)
print("λ_0 · v_0:\n", right_side)
# 检查两者是否相等（在浮点数精度允许范围内）
print("是否相等？", np.allclose(left_side, right_side))
```

![](img/40987d330529cbbb9d45a3b7425969ac_26.png)

![](img/40987d330529cbbb9d45a3b7425969ac_28.png)

运行代码，你会发现两者结果相同，验证了公式的正确性。

![](img/40987d330529cbbb9d45a3b7425969ac_30.png)

---

![](img/40987d330529cbbb9d45a3b7425969ac_32.png)

## 矩阵的分解与重构 🧩

![](img/40987d330529cbbb9d45a3b7425969ac_34.png)

特征值和特征向量更强大的地方在于，它们可以将一个方阵分解。这种分解可以表示为另一个重要公式：
**A = P · Λ · P⁻¹**
其中：
*   **P** 是由矩阵 **A** 的所有特征向量组成的矩阵。
*   **Λ** 是一个对角矩阵，其对角线上的元素就是 **A** 的特征值。
*   **P⁻¹** 是 **P** 的逆矩阵。

这意味着，我们可以用特征值和特征向量来“重建”原始矩阵。以下是验证步骤：

![](img/40987d330529cbbb9d45a3b7425969ac_36.png)

1.  **构建对角矩阵 Λ**：使用计算出的特征值。
2.  **计算逆矩阵 P⁻¹**：对特征向量矩阵求逆。
3.  **进行矩阵乘法**：计算 **P · Λ · P⁻¹**，结果应等于原始矩阵 **B**。

![](img/40987d330529cbbb9d45a3b7425969ac_38.png)

```python
# 1. 构建对角矩阵 Λ
Lambda = np.diag(eigenvalues)

![](img/40987d330529cbbb9d45a3b7425969ac_40.png)

![](img/40987d330529cbbb9d45a3b7425969ac_42.png)

# 2. 计算特征向量矩阵的逆
P_inv = np.linalg.inv(eigenvectors)

# 3. 根据公式 A = P · Λ · P⁻¹ 重构矩阵
reconstructed_B = eigenvectors.dot(Lambda).dot(P_inv)

![](img/40987d330529cbbb9d45a3b7425969ac_44.png)

print("原始矩阵 B:\n", B)
print("\n重构后的矩阵:\n", reconstructed_B)
print("\n重构是否成功？", np.allclose(B, reconstructed_B))
```

![](img/40987d330529cbbb9d45a3b7425969ac_46.png)

![](img/40987d330529cbbb9d45a3b7425969ac_48.png)

执行后，你会发现 `reconstructed_B` 与原始的 `B` 在数值上完全一致（忽略微小的浮点数误差）。这完美地演示了特征值分解的原理。

![](img/40987d330529cbbb9d45a3b7425969ac_50.png)

---

![](img/40987d330529cbbb9d45a3b7425969ac_52.png)

## 课程总结 📝

![](img/40987d330529cbbb9d45a3b7425969ac_54.png)

![](img/40987d330529cbbb9d45a3b7425969ac_56.png)

本节课我们一起学习了线性代数中特征值与特征向量分解的核心内容。

![](img/40987d330529cbbb9d45a3b7425969ac_58.png)

我们首先加载了真实数据集并计算了协方差矩阵。然后，我们使用 `np.linalg.eig` 计算了该矩阵的特征值和特征向量，并通过代码验证了核心公式 **A · v = λ · v**。最后，我们深入探讨了矩阵的分解形式 **A = P · Λ · P⁻¹**，并成功利用特征值和特征向量重构了原始矩阵。

![](img/40987d330529cbbb9d45a3b7425969ac_60.png)

![](img/40987d330529cbbb9d45a3b7425969ac_62.png)

理解特征值分解是学习更复杂矩阵运算（如奇异值分解SVD）和许多机器学习算法（如PCA主成分分析）的重要基础。