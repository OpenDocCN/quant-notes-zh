# 金融科普：P1：用Python绘制Markowitz有效边界 📈

在本节课中，我们将学习如何使用Python编程语言，基于历史股票数据来绘制现代投资组合理论中的核心概念——Markowitz有效边界。我们将从数据获取开始，逐步计算股票的收益率与风险，并最终通过可视化展示投资组合的可行集与有效边界。

![](img/b6001f2998eef947548b854d4ef156f5_1.png)

## 1. 环境准备与数据导入 📦

首先，我们需要导入必要的Python库。这些库将帮助我们获取数据、进行数学计算以及绘制图表。

![](img/b6001f2998eef947548b854d4ef156f5_3.png)

```python
import tushare as ts
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import random
```

![](img/b6001f2998eef947548b854d4ef156f5_5.png)

![](img/b6001f2998eef947548b854d4ef156f5_7.png)

**核心库说明**：
*   `tushare`: 用于获取中国A股市场金融数据。
*   `pandas` 和 `numpy`: 用于数据处理和数学运算。
*   `matplotlib`: 用于数据可视化。

![](img/b6001f2998eef947548b854d4ef156f5_9.png)

如果你尚未安装 `tushare` 库，需要在命令行中使用以下命令安装：
```bash
pip install tushare
```

![](img/b6001f2998eef947548b854d4ef156f5_11.png)

## 2. 获取股票池数据 🏢

![](img/b6001f2998eef947548b854d4ef156f5_13.png)

上一节我们准备好了编程环境，本节中我们来看看如何获取用于构建投资组合的股票数据。我们选择沪深300指数的成分股作为初始股票池。

![](img/b6001f2998eef947548b854d4ef156f5_15.png)

![](img/b6001f2998eef947548b854d4ef156f5_17.png)

以下是获取并查看股票池数据的步骤：
1.  设置你的tushare API token（需在官网注册获取）。
2.  获取沪深300成分股列表。
3.  从列表中筛选出我们用于构建组合的特定股票。

![](img/b6001f2998eef947548b854d4ef156f5_19.png)

```python
# 1. 设置token
ts.set_token('你的tushare_token')
pro = ts.pro_api()

# 2. 获取沪深300成分股列表（示例数据，实际需从文件或接口读取）
# 假设我们有一个包含代码和名称的DataFrame: hs300_components
hs300_components = pd.read_csv('hs300_components.csv') # 示例文件

![](img/b6001f2998eef947548b854d4ef156f5_21.png)

# 3. 筛选股票：例如，选取某一日收盘价最低的5只股票
# 这里需要先获取这些股票在特定日期的价格，为简化，我们假设直接选取了5只股票代码
selected_stocks = ['000001.SZ', '000002.SZ', '000333.SZ', '000858.SZ', '002415.SZ']
```

![](img/b6001f2998eef947548b854d4ef156f5_23.png)

## 3. 获取历史数据与计算基础指标 📊

![](img/b6001f2998eef947548b854d4ef156f5_25.png)

在确定了股票池之后，我们需要获取这些股票的历史价格数据，并据此计算每只股票的历史收益率均值、方差以及股票之间的协方差矩阵。这些是构建投资组合风险-收益模型的基础。

![](img/b6001f2998eef947548b854d4ef156f5_27.png)

![](img/b6001f2998eef947548b854d4ef156f5_29.png)

以下是计算过程：
1.  通过tushare接口获取选定股票在过去一段时间（例如3年）的日度收盘价数据。
2.  计算每只股票的对数收益率或简单收益率。
3.  基于历史收益率数据，计算每只股票的**预期收益率（均值）**和**收益率方差（风险）**。
4.  计算所有股票收益率之间的**协方差矩阵**。

```python
# 1. 获取历史日线行情数据
start_date = '2021-03-15'
end_date = '2024-03-15'
# 注意：需循环获取每只股票的数据并整合，这里为简化表示
# df_prices 应是一个DataFrame，索引为日期，列为各股票代码对应的收盘价

![](img/b6001f2998eef947548b854d4ef156f5_31.png)

![](img/b6001f2998eef947548b854d4ef156f5_33.png)

# 2. 计算日度收益率（以简单收益率为例）
df_returns = df_prices.pct_change().dropna()

# 3. 计算每只股票的预期收益率（年化）和方差（年化）
# 假设一年有252个交易日
expected_returns = df_returns.mean() * 252
covariance_matrix = df_returns.cov() * 252
```

![](img/b6001f2998eef947548b854d4ef156f5_35.png)

**核心公式**：
*   投资组合预期收益率：**E(R_p) = w^T * μ**
*   投资组合方差（风险）：**σ_p^2 = w^T * Σ * w**
    *   `w` 是资产权重向量。
    *   `μ` 是资产预期收益率向量。
    *   `Σ` 是资产收益率的协方差矩阵。

![](img/b6001f2998eef947548b854d4ef156f5_37.png)

## 4. 生成随机投资组合与计算可行集 🎲

有了单个资产的收益与风险指标，我们就可以构建投资组合了。一个投资组合由一组权重定义，所有权重之和为1。我们将随机生成大量这样的权重组合，并计算每个组合对应的预期收益率和风险，这些点构成的集合称为“可行集”。

![](img/b6001f2998eef947548b854d4ef156f5_39.png)

![](img/b6001f2998eef947548b854d4ef156f5_41.png)

以下是生成可行集的步骤：
1.  定义一个函数，用于生成一组随机的、总和为1的权重。
2.  定义一个`Portfolio`类，其方法可以根据给定的权重、预期收益率向量和协方差矩阵，计算组合的收益和风险。
3.  多次随机生成权重，计算对应的收益与风险，收集所有结果。

```python
def generate_random_weights(n_assets):
    """生成随机权重"""
    weights = np.random.random(n_assets)
    weights /= np.sum(weights) # 归一化，使和为1
    return weights

class Portfolio:
    def __init__(self, weights, expected_returns, cov_matrix):
        self.weights = weights
        self.expected_returns = expected_returns
        self.cov_matrix = cov_matrix

    def portfolio_return(self):
        """计算组合预期收益率"""
        return np.dot(self.weights.T, self.expected_returns)

    def portfolio_variance(self):
        """计算组合方差"""
        return np.dot(self.weights.T, np.dot(self.cov_matrix, self.weights))

# 生成可行集
n_portfolios = 5000
results = np.zeros((n_portfolios, 3)) # 列分别为：收益、方差、标准差

for i in range(n_portfolios):
    weights = generate_random_weights(len(selected_stocks))
    port = Portfolio(weights, expected_returns, covariance_matrix)
    ret = port.portfolio_return()
    var = port.portfolio_variance()
    std = np.sqrt(var)
    results[i, :] = [ret, var, std]
```

![](img/b6001f2998eef947548b854d4ef156f5_43.png)

## 5. 绘制有效边界 📉

最后，我们将可行集绘制在图表上。其中，横轴代表风险（方差或标准差），纵轴代表预期收益率。可行集最左上方的边缘，即在相同风险下提供最高收益，或在相同收益下承担最低风险的那些投资组合，就构成了“有效边界”。

以下是绘图代码：
```python
# 将结果转换为DataFrame便于处理
df_results = pd.DataFrame(results, columns=['Return', 'Variance', 'Std'])

![](img/b6001f2998eef947548b854d4ef156f5_45.png)

# 绘制以方差为风险度量的可行集和有效边界
plt.figure(figsize=(12, 6))
plt.subplot(1, 2, 1)
plt.scatter(df_results['Variance'], df_results['Return'], c=df_results['Return']/df_results['Std'], cmap='viridis', alpha=0.5, marker='.')
plt.xlabel('Portfolio Variance (Risk)')
plt.ylabel('Portfolio Expected Return')
plt.title('Efficient Frontier (Variance as Risk)')
plt.colorbar(label='Sharpe Ratio (Return/Std)')

# 绘制以标准差为风险度量的可行集和有效边界
plt.subplot(1, 2, 2)
plt.scatter(df_results['Std'], df_results['Return'], c=df_results['Return']/df_results['Std'], cmap='viridis', alpha=0.5, marker='.')
plt.xlabel('Portfolio Standard Deviation (Risk)')
plt.ylabel('Portfolio Expected Return')
plt.title('Efficient Frontier (Std as Risk)')
plt.colorbar(label='Sharpe Ratio (Return/Std)')

plt.tight_layout()
plt.show()
```

**图表解读**：
*   第一张图（横轴为方差）中，有效边界呈现**抛物线**形状。
*   第二张图（横轴为标准差）中，有效边界呈现**双曲线的一支**形状。这是Markowitz投资组合理论的经典图示。

![](img/b6001f2998eef947548b854d4ef156f5_47.png)

## 总结 🎯

![](img/b6001f2998eef947548b854d4ef156f5_49.png)

本节课中我们一起学习了Markowitz现代投资组合理论的可视化实践。我们从获取沪深300成分股数据开始，计算了历史收益率、方差与协方差，然后通过随机生成数千个投资组合权重模拟了“可行集”，最终绘制出了以方差和标准差为风险度量的“有效边界”图。这个过程清晰地展示了风险与收益之间的权衡关系，以及如何通过资产配置来优化投资组合。