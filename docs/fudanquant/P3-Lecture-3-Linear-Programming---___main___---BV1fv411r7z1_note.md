# 机器学习：P3：线性代数复习与线性规划入门 🧮

在本节课中，我们将学习机器学习中两个重要的优化问题：线性规划和二次规划。优化问题是机器学习的核心，而线性规划与二次规划则是其中最简单和基础的形式。我们将首先复习必要的线性代数知识，然后深入探讨线性规划的定义、几何意义及其在分类问题中的应用。

## 线性代数核心概念复习 📚

![](img/9c2727a5d618369bacc560c3a18c9f53_1.png)

上一节我们介绍了感知机模型，其中用到了向量和矩阵的概念。本节中，我们来系统地复习一下线性代数的基本知识，这些知识是理解后续优化问题的基础。

### 矩阵的基本定义与运算

![](img/9c2727a5d618369bacc560c3a18c9f53_3.png)

矩阵是将数字按行和列排列的矩形阵列。一个 **m × n** 的矩阵 **A** 可以表示为：

![](img/9c2727a5d618369bacc560c3a18c9f53_5.png)

$$
A = \begin{bmatrix}
a_{11} & a_{12} & \cdots & a_{1n} \\
a_{21} & a_{22} & \cdots & a_{2n} \\
\vdots & \vdots & \ddots & \vdots \\
a_{m1} & a_{m2} & \cdots & a_{mn}
\end{bmatrix}
$$

其中，$a_{ij}$ 是实数，$i=1,...,m$, $j=1,...,n$。

![](img/9c2727a5d618369bacc560c3a18c9f53_7.png)

矩阵可以进行以下基本运算：
*   **加法/减法**：仅适用于同维度的矩阵，对应元素相加减。
*   **数乘**：一个实数乘以矩阵的每一个元素。
*   **乘法**：矩阵 **A** (m × n) 与矩阵 **B** (n × k) 可以相乘，结果 **C** (m × k) 的元素为：
    $$ c_{ij} = \sum_{t=1}^{n} a_{it} b_{tj} $$
    矩阵乘法不满足交换律，即通常 $AB \neq BA$。

![](img/9c2727a5d618369bacc560c3a18c9f53_9.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_10.png)

### 向量与矩阵的表示

向量是矩阵的特殊形式。
*   **行向量**：1 × n 的矩阵，例如 $\mathbf{a} = [a_1, a_2, ..., a_n]$。
*   **列向量**：n × 1 的矩阵，例如 $\mathbf{b} = [b_1, b_2, ..., b_n]^T$。
    在线性代数中，我们通常**默认使用列向量**。一个矩阵也可以用其列向量或行向量组表示：
    $$ A = [\mathbf{a}_1, \mathbf{a}_2, ..., \mathbf{a}_n] $$
    其中每个 $\mathbf{a}_j$ 是一个 m 维列向量。

![](img/9c2727a5d618369bacc560c3a18c9f53_12.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_14.png)

### 矩阵作为线性映射

矩阵更深刻的含义是**线性空间之间的线性映射**。一个 m × n 的矩阵 **A** 定义了一个从 $\mathbb{R}^n$ 到 $\mathbb{R}^m$ 的线性变换 **L**：
对于任意向量 $\mathbf{x} \in \mathbb{R}^n$，有 $L(\mathbf{x}) = A\mathbf{x} \in \mathbb{R}^m$。
线性性质满足：$L(\alpha \mathbf{u} + \beta \mathbf{v}) = \alpha L(\mathbf{u}) + \beta L(\mathbf{v})$。

![](img/9c2727a5d618369bacc560c3a18c9f53_16.png)

### 方阵的特殊性质

![](img/9c2727a5d618369bacc560c3a18c9f53_18.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_20.png)

当矩阵的行数等于列数（即 **n × n 方阵**）时，我们可计算：
*   **行列式 (Determinant)**：记为 $\det(A)$ 或 $|A|$。行列式可以为零、正或负。
*   **迹 (Trace)**：所有主对角线元素之和，记为 $\text{tr}(A) = \sum_{i=1}^n a_{ii}$。
    行列式和迹都是线性变换的**不变量**，不依赖于基的选择。对于矩阵乘法，有：
    $$ \det(AB) = \det(A)\det(B) $$
    $$ \text{tr}(AB) = \text{tr}(BA) $$

![](img/9c2727a5d618369bacc560c3a18c9f53_22.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_24.png)

### 线性方程组

![](img/9c2727a5d618369bacc560c3a18c9f53_26.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_28.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_30.png)

线性方程组 $A\mathbf{x} = \mathbf{b}$，其中 $A$ 是 m × n 矩阵。当 $m = n$ 且 $\det(A) \neq 0$ 时，矩阵 **A** 可逆，方程有唯一解：$\mathbf{x} = A^{-1}\mathbf{b}$。

![](img/9c2727a5d618369bacc560c3a18c9f53_32.png)

### 特征值与特征向量

对于 n 阶方阵 **A**，若存在数 $\lambda$ 和非零向量 $\mathbf{v}$，使得 $A\mathbf{v} = \lambda \mathbf{v}$，则称 $\lambda$ 为 **A** 的**特征值**，$\mathbf{v}$ 为对应的**特征向量**。
对于**对称矩阵**（满足 $A^T = A$），其所有特征值都是实数，并且存在一组两两正交的特征向量。

![](img/9c2727a5d618369bacc560c3a18c9f53_34.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_36.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_38.png)

### 向量的内积与长度

![](img/9c2727a5d618369bacc560c3a18c9f53_40.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_42.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_44.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_46.png)

在 $\mathbb{R}^n$ 中，向量 $\mathbf{x}$ 和 $\mathbf{y}$ 的**内积**（点积）定义为：
$$ \langle \mathbf{x}, \mathbf{y} \rangle = \mathbf{x}^T \mathbf{y} = \sum_{i=1}^n x_i y_i $$
向量 $\mathbf{x}$ 的**长度**（欧几里得范数）为：
$$ \|\mathbf{x}\| = \sqrt{\langle \mathbf{x}, \mathbf{x} \rangle} = \sqrt{\sum_{i=1}^n x_i^2} $$
内积的几何意义是描述两个向量的夹角 $\theta$：$\cos \theta = \frac{\langle \mathbf{x}, \mathbf{y} \rangle}{\|\mathbf{x}\| \|\mathbf{y}\|}$。
由此可得重要的**柯西-施瓦茨不等式**：
$$ |\langle \mathbf{x}, \mathbf{y} \rangle| \le \|\mathbf{x}\| \|\mathbf{y}\| $$

### 二次型

![](img/9c2727a5d618369bacc560c3a18c9f53_48.png)

给定对称矩阵 **A**，函数 $Q(\mathbf{x}) = \mathbf{x}^T A \mathbf{x}$ 称为关于 $\mathbf{x}$ 的**二次型**。这是一个二次函数，在优化问题中非常重要。

![](img/9c2727a5d618369bacc560c3a18c9f53_50.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_52.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_54.png)

---

![](img/9c2727a5d618369bacc560c3a18c9f53_56.png)

## 优化问题与线性规划 🎯

![](img/9c2727a5d618369bacc560c3a18c9f53_58.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_60.png)

复习完线性代数后，我们进入今天的核心主题：优化问题，特别是线性规划。

![](img/9c2727a5d618369bacc560c3a18c9f53_62.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_64.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_66.png)

### 什么是优化问题

![](img/9c2727a5d618369bacc560c3a18c9f53_68.png)

优化问题的目标是寻找一个函数在特定区域内的最大值或最小值。
*   **无约束优化**：在整个定义域（如 $\mathbb{R}^n$）中求函数 $f(\mathbf{x})$ 的极值。
*   **约束优化**：在满足一定条件（约束）的区域 $\Omega$ 内求函数 $f(\mathbf{x})$ 的极值。约束通常由等式 $g(\mathbf{x}) = 0$ 或不等式 $g(\mathbf{x}) \le 0$ 定义。

![](img/9c2727a5d618369bacc560c3a18c9f53_70.png)

### 线性规划的定义

![](img/9c2727a5d618369bacc560c3a18c9f53_72.png)

**线性规划**是一种特殊的约束优化问题，其目标函数和所有约束条件都是自变量的线性函数。
线性规划的标准形式如下：
$$
\begin{aligned}
& \text{minimize} \quad \mathbf{c}^T \mathbf{x} \\
& \text{subject to} \quad A\mathbf{x} \le \mathbf{b}
\end{aligned}
$$
其中：
*   $\mathbf{x} \in \mathbb{R}^n$ 是决策变量。
*   $\mathbf{c} \in \mathbb{R}^n$ 是目标函数的系数向量。
*   $A$ 是 m × n 的约束矩阵。
*   $\mathbf{b} \in \mathbb{R}^m$ 是约束条件的右端向量。
*   “$\le$” 表示对向量的每个分量进行比较。

![](img/9c2727a5d618369bacc560c3a18c9f53_74.png)

我们也可以求最大值（`maximize`），通过改变 $\mathbf{c}$ 的符号可以相互转化。

![](img/9c2727a5d618369bacc560c3a18c9f53_76.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_78.png)

### 线性规划的几何直观

![](img/9c2727a5d618369bacc560c3a18c9f53_80.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_82.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_84.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_86.png)

在二维平面（$\mathbb{R}^2$）上，每个线性不等式 $a_{i1}x_1 + a_{i2}x_2 \le b_i$ 表示一个半平面。m 个这样的半平面的交集构成一个**可行域** $\Omega$。

![](img/9c2727a5d618369bacc560c3a18c9f53_88.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_90.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_92.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_94.png)

可行域 $\Omega$ 具有一个关键性质：它是一个**凸集**。凸集的定义是：集合内任意两点的连线仍然包含在该集合内。即，对任意 $\mathbf{x}, \mathbf{y} \in \Omega$ 和任意 $t \in [0, 1]$，有 $t\mathbf{x} + (1-t)\mathbf{y} \in \Omega$。
线性不等式约束围成的区域是一种特殊的凸集——**凸多边形**。

目标函数 $\mathbf{c}^T \mathbf{x}$ 是线性的，其等高线是平行直线族。寻找最优解的过程，相当于让这条等高线在可行域 $\Omega$ 内平行移动。
一个重要结论是：线性规划的最优解（如果存在）一定在可行域 $\Omega$ 的**顶点**（即凸多边形的角点）上达到。

### 线性规划的变形与转化

![](img/9c2727a5d618369bacc560c3a18c9f53_96.png)

许多看似非线性的问题可以转化为线性规划问题来解决。以下是两个例子：

![](img/9c2727a5d618369bacc560c3a18c9f53_98.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_100.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_102.png)

1.  **等式约束**：约束 $A\mathbf{x} = \mathbf{b}$ 可以等价地写为两个不等式：
    $$ A\mathbf{x} \le \mathbf{b} \quad \text{且} \quad -A\mathbf{x} \le -\mathbf{b} $$
    从而并入标准形式。

![](img/9c2727a5d618369bacc560c3a18c9f53_104.png)

2.  **含绝对值的目标函数**：问题 $\text{minimize} |\mathbf{c}^T \mathbf{x}| \ \text{s.t.} \ A\mathbf{x} \le \mathbf{b}$ 可以通过引入辅助变量 $u, v \ge 0$ 来转化。令：
    $$ \mathbf{c}^T \mathbf{x} = u - v, \quad |\mathbf{c}^T \mathbf{x}| = u + v $$
    则原问题转化为：
    $$
    \begin{aligned}
    & \text{minimize} \quad u + v \\
    & \text{subject to} \quad A\mathbf{x} \le \mathbf{b}, \\
    & \quad \quad \quad \quad \ \mathbf{c}^T \mathbf{x} = u - v, \\
    & \quad \quad \quad \quad \ u \ge 0, v \ge 0
    \end{aligned}
    $$
    这是一个关于变量 $(\mathbf{x}, u, v)$ 的线性规划。

---

![](img/9c2727a5d618369bacc560c3a18c9f53_106.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_108.png)

## 线性规划在分类问题中的应用 🤖

![](img/9c2727a5d618369bacc560c3a18c9f53_110.png)

现在，我们来看线性规划如何应用于机器学习中的分类问题，这与我们上节课的感知机模型密切相关。

### 线性可分情况下的感知机

![](img/9c2727a5d618369bacc560c3a18c9f53_112.png)

回顾感知机模型：给定数据点 $(\mathbf{x}_i, y_i)$，其中 $y_i \in \{+1, -1\}$，目标是找到一个权重向量 $\mathbf{w}$，使得对所有 $i$ 有 $y_i (\mathbf{w}^T \mathbf{x}_i) > 0$。
这等价于寻找 $\mathbf{w}$，使得 $y_i (\mathbf{w}^T \mathbf{x}_i) \ge 1$ 对所有 $i$ 成立（通过缩放 $\mathbf{w}$ 总可以做到）。
我们可以将其表述为一个**可行性线性规划问题**（没有目标函数，只有约束）：
$$
\text{Find } \mathbf{w} \quad \text{subject to} \quad y_i (\mathbf{x}_i^T \mathbf{w}) \ge 1, \quad \forall i=1,...,n
$$
用矩阵形式可写为：$A\mathbf{w} \ge \mathbf{1}$，其中矩阵 **A** 的第 i 行是 $y_i \mathbf{x}_i^T$，$\mathbf{1}$ 是全1向量。调用线性规划求解器即可判断是否存在这样的 $\mathbf{w}$。

![](img/9c2727a5d618369bacc560c3a18c9f53_114.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_116.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_118.png)

### 线性不可分情况与软间隔

![](img/9c2727a5d618369bacc560c3a18c9f53_120.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_122.png)

当数据不是线性可分时，上述约束无法同时满足。我们引入**松弛变量** $\xi_i \ge 0$ 来放松约束：
$$ y_i (\mathbf{w}^T \mathbf{x}_i) \ge 1 - \xi_i, \quad \forall i $$
$\xi_i$ 衡量了第 i 个样本被错误分类的程度。为了同时尽可能正确分类并控制错误程度，我们构建一个新的优化问题：
$$
\begin{aligned}
& \text{minimize} \quad \sum_{i=1}^{n} \xi_i \\
& \text{subject to} \quad y_i (\mathbf{x}_i^T \mathbf{w}) \ge 1 - \xi_i, \quad \forall i \\
& \quad \quad \quad \quad \ \xi_i \ge 0, \quad \forall i
\end{aligned}
$$
这仍然是一个**线性规划问题**，其决策变量是 $(\mathbf{w}, \xi_1, ..., \xi_n)$。通过求解这个问题，我们可以在数据线性不可分时，找到一个“最好”的分类超平面，即犯错误最小的那个。这构成了**支持向量机（SVM）** 思想的雏形。

![](img/9c2727a5d618369bacc560c3a18c9f53_124.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_126.png)

---

![](img/9c2727a5d618369bacc560c3a18c9f53_128.png)

## 总结 📝

![](img/9c2727a5d618369bacc560c3a18c9f53_130.png)

![](img/9c2727a5d618369bacc560c3a18c9f53_132.png)

本节课中我们一起学习了以下内容：
1.  **线性代数复习**：回顾了矩阵、向量、线性映射、特征值、内积和二次型等核心概念，这些是理解机器学习模型的数学基础。
2.  **优化问题简介**：区分了无约束优化和约束优化，并指出机器学习核心是求解优化问题。
3.  **线性规划**：学习了线性规划的标准形式、几何意义（凸集与顶点最优解）以及如何将一些非标准问题（如含等式约束或绝对值）转化为线性规划。
4.  **应用实例**：探讨了线性规划如何用于解决分类问题，包括线性可分情况下的感知机可行性问题，以及线性不可分情况下通过引入松弛变量构建的“软间隔”分类器。

![](img/9c2727a5d618369bacc560c3a18c9f53_134.png)

线性规划是优化理论中最基本、应用最广泛的工具之一。掌握其基本思想，为我们后续学习更复杂的优化模型（如二次规划）以及在机器学习中的深入应用打下了坚实的基础。