# 量化交易与Python金融分析实战：P67：8.8.凸集的交集与保凸运算（2）

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_1.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_3.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_5.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_7.png)

在本节课中，我们将继续学习凸集的性质，重点探讨几种重要的保凸运算，包括透视函数、线性分数函数及其在概率论中的应用。这些运算能确保凸集在经过特定变换后，其凸性得以保持。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_9.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_11.png)

## 透视函数及其保凸性

上一节我们介绍了仿射变换的保凸性，本节中我们来看看另一种重要的映射——透视函数。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_13.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_15.png)

透视函数是一个从 **R^(n+1)** 维空间映射到 **R^n** 维空间的函数，其本质是降维操作。首先，我们定义其定义域。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_17.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_19.png)

**定义域**：函数中变量的取值范围。透视函数的定义域并非整个 **R^(n+1)** 空间，而是其子集，记作 **dom P**。具体来说，对于变量 **(z, t)**，其中 **z ∈ R^n**，我们要求最后一个元素 **t** 必须是一个正数。用公式表示为：
```
dom P = { (z, t) | z ∈ R^n, t > 0 }
```

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_21.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_23.png)

**透视函数定义**：给定一个向量 **(z, t)**，其中 **t > 0**，透视函数 **P** 将其映射为：
```
P(z, t) = z / t
```
其含义是将前 **n** 个元素 **z** 中的每一个都除以最后一个元素 **t**，然后丢弃 **t**，从而得到一个 **n** 维向量。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_25.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_27.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_28.png)

**几何解释**：透视函数名称来源于类似针孔成像的几何解释。考虑二维情况，点 **(x1, x2)** 且 **x2 > 0**。连接该点与原点画一条直线，该直线与直线 **x2 = 1** 的交点为 **(-x1/x2, 1)**。该点的横坐标 **-x1/x2** 正是透视函数 **P(x1, x2)** 取负号的结果。因此，透过原点“观察”一个点，得到的投影点坐标即与透视函数相关。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_29.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_30.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_31.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_32.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_34.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_36.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_38.png)

**重要结论**：任意凸集经过透视函数映射后，得到的像集仍然是凸集。直观理解是，透过一个针孔相机观察一个凸体，看到的像仍然是一个凸体。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_40.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_42.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_44.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_45.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_46.png)

## 线段经透视映射的证明

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_48.png)

为了深入理解，我们证明一个具体例子：**R^(n+1)** 空间中的一个线段，经过透视函数映射后，在 **R^n** 空间中仍然是一个线段。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_50.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_52.png)

考虑线段上的两点 **X = (x, x_{n+1})** 和 **Y = (y, y_{n+1})**，其中 **x, y ∈ R^n**，且 **x_{n+1} > 0, y_{n+1} > 0**。该线段可表示为：
```
θX + (1-θ)Y, 其中 θ ∈ [0, 1]
```
现在，我们考察该线段上一点经过透视映射后的结果：
```
P(θX + (1-θ)Y) = (θx + (1-θ)y) / (θ x_{n+1} + (1-θ) y_{n+1})
```
我们进行如下代数变换：
1.  令 **μ = (θ x_{n+1}) / (θ x_{n+1} + (1-θ) y_{n+1})**。由于 **x_{n+1}, y_{n+1} > 0** 且 **θ ∈ [0,1]**，易知 **μ ∈ [0, 1]**。
2.  则 **1 - μ = ((1-θ) y_{n+1}) / (θ x_{n+1} + (1-θ) y_{n+1})**。
3.  将原式分子分母同除以 **(θ x_{n+1} + (1-θ) y_{n+1})**，可得：
    ```
    P(θX + (1-θ)Y) = μ * (x / x_{n+1}) + (1-μ) * (y / y_{n+1})
    ```
4.  注意到 **P(X) = x / x_{n+1}**，**P(Y) = y / y_{n+1}**。
5.  因此，**P(θX + (1-θ)Y) = μ * P(X) + (1-μ) * P(Y)**。

上述推导表明，原线段上任意一点（由 **θ** 参数化）的透视像，可以表示为像空间两点 **P(X)** 与 **P(Y)** 的凸组合（由 **μ** 参数化）。并且，**θ** 与 **μ** 之间存在一一对应的连续映射关系（例如，当 **x_{n+1} = y_{n+1}** 时，**μ = θ**；否则为单调连续函数）。因此，当 **θ** 在 **[0,1]** 区间连续变化时，**μ** 也在 **[0,1]** 区间连续变化，其像点构成了 **P(X)** 与 **P(Y)** 之间的连续线段。这就证明了线段经透视映射后仍为线段。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_54.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_56.png)

## 反透视映射的保凸性

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_58.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_60.png)

与仿射映射类似，我们也可以考虑透视函数的逆映射（反透视映射）是否保凸。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_62.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_63.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_64.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_65.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_67.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_69.png)

给定一个 **R^n** 空间中的集合 **C**，其反透视映射 **P^{-1}(C)** 定义为 **R^(n+1)** 空间中的如下集合：
```
P^{-1}(C) = { (x, t) | x/t ∈ C, t > 0 }
```
这里我们要求 **t > 0**。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_71.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_73.png)

**结论**：如果 **C** 是凸集，那么其反透视映射 **P^{-1}(C)** 也是凸集。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_75.png)

**证明思路**：
1.  在 **P^{-1}(C)** 中任取两点 **(x, t)** 和 **(y, s)**，根据定义有 **x/t ∈ C**，**y/s ∈ C**。
2.  取 **θ ∈ [0, 1]**，构造凸组合：**θ(x, t) + (1-θ)(y, s) = (θx+(1-θ)y, θt+(1-θ)s)**。
3.  我们需要证明这个新点属于 **P^{-1}(C)**，即证明 **(θx+(1-θ)y) / (θt+(1-θ)s) ∈ C**。
4.  仿照前面的技巧，令 **μ = (θt) / (θt+(1-θ)s)**，则 **1-μ = ((1-θ)s) / (θt+(1-θ)s)**。
5.  那么，
    ```
    (θx+(1-θ)y) / (θt+(1-θ)s) = μ*(x/t) + (1-μ)*(y/s)
    ```
6.  由于 **x/t** 和 **y/s** 都属于凸集 **C**，它们的凸组合 **μ*(x/t) + (1-μ)*(y/s)** 也必然属于 **C**。
7.  因此，凸组合点 **(θx+(1-θ)y, θt+(1-θ)s)** 满足反透视映射的定义，属于 **P^{-1}(C)**。证毕。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_76.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_78.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_80.png)

## 线性分数函数

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_82.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_84.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_85.png)

将仿射映射与透视映射复合，我们得到一类更广泛且重要的函数——线性分数函数（或线性分式函数）。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_86.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_87.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_89.png)

以下是其构造步骤：
1.  **仿射映射**：首先定义一个从 **R^n** 到 **R^(m+1)** 的仿射映射 **g**：
    ```
    g(x) = [A; c^T] x + [b; d] = [Ax + b; c^T x + d]
    ```
    其中 **A** 是 **m×n** 矩阵，**b** 是 **m** 维向量，**c** 是 **n** 维向量，**d** 是标量。
2.  **透视映射**：然后应用之前定义的透视函数 **P**，将 **R^(m+1)** 映射到 **R^m**。
3.  **复合**：线性分数函数 **f** 定义为这两个映射的复合：
    ```
    f = P ∘ g
    ```
    即 **f(x) = P(g(x))**。
4.  **具体形式**：将定义代入，得到线性分数函数的显式表达式：
    ```
    f(x) = (Ax + b) / (c^T x + d)
    ```
    **定义域**：要求 **dom f = { x | c^T x + d > 0 }**，以保证透视函数定义有效。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_91.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_92.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_94.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_96.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_97.png)

**重要性质**：线性分数函数是保凸的。任意凸集经过线性分数函数映射后，其像集仍是凸集。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_98.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_100.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_102.png)

**理解**：虽然 **f(x)** 的形式是非线性的（分子分母都是线性函数），但其保凸性很容易理解。因为凸集 **C** 先经过保凸的仿射映射 **g**，得到凸集 **g(C)**；再经过保凸的透视映射 **P**，最终结果 **P(g(C))** 自然是凸集。线性分数函数在优化和许多实际问题中经常出现，它是一个性质良好的非线性函数。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_103.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_104.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_105.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_106.png)

## 在概率论中的应用实例

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_108.png)

最后，我们看一个线性分数函数在概率论中的具体应用：从联合概率分布到条件概率分布的映射。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_110.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_112.png)

考虑两个离散随机变量 **U** 和 **V**，**U** 取值 **{1,...,n}**，**V** 取值 **{1,...,m}**。它们的联合概率分布由矩阵 **P** 表示，其中元素：
```
p_ij = Prob(U=i, V=j)
```
在给定 **V=j** 的条件下，**U** 的条件概率分布为：
```
f_ij = Prob(U=i | V=j)
```
根据条件概率公式，两者关系为：
```
f_ij = p_ij / (∑_{k=1}^{n} p_kj)
```
对于固定的 **j**，向量 **[p_1j, p_2j, ..., p_nj]^T** 是一个 **n** 维向量（属于联合概率分布集合）。上述公式将其映射为一个标量 **f_ij**。这个映射可以看作一个线性分数函数：
*   **分子**：是取该向量第 **i** 个分量的线性操作（乘以一个仅在第 **i** 位为1的单位向量）。
*   **分母**：是该向量所有分量求和的线性操作（乘以一个全1向量）。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_113.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_114.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_116.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_118.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_120.png)

因此，从联合概率分布（的某个切片）到条件概率的映射是一个线性分数函数。一个自然的问题是：所有可能的联合概率分布构成的集合是凸集吗？（提示：考虑概率的非负性和归一化约束）。如果它是凸集，那么根据线性分数函数的保凸性，所得到的条件概率分布集合也将是凸集。这个结论在统计学和机器学习中很有意义。

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_122.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_124.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_126.png)

## 总结

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_128.png)

![](img/bfa1f8daa54e75ee60d4ba1ab35dcc8a_130.png)

本节课中我们一起学习了三种重要的保凸运算：
1.  **透视函数**：`P(z,t)=z/t`，能将高维凸集投影到低维并保持凸性。
2.  **反透视映射**：凸集的原像在高维空间中也是凸集。
3.  **线性分数函数**：`f(x)=(Ax+b)/(c^T x+d)`，作为仿射映射与透视映射的复合，它是一种性质优良的非线性保凸映射。

我们还探讨了线性分数函数在描述联合概率到条件概率映射中的应用。理解这些保凸运算，对于后续学习凸优化问题中可行域与目标函数的分析至关重要。