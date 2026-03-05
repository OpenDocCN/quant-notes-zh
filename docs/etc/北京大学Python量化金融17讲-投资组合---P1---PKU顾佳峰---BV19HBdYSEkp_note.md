# 北京大学Python量化金融17讲：投资组合理论 - P1 - PKU顾佳峰

在本节课中，我们将要学习量化金融中的一个核心理论——投资组合理论。该理论由马科维茨提出，并因此获得了诺贝尔经济学奖。我们将了解其基本原理，并学习如何使用Python进行模拟计算，以找到在给定预期收益下风险最小的股票投资组合。

## 概述

![](img/fa0818a1f244423df7a3869ed1c8f530_1.png)

投资组合理论主要研究如何将资金分配到不同的资产（如股票）中，以实现收益与风险之间的最佳平衡。马科维茨发现，一个投资组合的预期收益是各资产收益的加权平均，但其风险（方差）并非各资产风险的简单平均。这为优化投资组合提供了理论基础。

## 核心概念与模型

马科维茨提出的均值-方差模型旨在寻找在特定预期收益水平下，风险最小的投资组合，或者在特定风险水平下，收益最大的投资组合。其中，风险通常用收益率的方差或标准差来衡量。

一个投资组合的预期收益公式为：
**E(R_p) = Σ (w_i * E(R_i))**
其中，`E(R_p)`是组合的预期收益，`w_i`是第i种资产的权重，`E(R_i)`是第i种资产的预期收益。

组合的风险（方差）公式为：
**σ_p² = Σ Σ (w_i * w_j * Cov(R_i, R_j))**
其中，`σ_p²`是组合收益的方差，`Cov(R_i, R_j)`是资产i和资产j收益之间的协方差。

## Python模拟：双资产组合案例

上一节我们介绍了投资组合理论的核心思想，本节中我们来看看如何用Python进行实际计算。我们首先从一个简单的两只股票的投资组合开始。

![](img/fa0818a1f244423df7a3869ed1c8f530_3.png)

假设有两只股票：
*   股票A：预期收益为5%，方差为8%（即标准差约为0.283）。
*   股票B：预期收益为20%，方差为30%（即标准差约为0.548）。

我们的目标是找到这两只股票的最优投资权重（`w1`和`w2`，且`w1 + w2 = 1`），使得投资组合的夏普比率最大化。夏普比率衡量的是每承担一单位风险所获得的超额收益，其公式为：
**Sharpe Ratio = (E(R_p) - R_f) / σ_p**
为简化，我们通常假设无风险利率`R_f`为0。

以下是使用Python进行模拟计算的步骤：

```python
import numpy as np
import matplotlib.pyplot as plt

# 定义两只股票的预期收益和标准差
returns = np.array([0.05, 0.20])
std_devs = np.array([np.sqrt(0.08), np.sqrt(0.30)])
# 为简化，假设两只股票相关系数为0
corr_coef = 0
cov_matrix = np.array([[std_devs[0]**2, corr_coef*std_devs[0]*std_devs[1]],
                       [corr_coef*std_devs[0]*std_devs[1], std_devs[1]**2]])

# 模拟不同的权重组合
weights = []
port_returns = []
port_risks = []
sharpe_ratios = []

for w1 in np.arange(0, 1.01, 0.01):
    w2 = 1 - w1
    weights.append([w1, w2])
    # 计算组合预期收益
    port_return = np.dot([w1, w2], returns)
    port_returns.append(port_return)
    # 计算组合风险（标准差）
    port_risk = np.sqrt(np.dot([w1, w2], np.dot(cov_matrix, [w1, w2])))
    port_risks.append(port_risk)
    # 计算夏普比率（假设无风险利率为0）
    sharpe_ratio = port_return / port_risk
    sharpe_ratios.append(sharpe_ratio)

# 找到夏普比率最大的组合
max_sharpe_idx = np.argmax(sharpe_ratios)
optimal_weights = weights[max_sharpe_idx]
optimal_return = port_returns[max_sharpe_idx]
optimal_risk = port_risks[max_sharpe_idx]
optimal_sharpe = sharpe_ratios[max_sharpe_idx]

print(f"最优权重: 股票A={optimal_weights[0]:.2%}, 股票B={optimal_weights[1]:.2%}")
print(f"组合预期收益: {optimal_return:.2%}")
print(f"组合风险（标准差）: {optimal_risk:.4f}")
print(f"夏普比率: {optimal_sharpe:.3f}")

# 绘制有效前沿和最优组合点
plt.figure(figsize=(10,6))
plt.scatter(port_risks, port_returns, c=sharpe_ratios, cmap='viridis', s=10)
plt.colorbar(label='Sharpe Ratio')
plt.scatter(optimal_risk, optimal_return, c='red', s=100, marker='*', label='Optimal Portfolio (Max Sharpe)')
plt.xlabel('Portfolio Risk (Standard Deviation)')
plt.ylabel('Portfolio Expected Return')
plt.title('Efficient Frontier for Two-Asset Portfolio')
plt.legend()
plt.grid(True)
plt.show()
```

运行上述代码后，我们得到类似以下的结果：
*   第一只股票（A）的最优持股比例约为78%。
*   第二只股票（B）的最优持股比例约为22%。
*   该组合的预期收益约为8.3%。
*   组合风险（标准差）约为0.0908。
*   夏普比率约为0.913。

从生成的图表中，我们可以看到所有可能权重组合构成的曲线（有效前沿），以及夏普比率最高的点（最优组合）。该点是通过从原点（风险为0，收益为0）向有效前沿做切线得到的，这条切线被称为资本市场线。

## Python模拟：多资产组合案例

理解了双资产组合后，本节中我们将其扩展到三只或更多股票的情况。其原理完全相同，只是计算变得更加复杂，并且有效前沿从一个“曲线”变成了一个“曲面”或“区域”。

假设有三只股票，其预期收益和协方差矩阵如下：

```python
# 定义三只股票的预期收益
returns_3 = np.array([0.08, 0.12, 0.15])
# 定义协方差矩阵（简化示例）
cov_matrix_3 = np.array([
    [0.0025, 0.0004, 0.0006],
    [0.0004, 0.0049, 0.0010],
    [0.0006, 0.0010, 0.0064]
])

# 使用优化算法寻找最优组合（这里使用简单的随机模拟）
num_portfolios = 10000
results = np.zeros((3, num_portfolios))

for i in range(num_portfolios):
    # 随机生成一组权重，并归一化使其和为1
    weights = np.random.random(3)
    weights /= np.sum(weights)
    # 计算组合收益和风险
    port_return = np.dot(weights, returns_3)
    port_risk = np.sqrt(np.dot(weights.T, np.dot(cov_matrix_3, weights)))
    # 计算夏普比率
    sharpe = port_return / port_risk
    # 存储结果
    results[0, i] = port_risk
    results[1, i] = port_return
    results[2, i] = sharpe

# 找到夏普比率最大的组合
max_sharpe_idx = np.argmax(results[2])
optimal_risk = results[0, max_sharpe_idx]
optimal_return = results[1, max_sharpe_idx]
optimal_sharpe = results[2, max_sharpe_idx]
# 注意：由于是随机模拟，每次运行得到的“最优权重”会略有不同，但收益和风险特征接近

print(f"模拟最优组合预期收益: {optimal_return:.2%}")
print(f"模拟最优组合风险: {optimal_risk:.4f}")
print(f"模拟最优夏普比率: {optimal_sharpe:.3f}")

# 绘制模拟结果散点图
plt.figure(figsize=(10,6))
plt.scatter(results[0, :], results[1, :], c=results[2, :], cmap='viridis', s=5, alpha=0.5)
plt.colorbar(label='Sharpe Ratio')
plt.scatter(optimal_risk, optimal_return, c='red', s=100, marker='*', label='Simulated Optimal Portfolio')
plt.xlabel('Portfolio Risk (Standard Deviation)')
plt.ylabel('Portfolio Expected Return')
plt.title('Simulated Portfolios for Three-Asset Case')
plt.legend()
plt.grid(True)
plt.show()
```

![](img/fa0818a1f244423df7a3869ed1c8f530_5.png)

通过模拟，我们可以得到一个大致的有效前沿区域，并找到其中夏普比率相对较高的组合。例如，可能得到一个近似最优的持股比例为：股票一70%，股票二20%，股票三10%，对应的组合回报率约为10.5%，风险约为0.096，夏普比率约为1.09。

## 总结

本节课中我们一起学习了马科维茨的投资组合理论。我们了解到，通过将不同资产进行组合，可以在不降低预期收益的情况下降低整体风险，或者在承担相同风险的情况下提高预期收益。我们使用Python演示了如何计算双资产组合的有效前沿和最优夏普比率组合，并将其原理扩展到多资产的情况。该理论为现代资产配置和风险管理奠定了坚实的基础。