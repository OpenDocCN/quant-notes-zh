# 量化金融随机微积分：P1：Python模拟几何布朗运动 🚀

![](img/04f1e84acd2f9ec005b75361565f390e_1.png)

在本节课中，我们将学习如何使用Python模拟几何布朗运动。几何布朗运动是金融领域，特别是期权定价理论中一个非常基础的随机过程模型。我们将通过代码实践，直观地理解其模拟过程。

## 概述

![](img/04f1e84acd2f9ec005b75361565f390e_3.png)

几何布朗运动常用于描述股票价格等金融资产的随机演化。其动力学由一个随机微分方程定义。在本教程中，我们不深入推导其数学原理，而是专注于在Python中实现其模拟过程。如果你想了解从微积分到随机积分的完整推导，请关注后续教程。

## 模型定义

几何布朗运动的随机微分方程形式如下：

![](img/04f1e84acd2f9ec005b75361565f390e_5.png)

**dS_t = μ S_t dt + σ S_t dW_t**

![](img/04f1e84acd2f9ec005b75361565f390e_7.png)

其中：
*   **S_t** 代表在时间 t 的资产价格。
*   **μ** 是漂移系数（平均回报率）。
*   **σ** 是波动率系数。
*   **W_t** 是标准布朗运动。

![](img/04f1e84acd2f9ec005b75361565f390e_9.png)

通过对上述方程求解，我们可以得到其显式解（积分形式）：

![](img/04f1e84acd2f9ec005b75361565f390e_11.png)

**S_t = S_0 exp( (μ - σ²/2) t + σ W_t )**

这个公式表明，股票价格的对数 `ln(S_t)` 服从正态分布。

![](img/04f1e84acd2f9ec005b75361565f390e_13.png)

![](img/04f1e84acd2f9ec005b75361565f390e_15.png)

## 参数设置与模拟步骤

现在，我们开始编写代码。首先导入必要的库。

```python
import numpy as np
import matplotlib.pyplot as plt
```

接下来，定义模拟所需的参数。

```python
# 模型参数
mu = 0.10        # 漂移系数，年化10%
sigma = 0.30     # 波动率，年化30%
S0 = 100         # 初始股价
T = 1.0          # 模拟总时长（年）
n = 100          # 时间步数
M = 100          # 模拟路径数

# 计算单步时间增量
dt = T / n
```

参数定义完成后，我们进入核心的模拟环节。我们将使用向量化操作一次性生成所有路径，以提高效率。

```python
# 模拟随机过程
# 生成随机增量：形状为 (M, n) 的正态分布随机数
random_increments = np.random.normal(0, np.sqrt(dt), size=(M, n))

# 计算每步的对数收益率：漂移项 + 随机项
drift = (mu - 0.5 * sigma**2) * dt
volatility_component = sigma * random_increments
log_returns = drift + volatility_component

![](img/04f1e84acd2f9ec005b75361565f390e_17.png)

# 构建包含初始值1的数组，并计算累积乘积（即价格路径）
# 首先将初始值1与各步收益率堆叠
log_returns_with_initial = np.vstack([np.ones(M), log_returns.T]).T  # 转置以方便操作
# 计算累积乘积，得到每条路径随时间的乘数因子
price_factors = np.cumprod(log_returns_with_initial, axis=1)

![](img/04f1e84acd2f9ec005b75361565f390e_19.png)

# 将乘数因子乘以初始股价，得到最终的价格路径
St = S0 * price_factors
```

![](img/04f1e84acd2f9ec005b75361565f390e_21.png)

## 结果可视化

![](img/04f1e84acd2f9ec005b75361565f390e_23.png)

模拟完成后，我们需要生成对应的时间轴并将价格路径可视化。

以下是创建时间轴和绘图的步骤：

![](img/04f1e84acd2f9ec005b75361565f390e_25.png)

![](img/04f1e84acd2f9ec005b75361565f390e_27.png)

```python
# 创建时间点数组
time_points = np.linspace(0, T, n+1)

# 为了绘图，需要将时间数组扩展为与价格路径数组St相同的形状
# 使用np.tile函数进行扩展
time_array = np.tile(time_points, (M, 1))

![](img/04f1e84acd2f9ec005b75361565f390e_29.png)

# 绘制所有模拟路径
plt.figure(figsize=(10, 6))
plt.plot(time_array.T, St.T, lw=0.8, alpha=0.7)
plt.xlabel('时间 (年)')
plt.ylabel('股价')
plt.title(f'几何布朗运动模拟 (μ={mu}, σ={sigma})')
plt.grid(True, alpha=0.3)
plt.show()
```

![](img/04f1e84acd2f9ec005b75361565f390e_31.png)

运行上述代码，你将得到一张图表。X轴代表时间（年），Y轴代表模拟的股票价格。图中展示了100条不同的价格路径，它们因30%的波动率和10%的漂移率而呈现出分散的走势。

## 总结

本节课中，我们一起学习了如何在Python中模拟几何布朗运动。我们首先定义了模型的参数（μ, σ, S0），然后利用其显式解公式和NumPy的向量化计算，高效地生成了多条资产价格路径，最后通过Matplotlib将结果可视化。

![](img/04f1e84acd2f9ec005b75361565f390e_33.png)

几何布朗运动是金融工程和期权定价理论的基石模型。希望你能从本教程中获得启发。在下一个教程中，我们将探讨如何运用金融数学、微积分和随机积分知识，为更复杂的随机微分方程添加参数或特性。如果你想深入学习，请关注后续更新。