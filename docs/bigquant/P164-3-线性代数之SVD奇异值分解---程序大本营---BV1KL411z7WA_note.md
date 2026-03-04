# 线性代数：P164：SVD奇异值分解 🧮

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_1.png)

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_2.png)

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_4.png)

在本节课中，我们将要学习奇异值分解。这是一种重要的矩阵因子分解运算，尤其适用于那些无法进行特征值分解的矩阵。我们将通过对比特征值分解，来理解SVD的结构、意义和计算方法。

上一节我们介绍了特征值和特征向量分解，本节中我们来看看奇异值分解。

## 奇异值分解的概念

奇异值分解也是一种矩阵因子分解运算。它将一个矩阵分解为三个特定矩阵的乘积。

其核心公式如下：
**A ≈ U Σ V^T**

在这个公式中：
*   **U** 被称为左奇异向量矩阵。
*   **Σ** 是一个对角矩阵，其对角线上的值称为奇异值。
*   **V^T** 是右奇异向量矩阵 **V** 的转置。

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_6.png)

## 与特征值分解的对比

奇异值分解的结构与特征值分解类似。特征值分解的公式为 **A = P Λ P^(-1)**。

以下是两者的对应关系：
*   左奇异矩阵 **U** 对应特征向量矩阵 **P**。
*   右奇异矩阵的转置 **V^T** 对应逆矩阵 **P^(-1)**。
*   奇异值矩阵 **Σ** 对应特征值对角矩阵 **Λ**。

然而，两者有一个关键区别：特征值分解要求矩阵是方阵且可对角化，其结果通常是精确的等式（**A = P Λ P^(-1)**）。而奇异值分解适用于任意形状的矩阵（包括非方阵），其结果通常是一个近似（**A ≈ U Σ V^T**）。

## 为什么需要奇异值分解？

并非所有矩阵都能进行特征值分解。例如，对于一个3行5列的矩阵，它代表3个方程包含5个未知数。这样的方程组没有唯一解，因此无法进行标准的特征值分解。

在数学上，这种“不圆满”、无法用标准方法完美分解的情况，催生了奇异值分解。SVD为这类问题提供了一种近似的、但极其有用的分解方案。

## 代码实践：SVD计算

让我们通过代码来演示奇异值分解。首先，我们创建一个无法进行特征值分解的矩阵。

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_8.png)

```python
import numpy as np

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_10.png)

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_12.png)

# 创建一个3行5列的随机整数矩阵
X = np.random.randint(0, 10, size=(3, 5))
print("原始矩阵 X:")
print(X)
```

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_14.png)

接下来，我们使用SciPy库中的`linalg.svd`函数进行奇异值分解。

```python
from scipy import linalg

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_16.png)

# 进行奇异值分解，full_matrices=False会返回紧凑形式的分解
U, Sigma, Vt = linalg.svd(X, full_matrices=False)

print("\n左奇异矩阵 U:")
print(U)
print("\n奇异值 Sigma (向量形式):")
print(Sigma)
print("\n右奇异矩阵的转置 Vt:")
print(Vt)
```

计算完成后，我们得到了三个部分：**U**、**Sigma**（以向量形式存储的奇异值）和 **Vt**。

## 重构原始矩阵

为了验证分解的有效性，我们可以用分解出的三个矩阵来近似重构原始矩阵 **X**。由于 **Sigma** 是向量，我们需要先将其转换为对角矩阵。

```python
# 将奇异值向量Sigma转换为对角矩阵
Sigma_matrix = np.eye(3) * Sigma  # 创建一个3x3的对角矩阵，对角线元素为Sigma

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_18.png)

# 使用 U, Sigma_matrix, Vt 重构矩阵
X_reconstructed = U.dot(Sigma_matrix).dot(Vt)

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_20.png)

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_22.png)

print("\n使用SVD重构的矩阵 X_reconstructed:")
print(X_reconstructed)
print("\n原始矩阵 X (用于对比):")
print(X)
```

你会看到 `X_reconstructed` 与原始矩阵 `X` 在数值上非常接近。细微的差异（如非常接近零的值）是由于浮点数计算精度和SVD本身的近似性质导致的。

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_24.png)

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_26.png)

## 总结

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_28.png)

本节课中我们一起学习了奇异值分解。
1.  **SVD是什么**：它是一种将矩阵分解为 **U Σ V^T** 三个矩阵的运算。
2.  **与特征值分解的关系**：结构相似，但SVD适用于更广泛的矩阵（包括非方阵），结果是近似解。
3.  **核心计算**：通过 `scipy.linalg.svd` 函数可以方便地计算矩阵的SVD。
4.  **应用验证**：利用分解出的矩阵可以近似重构原始矩阵，这证明了分解的有效性。

![](img/c4bb2c8976a51a295bd71a1e1fcebe15_30.png)

奇异值分解是主成分分析等多种机器学习算法的基础，理解其原理至关重要。