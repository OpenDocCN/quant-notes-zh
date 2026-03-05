# 凸优化：P85：对偶性、Lagrange函数与对偶函数

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_1.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_3.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_5.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_7.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_9.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_11.png)

在本节课中，我们将学习凸优化中一个极为核心的思想——**对偶性**。我们将从如何将带约束的优化问题转化为更易处理的形式入手，逐步引入**Lagrange函数**和**Lagrange对偶函数**的概念，并探讨它们的关键性质。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_13.png)

上节课我们主要介绍了两个概念：**半定规划问题**的含义，以及**多目标优化问题**。我们讲解多目标优化的主要目的，是为了阐明“目标与约束可以相互转化”这一重要思想。本节我们将回归到**单目标优化问题**，并深入探讨如何简化那些带有“讨厌”约束的优化问题。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_15.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_16.png)

## 从原始问题到Lagrange函数

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_18.png)

考虑一个标准的约束优化问题，我们称之为**原始问题**：

$$
\begin{aligned}
\min_{x} \quad & f_0(x) \\
\text{s.t.} \quad & f_i(x) \leq 0, \quad i = 1, \ldots, m \\
& h_i(x) = 0, \quad i = 1, \ldots, p
\end{aligned}
$$

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_20.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_21.png)

其中，$x \in \mathbb{R}^n$ 是优化变量。$f_0$ 是目标函数，$f_i$ 表示 $m$ 个不等式约束，$h_i$ 表示 $p$ 个等式约束。该问题的定义域 $D$ 是所有函数定义域的交集。我们记该问题的最优值为 $p^*$。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_23.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_25.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_27.png)

对于这样一个带约束的问题，优化起来通常比较困难。一个核心思路是：能否将这些约束以某种形式“吸收”到目标函数中，从而简化问题？这就引出了 **Lagrange函数** 的概念。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_29.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_31.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_33.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_35.png)

**Lagrange函数** $L(x, \lambda, \nu)$ 是基于上述原始问题构造的一个新函数，它引入了两组新的变量（称为Lagrange乘子）：
*   $\lambda \in \mathbb{R}^m$：对应于 $m$ 个不等式约束。
*   $\nu \in \mathbb{R}^p$：对应于 $p$ 个等式约束。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_37.png)

其构造方式如下：

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_39.png)

$$
L(x, \lambda, \nu) = f_0(x) + \sum_{i=1}^{m} \lambda_i f_i(x) + \sum_{i=1}^{p} \nu_i h_i(x)
$$

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_41.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_43.png)

**理解Lagrange函数**：
*   它保留了原始目标函数 $f_0(x)$。
*   它将每个不等式约束 $f_i(x)$ 乘以一个对应的非负乘子 $\lambda_i$ 后求和。
*   它将每个等式约束 $h_i(x)$ 乘以一个对应的乘子 $\nu_i$ 后求和。
*   本质上，**Lagrange函数是将原始目标函数和所有约束函数进行了一个线性加权组合**。对于固定的 $x$，$L$ 关于 $\lambda$ 和 $\nu$ 是线性的。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_45.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_47.png)

## Lagrange对偶函数及其性质

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_49.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_51.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_52.png)

在定义了Lagrange函数之后，我们可以进一步构造 **Lagrange对偶函数**（或简称为**对偶函数**）。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_53.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_55.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_57.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_59.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_61.png)

**对偶函数** $g(\lambda, \nu)$ 的定义如下：

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_63.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_65.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_66.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_68.png)

$$
g(\lambda, \nu) = \inf_{x \in D} L(x, \lambda, \nu)
$$

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_70.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_72.png)

**理解对偶函数**：
*   它对固定的 Lagrange 乘子 $\lambda$ 和 $\nu$，在原始变量 $x$ 的定义域 $D$ 上，寻找 Lagrange 函数 $L$ 的**下确界**（最小值）。
*   因此，$g$ 是一个**仅关于 Lagrange 乘子 $\lambda$ 和 $\nu$ 的函数**。
*   $\lambda$ 称为与不等式约束关联的 Lagrange 乘子，$\nu$ 称为与等式约束关联的 Lagrange 乘子。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_74.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_76.png)

对偶函数拥有两个极其重要的性质，无论原始问题是否为凸问题，这些性质都成立。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_78.png)

**性质一：对偶函数是凹函数**
对偶函数 $g(\lambda, \nu)$ 一定是凹函数。这是因为 $L(x, \lambda, \nu)$ 关于 $(\lambda, \nu)$ 是线性的，而一族线性函数的逐点下确界（infimum）构成一个凹函数。这是一个非常强的结论，为后续分析奠定了基础。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_80.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_81.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_82.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_84.png)

**性质二：对偶函数给出原始最优值的下界**
对于任意 $\lambda \succeq 0$（即 $\lambda_i \geq 0$）和任意 $\nu$，对偶函数的值总是小于等于原始问题的最优值 $p^*$：

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_86.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_88.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_90.png)

$$
g(\lambda, \nu) \leq p^*， \quad \forall \lambda \succeq 0, \nu
$$

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_92.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_94.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_96.png)

**证明**：设 $x^*$ 是原始问题的一个最优解（可行解）。根据可行性，有 $f_i(x^*) \leq 0$，$h_i(x^*) = 0$。那么对于任意 $\lambda \succeq 0$ 和 $\nu$，有：
$$
\begin{aligned}
L(x^*, \lambda, \nu) &= f_0(x^*) + \sum_{i=1}^{m} \lambda_i f_i(x^*) + \sum_{i=1}^{p} \nu_i h_i(x^*) \\
&\leq f_0(x^*) = p^*
\end{aligned}
$$
因为 $g(\lambda, \nu)$ 是 $L(x, \lambda, \nu)$ 在所有 $x \in D$（包括 $x^*$）上的下确界，所以必然有 $g(\lambda, \nu) \leq L(x^*, \lambda, \nu) \leq p^*$。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_98.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_100.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_102.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_104.png)

**意义**：这个性质意味着，对于任意一组非负的 $\lambda$ 和任意的 $\nu$，$g(\lambda, \nu)$ 都构成了原始最优值 $p^*$ 的一个**下界**。那么，一个自然的问题是：我们能否找到**最好的下界**，即最接近 $p^*$ 的那个下界？这就引出了在 $\lambda \succeq 0$ 的约束下**最大化对偶函数**的问题：
$$
\begin{aligned}
\max_{\lambda, \nu} \quad & g(\lambda, \nu) \\
\text{s.t.} \quad & \lambda \succeq 0
\end{aligned}
$$
由于 $g$ 是凹函数，且约束是线性的，因此**这个对偶问题是一个凸优化问题**，通常比原始问题更容易求解。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_106.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_108.png)

## 实例分析

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_110.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_112.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_113.png)

为了帮助理解上述抽象概念，我们来看几个具体例子。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_115.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_117.png)

**例1：线性等式约束的二次规划**
考虑问题：
$$
\begin{aligned}
\min_{x} \quad & x^T x \\
\text{s.t.} \quad & Ax = b
\end{aligned}
$$
*   **Lagrange函数**：$L(x, \nu) = x^T x + \nu^T (Ax - b)$
*   **对偶函数**：为求 $g(\nu) = \inf_x L(x, \nu)$，对 $x$ 求导并令其为零：$\nabla_x L = 2x + A^T \nu = 0$，得 $x = -\frac{1}{2}A^T \nu$。代入 $L$：
    $$
    g(\nu) = -\frac{1}{4} \nu^T A A^T \nu - b^T \nu
    $$
    这是一个关于 $\nu$ 的二次凹函数（因为 $-\frac{1}{4}AA^T$ 是半负定矩阵）。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_119.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_121.png)

**例2：标准形式的线性规划**
考虑问题：
$$
\begin{aligned}
\min_{x} \quad & c^T x \\
\text{s.t.} \quad & Ax = b \\
& x \succeq 0
\end{aligned}
$$
首先将约束改写为标准形式：$Ax - b = 0$，$-x \leq 0$。
*   **Lagrange函数**：$L(x, \lambda, \nu) = c^T x - \lambda^T x + \nu^T (Ax - b) = -b^T \nu + (c - \lambda + A^T \nu)^T x$
*   **对偶函数**：$g(\lambda, \nu) = \inf_x L(x, \lambda, \nu)$。这是一个关于 $x$ 的线性函数。
    *   若 $c - \lambda + A^T \nu = 0$，则 $g(\lambda, \nu) = -b^T \nu$。
    *   若 $c - \lambda + A^T \nu \neq 0$，则通过令 $x$ 趋向无穷可使 $L$ 趋向 $-\infty$，故 $g(\lambda, \nu) = -\infty$。
    因此，$g(\lambda, \nu)$ 是一个在超平面 $c - \lambda + A^T \nu = 0$ 上取有限值，在其他区域为 $-\infty$ 的凹函数。

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_123.png)

**例3：非凸问题**
考虑一个非凸问题：
$$
\begin{aligned}
\min_{x} \quad & x^T W x \\
\text{s.t.} \quad & x_i^2 = 1, \quad i = 1, \ldots, n
\end{aligned}
$$
约束 $x_i = \pm 1$ 是非凸的。
*   **Lagrange函数**：$L(x, \nu) = x^T W x + \sum_{i=1}^n \nu_i (x_i^2 - 1) = x^T (W + \text{diag}(\nu)) x - \mathbf{1}^T \nu$
*   **对偶函数**：$g(\nu) = \inf_x x^T (W + \text{diag}(\nu)) x - \mathbf{1}^T \nu$。
    这是一个关于 $x$ 的二次型。若矩阵 $Q = W + \text{diag}(\nu)$ 的特征值均非负（即 $Q \succeq 0$），则最小值为 $-\mathbf{1}^T \nu$（在 $x=0$ 处取得）。若 $Q$ 有负特征值，则最小值是 $-\infty$。
    因此，$g(\nu)$ 也是一个分片凹函数。**即使原始问题非凸，其对偶函数依然是凹函数**，这验证了对偶函数的通用性质。

## 总结

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_125.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_127.png)

![](img/0afd4fdf6c0ee233708bfe0e8b28be32_129.png)

本节课我们一起学习了凸优化中的核心概念——**对偶性**的起点。我们首先介绍了如何为带约束的原始优化问题构造 **Lagrange函数**，它通过引入Lagrange乘子将约束整合到目标中。接着，我们定义了 **Lagrange对偶函数**，它通过最小化Lagrange函数得到，并且具有两个关键性质：1) **它一定是凹函数**；2) **对于非负的乘子，它的值给出了原始问题最优值的一个下界**。这些性质为我们后续研究**对偶问题**以及**强对偶性**奠定了坚实的基础。通过几个实例，我们看到无论原始问题是凸、线性还是非凸的，其对应的对偶函数都保持着凹性，这彰显了对偶理论威力的来源。