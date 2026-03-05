# 量化交易与Python金融分析实战：P87：强对偶、弱对偶、对偶间隙、Slater条件与弱Slater条件

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_0.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_2.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_3.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_4.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_5.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_6.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_7.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_9.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_11.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_13.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_15.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_17.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_18.png)

在本节课中，我们将深入学习优化理论中的核心概念——对偶性。我们将重点探讨什么是强对偶与弱对偶，如何理解对偶间隙，以及在什么条件下可以保证强对偶成立（即Slater条件及其弱化形式）。掌握这些知识对于理解后续的算法（如KKT条件）至关重要。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_19.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_20.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_22.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_24.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_26.png)

## 回顾：原问题、拉格朗日函数与对偶问题

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_28.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_29.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_31.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_33.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_35.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_37.png)

上一节我们介绍了如何为一个优化问题构造拉格朗日函数和对偶问题。本节中，我们来看看这些概念之间的关系和重要性质。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_39.png)

首先，考虑一个任意的优化问题（可能是凸问题，也可能是非凸问题），我们称之为**原问题**。原问题有其定义域 **D** 和最优值 **P***。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_41.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_43.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_44.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_45.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_46.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_48.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_50.png)

以下是几个核心概念的定义：

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_52.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_54.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_56.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_58.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_60.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_62.png)

*   **拉格朗日函数 (Lagrangian Function)**：对于包含不等式约束 `f_i(x) ≤ 0` 和等式约束 `h_i(x) = 0` 的原问题，其拉格朗日函数引入对偶变量 **λ** (对应不等式) 和 **ν** (对应等式)：
    `L(x, λ, ν) = f_0(x) + Σ_{i=1}^m λ_i f_i(x) + Σ_{i=1}^p ν_i h_i(x)`
    其中，要求 **λ ≥ 0**。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_64.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_66.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_68.png)

*   **对偶函数 (Dual Function)**：对偶函数 **g(λ, ν)** 是拉格朗日函数关于原变量 **x** 在其定义域 **D** 上的下确界（最小值）：
    `g(λ, ν) = inf_{x ∈ D} L(x, λ, ν)`

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_70.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_72.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_73.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_75.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_77.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_79.png)

*   **对偶问题 (Dual Problem)**：对偶问题是关于对偶变量 **λ, ν** 最大化对偶函数，并附带 **λ ≥ 0** 的约束：
    `maximize g(λ, ν) subject to λ ≥ 0`
    对偶问题的最优值记为 **d***。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_80.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_82.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_84.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_85.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_87.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_89.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_91.png)

## 弱对偶与强对偶

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_93.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_95.png)

基于上述定义，我们可以引出两个关键性质。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_97.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_99.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_101.png)

第一个重要性质是：**对偶问题一定是一个凸优化问题**。这是因为对偶函数 **g(λ, ν)** 关于 **(λ, ν)** 是一个凹函数，最大化一个凹函数（在凸集上）构成一个凸问题。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_103.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_105.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_107.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_109.png)

第二个重要性质是所谓的**弱对偶性**：对于任意优化问题（只要原问题和对偶问题都有解），其原问题最优值和对偶问题最优值一定满足 **d* ≤ P***。也就是说，对偶问题的最优值 **d*** 为原问题的最优值 **P*** 提供了一个**下界**。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_111.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_112.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_113.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_115.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_117.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_119.png)

一个很自然的问题是：**在什么情况下这个下界是紧的，即 d* = P* 成立？** 这就引出了**强对偶**的概念。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_120.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_121.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_122.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_123.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_125.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_127.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_129.png)

*   **弱对偶 (Weak Duality)**：`d* ≤ P*` 恒成立。
*   **强对偶 (Strong Duality)**：`d* = P*` 成立。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_131.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_133.png)

我们定义 **P* - d*** 为**对偶间隙 (Duality Gap)**。强对偶成立等价于对偶间隙为零。

## 保证强对偶的条件：Slater条件

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_135.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_136.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_137.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_138.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_139.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_141.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_143.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_145.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_147.png)

那么，什么情况下强对偶成立呢？对于凸优化问题，有一个著名的充分条件——**Slater条件**。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_149.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_151.png)

在介绍Slater条件前，需要先理解一个几何概念：**相对内部 (Relative Interior)**。一个集合 **D** 的相对内部 `relint(D)`，直观上可以理解为去掉 **D** 的“边界”后剩下的内部点集。对于开集，其相对内部就是自身。

**Slater条件** 是针对标准形式的凸优化问题而言的：
`minimize f_0(x) subject to f_i(x) ≤ 0, i=1,...,m, Ax = b`
其中 `f_0, f_1, ..., f_m` 是凸函数，等式约束是仿射的。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_153.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_154.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_156.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_158.png)

Slater条件表述为：**如果存在一个点 x ∈ relint(D)**（即在原问题定义域的相对内部），使得该点满足：
1.  `Ax = b` （严格满足所有等式约束）
2.  `f_i(x) < 0, for i=1,...,m` （严格满足所有不等式约束，即取严格小于号）

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_160.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_162.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_164.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_166.png)

那么，强对偶成立，即 `P* = d*`。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_168.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_169.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_170.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_171.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_173.png)

**重要说明**：
1.  Slater条件是一个**充分条件**，而非充要条件。满足它则强对偶一定成立；但不满足时，强对偶也可能成立。
2.  对于许多常见的凸问题（如线性规划、二次规划），Slater条件通常是满足的。
3.  该条件要求不等式约束取严格不等号，这在验证时可能较难。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_175.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_176.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_177.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_178.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_179.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_181.png)

## 弱Slater条件

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_183.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_185.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_187.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_188.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_189.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_191.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_193.png)

由于严格的Slater条件有时验证起来不方便，对于约束形式更特殊的问题，有一个更易满足的**弱Slater条件**。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_195.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_196.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_197.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_198.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_199.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_200.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_201.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_203.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_204.png)

如果凸优化问题中的**不等式约束函数 `f_i(x)` 都是仿射的**（即问题形式为：凸目标函数 + 线性不等式约束 + 线性等式约束），那么只要该问题是可行的（至少存在一个可行解），**强对偶就一定成立**。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_206.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_208.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_209.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_210.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_211.png)

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_212.png)

这个弱条件直接导出了一个重要推论：**对于任何可行的线性规划问题，其强对偶总是成立**。这是线性规划对偶理论的基础。

![](img/25c1a38c0ec8d9b40f8bdd66fc807b55_213.png)

## 实例分析

让我们通过两个例子来加深理解。

**例1：等式约束二次规划**
考虑问题：`minimize (1/2)x^T P x + q^T x + r subject to Ax = b`，其中 `P ≻ 0`（正定）。
根据弱Slater条件，只要问题可行（存在x使Ax=b），强对偶就成立。其拉格朗日函数为 `L(x, ν) = (1/2)x^T P x + q^T x + r + ν^T (Ax - b)`。通过对x求导并令其为零，可以推导出其对偶问题也是一个（无约束的）二次函数最大化问题，且两者的最优值相等。

**例2：二次约束二次规划**
考虑问题：`minimize (1/2)x^T P_0 x + q_0^T x + r_0 subject to (1/2)x^T P_i x + q_i^T x + r_i ≤ 0, i=1,...,m`，其中 `P_0 ≻ 0`, `P_i ≽ 0`。
其拉格朗日函数为 `L(x, λ) = (1/2)x^T (P_0 + Σ λ_i P_i)x + (q_0 + Σ λ_i q_i)^T x + (r_0 + Σ λ_i r_i)`。
当 `λ ≥ 0` 时，括号内的矩阵是正定的，对偶函数 `g(λ)` 可以通过对x求极小值（求解一个线性方程组）得到，结果是一个关于λ的凹函数。虽然验证其是否满足严格的Slater条件（即找到x使所有二次约束都严格小于0）可能较难，但对于许多此类问题，强对偶依然成立。例如，在最简单的情况下，如果所有 `q_i=0, r_i=0`，且 `P_i` 半正定，那么唯一可行解（也是最优解）是 `x=0`，此时原问题最优值 `P* = 0`。可以验证，其对偶问题的最优值 `d*` 也为0，强对偶成立。这说明即使不满足Slater条件，强对偶也可能成立。

## 总结

本节课中，我们一起学习了优化理论中对偶性的核心概念：
1.  **弱对偶** (`d* ≤ P*`) 对于任何优化问题普遍成立，对偶问题的最优值提供了原问题最优值的一个下界。
2.  **强对偶** (`d* = P*`) 是我们希望达到的理想情况，意味着对偶间隙为零。
3.  **Slater条件** 是保证凸优化问题强对偶成立的一个充分条件，它要求存在一个相对内部的点使所有不等式约束严格成立。
4.  **弱Slater条件** 是Slater条件的一个特例，对于不等式约束为仿射的凸问题，只要可行，则强对偶必然成立。这直接解释了线性规划强对偶总是成立的原因。
理解这些概念是运用对偶方法分析问题和设计算法（如接下来的KKT条件）的基石。