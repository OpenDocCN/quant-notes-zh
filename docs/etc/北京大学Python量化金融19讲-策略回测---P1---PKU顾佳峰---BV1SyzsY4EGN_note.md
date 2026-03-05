# Python量化金融19讲：策略回测

## 📚 课程概述

在本节课中，我们将学习量化金融中一个至关重要的环节——策略回测。我们将通过一个简单的例子，演示如何使用Python模拟股票价格数据，并基于此数据构建和测试一个交易策略，以评估其有效性。

## 📈 为什么要进行策略回测

![](img/aa06de603b17073018b4ee89abb1d037_1.png)

在用量化方法编写金融策略代码后，我们需要检验该策略在不同市场样本下的合理性与有效性。策略回测正是使用样本外数据对策略进行重新估计和验证的方法。通常，一个完整的量化策略开发流程包括：策略构建、回测、模拟盘测试，最后才是实盘操作。因此，回测是连接策略构想与实际应用的关键桥梁。

## 🧪 策略回测演示

接下来，我们将通过一个具体案例来演示策略回测的完整过程。

### 第一步：模拟股票价格数据

我们首先使用几何布朗运动模型来模拟股票价格走势。几何布朗运动是金融研究中常用的模型，尤其在布莱克-斯科尔斯-默顿期权定价模型中扮演核心角色。

以下是使用Python模拟GBM过程的代码：

```python
import numpy as np
import pandas as pd

# 设置参数
S0 = 100      # 初始股价
mu = 0.05     # 预期年化收益率
sigma = 0.2   # 年化波动率
T = 1.0       # 时间（年）
N = 200       # 交易期数
dt = T/N      # 每期时间间隔

# 模拟价格路径
np.random.seed(42)  # 确保结果可复现
W = np.random.standard_normal(N)
W = np.cumsum(W)*np.sqrt(dt)  # 标准布朗运动
X = (mu - 0.5 * sigma**2) * np.arange(N)*dt + sigma * W
S = S0 * np.exp(X)  # 几何布朗运动价格

# 创建DataFrame
price_data = pd.DataFrame(S, columns=[‘Price’])
```

### 第二步：定义交易信号

基于模拟的价格数据，我们定义一个简单的移动平均策略。该策略的核心是计算短期和长期的平均收益，并据此生成交易信号。

以下是计算移动平均和生成信号的代码：

![](img/aa06de603b17073018b4ee89abb1d037_3.png)

```python
# 计算短期（5期）和长期（10期）移动平均
price_data[‘MA_Short’] = price_data[‘Price’].rolling(window=5).mean()
price_data[‘MA_Long’] = price_data[‘Price’].rolling(window=10).mean()

# 定义交易信号：短期均线上穿长期均线时买入（信号为1），否则为0（不买入或做空）
price_data[‘Signal’] = 0
price_data.loc[price_data[‘MA_Short’] > price_data[‘MA_Long’], ‘Signal’] = 1
```

**核心概念解释**：
*   **短期移动平均（MA_Short）**：过去5个交易期的平均价格。
*   **长期移动平均（MA_Long）**：过去10个交易期的平均价格。
*   **交易信号（Signal）**：当 `MA_Short > MA_Long` 时，产生买入信号（1）；否则，不产生买入信号（0）。

### 第三步：执行策略回测

定义好策略后，我们将其应用于模拟数据，进行回测，以观察策略的绩效。

![](img/aa06de603b17073018b4ee89abb1d037_5.png)

以下是执行回测并计算策略收益的代码：

```python
# 计算策略收益：当信号为1时，持有资产；信号为0时，空仓。
price_data[‘Returns’] = price_data[‘Price’].pct_change()
price_data[‘Strategy_Returns’] = price_data[‘Signal’].shift(1) * price_data[‘Returns’]

# 计算累计收益
price_data[‘Cumulative_Market’] = (1 + price_data[‘Returns’]).cumprod()
price_data[‘Cumulative_Strategy’] = (1 + price_data[‘Strategy_Returns’]).cumprod()
```

### 第四步：结果分析与可视化

最后，我们通过可视化图表来对比策略收益与简单持有（买入并持有）基准的收益情况。

以下是结果可视化的代码：

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 6))
plt.plot(price_data[‘Cumulative_Market’], label=‘买入并持有收益’, color=‘red’)
plt.plot(price_data[‘Cumulative_Strategy’], label=‘移动平均策略收益’, color=‘blue’)
plt.title(‘策略回测结果对比’)
plt.xlabel(‘交易期数’)
plt.ylabel(‘累计收益’)
plt.legend()
plt.grid(True)
plt.show()
```

![](img/aa06de603b17073018b4ee89abb1d037_7.png)

**结果解读**：
从生成的图表中，我们可以观察到代表移动平均策略收益的蓝色曲线，其最终累计收益明显高于代表简单买入并持有策略的红色曲线。这表明，在我们模拟的这个特定市场环境下，所定义的移动平均交易策略能够产生超越基准的超额收益。

## 🎯 课程总结

本节课我们一起学习了量化金融策略开发中的关键步骤——策略回测。我们首先使用**几何布朗运动模型**模拟了股票价格数据，然后基于此数据构建了一个简单的**双移动平均交叉策略**，并完整地执行了回测流程。通过对比策略收益与基准收益，我们验证了该策略在模拟环境下的有效性。请记住，回测是检验策略历史表现的重要工具，但过去的优异表现并不完全等同于未来的盈利能力，在实际应用中还需结合更多风险管理和市场分析。