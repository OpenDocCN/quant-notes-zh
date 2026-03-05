# Python与量化交易：P27：偏微分方程数值解 📈

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_1.png)

在本节课中，我们将学习如何使用Python求解偏微分方程，并重点探讨其在金融期权定价中的应用。我们将从常微分方程的数值解法入手，逐步过渡到偏微分方程，最终实现布莱克-舒尔斯期权定价模型的数值求解。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_3.png)

## 课程大纲 📋

*   **常微分方程数值解**：介绍向前欧拉、向后欧拉和克兰克-尼科尔森三种方法。
*   **偏微分方程数值解**：将ODE方法推广到PDE，介绍显式、隐式和CN方法。
*   **期权基础**：简要介绍期权定义、收益函数和定价公式。
*   **期权定价实践**：使用Python实现PDE方法为欧式期权和障碍期权定价，并分析解的稳定性。

---

## 常微分方程数值解 🔢

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_5.png)

上一节我们介绍了课程的整体框架，本节中我们来看看常微分方程的数值解法。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_7.png)

常微分方程的一般形式包含微分方程和初值条件：
*   **微分形式**：`Y' = f(t, Y)`
*   **初值条件**：`Y(t0) = Y0`

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_9.png)

在理想情况下，若 `f(t, Y)` 可分离变量（即 `f(t, Y) = g(t) * h(Y)`），则可通过积分求得解析解。但大多数情况下难以求得解析解，因此需要数值方法。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_11.png)

### 有限差分法简介

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_13.png)

在讨论数值方法之前，我们需要确认解的存在性。皮卡德定理指出，如果函数 `f(t, Y)` 在初值点附近连续且满足利普希茨条件，则ODE在该区域存在唯一解。大多数金融模型都满足此条件。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_15.png)

由于解析解难以获得，我们使用数值方法求解。本节课主要讨论**有限差分法**。其核心思想是：
1.  生成解所在区域的离散网格。
2.  用差分近似替代方程中的导数。
3.  将微分方程转化为代数方程组。
4.  利用计算机求解该代数方程组。

有限差分法的优点是每一步的近似都是可见的，便于直观分析和进行敏感性检验。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_17.png)

以下是三种核心的有限差分方法：

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_19.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_21.png)

### 三种核心差分方法

1.  **向前欧拉法**
    *   **公式**：`Y_{n+1} = Y_n + h * f(t_n, Y_n)`
    *   **特点**：显式方法，计算简便，只需用到前一步的信息。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_23.png)

2.  **向后欧拉法**
    *   **公式**：`Y_{n+1} = Y_n + h * f(t_{n+1}, Y_{n+1})`
    *   **特点**：隐式方法，每一步需要解方程，稳定性优于显式方法。

3.  **克兰克-尼科尔森法**
    *   **公式**：`Y_{n+1} = Y_n + (h/2) * [f(t_n, Y_n) + f(t_{n+1}, Y_{n+1})]`
    *   **特点**：隐式方法，是向前和向后方法的折中，精度更高。

### 方法的稳定性与收敛性

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_25.png)

解出数值解后，我们还需关心解的**稳定性**（对微小扰动的敏感度）和**收敛性**（当步长趋近于0时，数值解是否趋近于真实解）。

*   **截断误差**：衡量数值解与精确解在单步内的偏差。阶数越高，精度越高。
*   **一致性**：当步长 `h` 趋于0时，截断误差也趋于0。向前/向后欧拉法具有二阶一致性，CN法具有三阶一致性。
*   **稳定性**：保证误差不会在迭代中无限放大。
    *   **向前欧拉法**：条件稳定。例如，对于方程 `Y' = λY`，要求 `|1 + λh| ≤ 1`。
    *   **向后欧拉法与CN法**：无条件稳定（绝对稳定），对步长 `h` 要求更宽松。
*   **收敛性**：一个数值方法如果同时满足一致性和稳定性，那么它就是收敛的。

另一种高精度方法是**龙格-库塔法**，其经典四阶格式精度最高，但通常也是条件稳定的。

### Python求解ODE示例

了解了理论，我们通过Python代码实践。以下是两个经典系统的求解示例。

**1. 范德波尔振荡器**
这是一个二元方程组。
```python
import numpy as np
from scipy.integrate import odeint
import matplotlib.pyplot as plt

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_27.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_29.png)

def vanderpol(Y, t, mu):
    x, y = Y
    dYdt = [y, mu*(1 - x**2)*y - x]
    return dYdt

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_31.png)

# 参数与初值
mu = 1.0
Y0 = [2.0, 0.0]
t = np.linspace(0, 30, 10000)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_33.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_35.png)

# 求解
sol = odeint(vanderpol, Y0, t, args=(mu,))

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_37.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_39.png)

# 绘图
plt.plot(t, sol[:, 0], label='x(t)')
plt.plot(t, sol[:, 1], label='y(t)')
plt.legend()
plt.xlabel('t')
plt.grid()
plt.show()
```

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_41.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_43.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_45.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_47.png)

**2. 洛伦兹系统**
这是一个三元混沌系统。
```python
from scipy.integrate import solve_ivp

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_48.png)

def lorenz(t, state, sigma, rho, beta):
    x, y, z = state
    dxdt = sigma * (y - x)
    dydt = x * (rho - z) - y
    dzdt = x * y - beta * z
    return [dxdt, dydt, dzdt]

# 参数与初值
sigma, rho, beta = 10.0, 28.0, 8.0/3.0
initial_state = [1.0, 1.0, 1.0]
t_span = (0, 40)

# 求解
sol = solve_ivp(lorenz, t_span, initial_state, args=(sigma, rho, beta),
                method='RK45', dense_output=True)

# 绘制三维相图
from mpl_toolkits.mplot3d import Axes3D
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
ax.plot(sol.y[0], sol.y[1], sol.y[2])
ax.set_xlabel('X')
ax.set_ylabel('Y')
ax.set_zlabel('Z')
plt.show()
```

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_50.png)

---

## 偏微分方程数值解 📐

上一节我们介绍了常微分方程的数值解法，本节中我们将其推广到更复杂的偏微分方程。

在金融中，许多过程都满足PDE，因为价格通常依赖于多个变量（如时间和资产价格）。PDE主要分为双曲型、抛物型和椭圆型。布莱克-舒尔斯方程就是一个典型的抛物型PDE。

求解PDE需要**初始条件**和**边界条件**。初始条件给出了时间起点的状态，边界条件则规定了空间变量在边界上的行为。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_52.png)

### 期权定价简介

我们求解PDE的主要目的是为期权定价。首先，简要介绍期权：
*   **期权**：赋予持有者在未来特定时间以预定价格买入或卖出标的资产的权利。
*   **看涨期权**：买入的权利。收益为 `max(S_T - K, 0)`。
*   **看跌期权**：卖出的权利。收益为 `max(K - S_T, 0)`。
*   **关键参数**：标的资产现价 `S0`，行权价 `K`，到期时间 `T`，波动率 `σ`，无风险利率 `r`，股息 `q`。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_54.png)

期权定价主要有三种方法：
1.  **解析解**：布莱克-舒尔斯公式。`C = S0 * N(d1) - K * e^{-rT} * N(d2)`
2.  **偏微分方程法**：求解布莱克-舒尔斯方程。`∂V/∂t + (1/2)σ²S²(∂²V/∂S²) + rS(∂V/∂S) - rV = 0`
3.  **蒙特卡洛模拟法**：模拟资产价格路径，计算收益的贴现期望。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_56.png)

### PDE有限差分法求解期权价格

我们重点介绍第二种方法。布莱克-舒尔斯方程是一个抛物型PDE，需要设定边界条件（如资产价格极高或极低时期权的价值）。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_58.png)

我们使用有限差分法，在时间和资产价格维度上分别离散化，步长为 `Δt` 和 `ΔS`。

**1. 差分近似**
*   一阶偏导：`∂V/∂S ≈ (V_{i+1, k} - V_{i, k}) / ΔS`
*   二阶偏导：`∂²V/∂S² ≈ (V_{i+1, k} - 2V_{i, k} + V_{i-1, k}) / (ΔS)²`
*   时间偏导（向前）：`∂V/∂t ≈ (V_{i, k+1} - V_{i, k}) / Δt`

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_60.png)

**2. 三种PDE解法**
    *   **显式差分**：将时间偏导用向前差分，其他项用 `k` 时刻值。可直接递推求解，但稳定性有条件限制。
    *   **隐式差分**：将时间偏导用向后差分，其他项用 `k+1` 时刻值。需要解线性方程组，但无条件稳定。
    *   **CN方法**：时间偏导用中心差分，其他项取 `k` 和 `k+1` 时刻的平均。需要解方程组，精度高且无条件稳定。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_62.png)

**3. 稳定性分析**
    *   **显式方法**：条件稳定。要求 `Δt ≤ (ΔS)² / (σ² * S_max²)`。
    *   **隐式与CN方法**：无条件稳定。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_64.png)

### Python实现PDE期权定价

以下是用显式差分法求解欧式看涨期权的简化示例。隐式和CN方法的实现类似，但需要求解线性方程组（例如使用 `np.linalg.solve`）。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_66.png)

```python
import numpy as np
import matplotlib.pyplot as plt

def explicit_fd_european_call(S_max, T, K, r, sigma, M, N):
    """
    显式有限差分法求解欧式看涨期权
    S_max: 资产价格上限
    T: 到期时间
    K: 行权价
    r: 无风险利率
    sigma: 波动率
    M: 时间步数
    N: 资产价格步数
    """
    dt = T / M
    dS = S_max / N
    S_values = np.linspace(0, S_max, N+1)
    t_values = np.linspace(0, T, M+1)

    # 初始化网格
    V = np.zeros((N+1, M+1))

    # 边界条件
    V[:, -1] = np.maximum(S_values - K, 0)  # 到期时刻收益
    V[0, :] = 0  # 资产价格为0时，期权价值为0
    V[-1, :] = S_max - K * np.exp(-r * (T - t_values))  # 资产价格极高时，近似于远期合约

    # 显式差分迭代 (从到期向前回溯)
    for j in range(M-1, -1, -1):
        for i in range(1, N):
            delta = (V[i+1, j+1] - V[i-1, j+1]) / (2 * dS)
            gamma = (V[i+1, j+1] - 2*V[i, j+1] + V[i-1, j+1]) / (dS**2)
            theta = 0.5 * sigma**2 * S_values[i]**2 * gamma + r * S_values[i] * delta - r * V[i, j+1]
            V[i, j] = V[i, j+1] - dt * theta

    return S_values, t_values, V

# 参数设置
S_max = 200
T = 1
K = 100
r = 0.05
sigma = 0.2
M = 2000  # 时间步数
N = 200   # 价格步数

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_68.png)

S, t, V = explicit_fd_european_call(S_max, T, K, r, sigma, M, N)

# 绘制期权价格曲面
fig = plt.figure(figsize=(12, 6))
ax = fig.add_subplot(111, projection='3d')
S_grid, t_grid = np.meshgrid(t, S)
ax.plot_surface(S_grid, t_grid, V, cmap='viridis')
ax.set_xlabel('Time to Maturity')
ax.set_ylabel('Asset Price')
ax.set_zlabel('Option Price')
ax.set_title('European Call Option Price Surface (Explicit FD)')
plt.show()

# 提取当前时刻(t=0)的期权价格曲线
option_price_t0 = V[:, 0]
plt.figure(figsize=(10, 6))
plt.plot(S, option_price_t0, label='Explicit FD Price')
plt.axvline(x=K, color='r', linestyle='--', label='Strike Price (K=100)')
plt.xlabel('Asset Price')
plt.ylabel('Option Price at t=0')
plt.title('European Call Option Price vs. Asset Price')
plt.legend()
plt.grid(True)
plt.show()
```

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_70.png)

对于**障碍期权**（如向上敲出看涨期权），只需修改到期收益和边界条件即可。在障碍价格处，期权价值设为0（敲出）。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_72.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_74.png)

**隐式差分和CN方法**的实现代码结构类似，但核心迭代步骤需要构造并求解一个三对角线性方程组，这能有效避免显式方法可能出现的数值不稳定现象，特别是在步长选择不当时。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_76.png)

---

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_78.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_80.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_82.png)

## 总结 🎯

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_84.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_86.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_88.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_90.png)

本节课我们一起学习了微分方程的数值解法及其在金融中的应用。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_92.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_94.png)

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_96.png)

1.  **常微分方程**：我们介绍了向前欧拉、向后欧拉和克兰克-尼科尔森三种有限差分方法，分析了它们的一致性、稳定性和收敛性，并通过Python求解了范德波尔和洛伦兹系统。
2.  **偏微分方程**：我们将ODE方法推广到PDE，重点介绍了显式、隐式和CN方法。这些方法是求解金融工程中核心的布莱克-舒尔斯方程的有力工具。
3.  **期权定价实践**：我们阐述了期权的基本概念，并详细讲解了如何使用PDE有限差分法为欧式期权和障碍期权定价。通过Python代码示例，我们对比了显式方法（可能不稳定）和隐式/CN方法（无条件稳定）的差异。

![](img/68a20b0ca2f103dc1b6c3e541e138f4c_98.png)

掌握PDE数值解法是进行复杂金融衍生品定价和风险管理的基础。在接下来的课程中，我们将学习另一种强大的定价工具——蒙特卡洛模拟法。