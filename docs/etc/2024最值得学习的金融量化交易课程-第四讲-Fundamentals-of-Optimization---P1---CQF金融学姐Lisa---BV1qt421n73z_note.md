# 2024最值得学习的金融量化交易课程：第四讲：Fundamentals of Optimization（优化基础 P1）📈  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_0.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_2.png)

## 📌 课程概述  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_4.png)

在本节课中，我们将系统学习**最优化（Optimization）的基础理论**，并将其应用到**投资组合选择（Portfolio Selection）**问题中。  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_6.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_8.png)

上一讲我们介绍了现代投资组合理论（MPT）的核心思想，本节将进一步使用**微积分与矩阵运算**，通过数学方法求解最优投资组合。  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_10.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_12.png)

本节重点包括：  

- 如何将投资问题转化为数学优化问题  
- 无约束优化的求解方法  
- 均值-方差优化（Mean-Variance Optimization）的完整推导  
- 最小二乘法（OLS）中的优化思想  

最后，我们将掌握从“理论理解”到“数学求解”的完整逻辑。  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_14.png)

# 一、最优化基础概念 🔍  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_16.png)

## 1️⃣ 什么是最优化  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_18.png)

最优化的核心目标是：  

> 在给定约束条件下，找到目标函数的**最大值或最小值**。  

在金融中常见形式包括：  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_20.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_22.png)

- **利润最大化**
- **效用最大化**
- **成本最小化**
- **风险最小化**  

例如：  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_24.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_26.png)

- 寻找**最大收益组合**
- 寻找**最小风险组合**  



## 2️⃣ 最优化问题的数学表达  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_28.png)

一个标准最优化问题包含三个核心要素：  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_30.png)

以下是标准结构：  

```
min / max   f(x1, x2, ..., xn)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_32.png)

subject to:
g1(x) = 0
g2(x) ≤ 0
g3(x) ≥ 0
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_34.png)

其中：  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_36.png)

- **目标函数（Objective Function）**：`f(x)`
- **决策变量（Decision Variables）**：`x1, x2, ..., xn`
- **约束条件（Constraints）**：等式或不等式  

在投资组合问题中，常见约束为：

```
∑ wi = 1
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_38.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_40.png)

即权重之和等于1。  



## 3️⃣ 最大化与最小化的转换  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_42.png)

在实际计算中，通常统一使用**最小化（minimize）**。  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_44.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_46.png)

最大化问题可以转化为最小化问题：  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_48.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_50.png)

```
max f(x) = min [-f(x)]
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_52.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_54.png)

即在函数前加负号即可。  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_56.png)

### 仿射变换（Affine Transformation）

若目标函数为：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_58.png)

```
A + B f(x)
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_60.png)

结论如下：

- 若 `B > 0`，最大值位置不变
- 若 `B < 0`，最大值变为最小值问题

常数项 A 不影响最优解。  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_62.png)

## 4️⃣ 目标函数等于某值的问题  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_64.png)

例如隐含波动率问题：

```
f(x) = C
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_66.png)

可以转化为最小化距离：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_68.png)

```
min (f(x) - C)^2
```

即寻找使理论价格最接近市场价格的参数。  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_70.png)

# 二、无约束优化方法 📐  



上一节介绍了优化问题的基本结构，本节开始讨论**无约束优化**。  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_72.png)

## 1️⃣ 一阶导数（梯度）  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_74.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_76.png)

设：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_78.png)

```
f(x1, x2, ..., xm)
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_80.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_82.png)

梯度为：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_84.png)

```
∇f(x) = [∂f/∂x1, ∂f/∂x2, ..., ∂f/∂xm]^T
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_86.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_88.png)

必要条件（First Order Condition）：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_90.png)

```
∇f(x*) = 0
```



## 2️⃣ 二阶导数（Hessian矩阵）  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_92.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_94.png)

二阶导数为：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_96.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_97.png)

```
H(x) = ∂²f / ∂xi ∂xj
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_98.png)

这是一个对称矩阵。  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_100.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_102.png)

### 充分条件（Second Order Condition）

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_104.png)

- 若 `H(x*)` **正定** → 极小值
- 若 `H(x*)` **负定** → 极大值
- 若 `H(x*) = 0` → 无法判断



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_106.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_108.png)

## 3️⃣ 正定矩阵定义  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_110.png)

矩阵 A 为正定矩阵，当且仅当：

```
xᵀ A x > 0    (∀ x ≠ 0)
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_112.png)

协方差矩阵 Σ 是对称正定矩阵。  



## 4️⃣ 边界条件  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_114.png)

除了导数条件，还必须检查边界点。  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_116.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_118.png)

例如在区间 `[a, b]` 内求最大值，需要比较：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_120.png)

- 临界点函数值
- f(a)
- f(b)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_122.png)

最终取最大值。  



# 三、均值-方差优化（Mean-Variance Optimization）📊  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_124.png)

接下来将优化理论应用到投资组合问题。  



## 1️⃣ 基本设定  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_126.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_128.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_130.png)

假设有 N 个资产：  

- 期望收益向量：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_132.png)

```
μ = [μ1, μ2, ..., μN]^T
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_134.png)

- 权重向量：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_136.png)

```
w = [w1, w2, ..., wN]^T
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_138.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_140.png)

- 协方差矩阵：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_142.png)

```
Σ
```



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_144.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_146.png)

## 2️⃣ 组合收益与风险  

组合期望收益：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_148.png)

```
μp = μᵀ w
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_150.png)

组合方差：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_152.png)

```
σp² = wᵀ Σ w
```



## 3️⃣ 协方差矩阵构建方法  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_154.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_156.png)

若已知标准差向量 σ 与相关系数矩阵 R：  

步骤如下：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_158.png)

### 第一步：构造对角矩阵

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_160.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_162.png)

```
D = diag(σ1, σ2, ..., σN)
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_164.png)

### 第二步：计算协方差矩阵

```
Σ = D R D
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_166.png)

这在Excel或Python中均适用。  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_168.png)

## 4️⃣ 引入无风险资产  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_170.png)

设无风险利率为 `Rf`  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_172.png)

无风险权重：

```
w0 = 1 - 1ᵀ w
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_174.png)

组合收益变为：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_175.png)

```
μp = Rf + wᵀ(μ - Rf 1)
```

组合方差不变：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_177.png)

```
σp² = wᵀ Σ w
```



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_179.png)

## 5️⃣ 构建效用函数  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_181.png)

效用函数为：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_183.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_185.png)

```
U(w) = μp - (λ/2) σp²
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_187.png)

代入得：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_189.png)

```
U(w) = Rf + wᵀ(μ - Rf1) - (λ/2) wᵀΣw
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_191.png)

其中：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_193.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_195.png)

- λ > 0 为风险厌恶系数  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_197.png)

## 6️⃣ 求解最优权重  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_199.png)

### 一阶导数：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_201.png)

```
∂U/∂w = (μ - Rf1) - λ Σ w
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_203.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_205.png)

令其等于0：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_207.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_209.png)

```
(μ - Rf1) = λ Σ w*
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_211.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_213.png)

解得：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_215.png)

```
w* = (1/λ) Σ⁻¹ (μ - Rf1)
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_217.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_219.png)

### 二阶导数：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_221.png)

```
∂²U/∂w² = -λ Σ
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_223.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_225.png)

由于 Σ 为正定矩阵，λ>0，因此：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_227.png)

```
-λ Σ < 0
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_229.png)

说明该点为**最大值**。  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_231.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_232.png)

# 四、线性回归中的优化思想 📉  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_234.png)

除了投资组合，优化还广泛应用于回归分析。  



## 1️⃣ 最小二乘法（OLS）  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_236.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_238.png)

目标：

```
min Σ (yi - ŷi)^2
```

即最小化**残差平方和（RSS）**。  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_240.png)

步骤：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_242.png)

- 构造目标函数
- 求一阶导 = 0
- 解出 β 系数
- 二阶导 > 0 → 为最小值  

这与前述优化逻辑完全一致。  



## 2️⃣ 残差平方和的直观理解  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_244.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_246.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_247.png)

给定一组数据点，用一条直线拟合。  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_248.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_250.png)

不同直线产生不同误差。  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_252.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_254.png)

最佳直线满足：

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_256.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_258.png)

```
Σ(误差²) 最小
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_260.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_262.png)

因此称为**最小二乘法**。  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_264.png)

# 五、本节总结 🧾  



![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_266.png)

本节课我们系统学习了：  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_268.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_270.png)

- 最优化问题的基本结构  
- 最大化与最小化转换方法  
- 一阶与二阶条件  
- 正定矩阵概念  
- 均值-方差优化完整推导  
- 最小二乘法中的优化思想  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_272.png)

我们从理论出发，推导出均值-方差优化的核心公式：

```
w* = (1/λ) Σ⁻¹ (μ - Rf1)
```

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_274.png)

这就是无约束条件下的最优风险资产权重。  

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_276.png)

![](img/2ac91bfe3690c69dd7a6ab510d11e9d3_278.png)

下一节我们将进入**带约束条件的优化问题**，并学习拉格朗日方法。