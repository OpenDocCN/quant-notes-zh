# 量化金融与机器学习：P1：CAPM时间序列回归的Python实现 📈

![](img/8a54bdc9e800f3b07c099b8a79836e65_1.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_3.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_5.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_7.png)

在本节课中，我们将学习如何使用Python实现资本资产定价模型的时间序列回归。我们将通过获取股票与市场指数数据，计算收益率，并进行线性回归来估计股票的阿尔法和贝塔系数。

![](img/8a54bdc9e800f3b07c099b8a79836e65_9.png)

## 概述与核心概念

![](img/8a54bdc9e800f3b07c099b8a79836e65_11.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_13.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_14.png)

资本资产定价模型的核心是计算股票的阿尔法（α）和贝塔（β）系数。这本质上是一个线性回归问题，其模型公式为：

![](img/8a54bdc9e800f3b07c099b8a79836e65_16.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_18.png)

**`Ri - Rf = α + β * (Rm - Rf) + ε`**

![](img/8a54bdc9e800f3b07c099b8a79836e65_20.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_22.png)

其中：
*   **`Ri`** 代表个股的收益率。
*   **`Rf`** 代表无风险利率。
*   **`Rm`** 代表市场组合的收益率。
*   **`α`** 代表超额收益（阿尔法）。
*   **`β`** 代表股票相对于市场的系统性风险（贝塔）。

![](img/8a54bdc9e800f3b07c099b8a79836e65_24.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_26.png)

本节我们将通过代码一步步实现这个回归过程。

![](img/8a54bdc9e800f3b07c099b8a79836e65_28.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_30.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_32.png)

## 数据准备与对齐

![](img/8a54bdc9e800f3b07c099b8a79836e65_34.png)

首先，我们需要获取个股和市场指数的历史价格数据。这里我们以“新安股份”作为目标股票，以“中证流通指数”作为市场指数（即 `Rm`）。

![](img/8a54bdc9e800f3b07c099b8a79836e65_36.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_38.png)

以下是实现步骤：

![](img/8a54bdc9e800f3b07c099b8a79836e65_40.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_42.png)

1.  **导入必要的库**：我们需要使用 `pandas` 进行数据处理，`numpy` 进行数值计算，`statsmodels` 进行回归分析，以及 `matplotlib` 进行可视化。
2.  **获取数据**：分别获取股票和指数的历史价格序列。
3.  **数据合并与对齐**：由于股票可能存在停牌日，导致数据缺失，我们需要将两支数据基于日期进行对齐，并填充缺失值。

![](img/8a54bdc9e800f3b07c099b8a79836e65_44.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_46.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_48.png)

以下是具体的代码实现：

![](img/8a54bdc9e800f3b07c099b8a79836e65_50.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_52.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_54.png)

```python
import pandas as pd
import numpy as np
import statsmodels.api as sm
import matplotlib.pyplot as plt

![](img/8a54bdc9e800f3b07c099b8a79836e65_56.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_58.png)

# 假设已经获取了‘新安股份’和‘中证流通指数’的收盘价数据，存储为DataFrame
# stock_data 和 index_data，索引为日期，有一列‘close’代表收盘价。

![](img/8a54bdc9e800f3b07c099b8a79836e65_60.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_62.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_64.png)

# 将两个DataFrame合并，以指数日期为基准进行左连接，确保日期索引一致
merged_data = pd.merge(index_data, stock_data, left_index=True, right_index=True, how='left', suffixes=('_index', '_stock'))

![](img/8a54bdc9e800f3b07c099b8a79836e65_66.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_68.png)

# 使用前向填充方法处理因停牌产生的缺失值
merged_data.fillna(method='ffill', inplace=True)

![](img/8a54bdc9e800f3b07c099b8a79836e65_70.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_72.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_74.png)

# 为列名赋予更清晰的标识
merged_data.columns = ['index_close', 'stock_close']
```

![](img/8a54bdc9e800f3b07c099b8a79836e65_76.png)

通过以上步骤，我们得到了一个对齐且无缺失值的价格数据集。

![](img/8a54bdc9e800f3b07c099b8a79836e65_78.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_80.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_82.png)

## 计算收益率与无风险利率

![](img/8a54bdc9e800f3b07c099b8a79836e65_84.png)

有了对齐的价格数据后，我们需要计算股票和指数的日收益率，并计算对应的日度无风险利率。

![](img/8a54bdc9e800f3b07c099b8a79836e65_86.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_88.png)

计算日收益率的公式为：**`收益率_t = (价格_t / 价格_{t-1}) - 1`**

![](img/8a54bdc9e800f3b07c099b8a79836e65_90.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_92.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_94.png)

以下是计算过程：

![](img/8a54bdc9e800f3b07c099b8a79836e65_96.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_98.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_100.png)

```python
# 计算股票和指数的日收益率
merged_data['stock_return'] = merged_data['stock_close'] / merged_data['stock_close'].shift(1) - 1
merged_data['index_return'] = merged_data['index_close'] / merged_data['index_close'].shift(1) - 1

![](img/8a54bdc9e800f3b07c099b8a79836e65_102.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_104.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_106.png)

# 删除因计算收益率产生的第一个空值行
merged_data.dropna(inplace=True)

![](img/8a54bdc9e800f3b07c099b8a79836e65_108.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_110.png)

# 设定年化无风险利率为3.6%，并计算日度无风险利率（按360天计算）
annual_rf = 0.036
daily_rf = (1 + annual_rf) ** (1/360) - 1
```

![](img/8a54bdc9e800f3b07c099b8a79836e65_112.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_114.png)

现在，我们得到了用于回归分析的核心数据：股票的日超额收益率 (`stock_return - daily_rf`) 和市场的日超额收益率 (`index_return - daily_rf`)。

![](img/8a54bdc9e800f3b07c099b8a79836e65_116.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_118.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_120.png)

## 执行时间序列回归

![](img/8a54bdc9e800f3b07c099b8a79836e65_122.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_123.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_124.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_126.png)

上一节我们准备好了回归所需的变量。本节中，我们将使用 `statsmodels` 库进行线性回归，以估计 CAPM 模型中的 α 和 β。

![](img/8a54bdc9e800f3b07c099b8a79836e65_128.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_130.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_132.png)

在回归中，因变量 Y 是股票的超额收益，自变量 X 是市场的超额收益。我们需要在自变量中添加常数项，以估计截距 α。

![](img/8a54bdc9e800f3b07c099b8a79836e65_134.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_135.png)

以下是回归代码：

![](img/8a54bdc9e800f3b07c099b8a79836e65_137.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_139.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_141.png)

```python
# 准备回归数据
Y = merged_data['stock_return'] - daily_rf  # 股票超额收益 (Ri - Rf)
X = merged_data['index_return'] - daily_rf  # 市场超额收益 (Rm - Rf)
X = sm.add_constant(X)  # 添加常数项，用于估计阿尔法(α)

![](img/8a54bdc9e800f3b07c099b8a79836e65_143.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_145.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_147.png)

# 执行普通最小二乘法(OLS)回归
model = sm.OLS(Y, X)
results = model.fit()

![](img/8a54bdc9e800f3b07c099b8a79836e65_148.png)

# 打印回归结果摘要
print(results.summary())
```

![](img/8a54bdc9e800f3b07c099b8a79836e65_150.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_152.png)

运行代码后，`results.summary()` 会输出详细的回归结果。我们需要重点关注以下几点：
*   **`coef` (系数)**：`const` 项的值就是 **α**，`index_return` 对应的系数就是 **β**。
*   **`R-squared` (决定系数)**：在0到1之间，越接近1表示模型拟合度越好。
*   **`P>|t|` (P值)**：用于判断系数的显著性。通常P值小于0.05认为该系数显著不为零。

![](img/8a54bdc9e800f3b07c099b8a79836e65_154.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_156.png)

## 结果解读与可视化

![](img/8a54bdc9e800f3b07c099b8a79836e65_158.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_160.png)

从回归结果中，我们可以解读出股票的风险收益特征。例如，如果 β 为 1.2，意味着市场收益变动1%时，该股票收益平均变动1.2%，波动性大于市场。α 则代表了无法被市场解释的超额收益。

![](img/8a54bdc9e800f3b07c099b8a79836e65_162.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_164.png)

最后，我们可以将数据点和拟合的回归线可视化，直观地展示拟合效果。

![](img/8a54bdc9e800f3b07c099b8a79836e65_166.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_168.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_170.png)

```python
# 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(X['index_return'], Y, alpha=0.5, label='Data Points')

![](img/8a54bdc9e800f3b07c099b8a79836e65_172.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_174.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_176.png)

# 绘制回归线
x_line = np.linspace(X['index_return'].min(), X['index_return'].max(), 100)
y_line = results.params['const'] + results.params['index_return'] * x_line
plt.plot(x_line, y_line, color='red', linewidth=2, label='Regression Line')

![](img/8a54bdc9e800f3b07c099b8a79836e65_178.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_180.png)

# 添加图表标签和图例
plt.xlabel('Market Excess Return (Rm - Rf)')
plt.ylabel('Stock Excess Return (Ri - Rf)')
plt.title('CAPM Time-Series Regression')
plt.legend()
plt.grid(True)
plt.show()

![](img/8a54bdc9e800f3b07c099b8a79836e65_182.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_184.png)

# 打印核心参数
print(f"估算的阿尔法(α)为: {results.params['const']:.6f}")
print(f"估算的贝塔(β)为: {results.params['index_return']:.6f}")
print(f"模型拟合优度(R-squared)为: {results.rsquared:.4f}")
```

![](img/8a54bdc9e800f3b07c099b8a79836e65_186.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_188.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_190.png)

生成的图表将显示市场超额收益与股票超额收益的散点分布，以及穿过这些点的最优拟合直线。直线的斜率就是 β，截距就是 α。

![](img/8a54bdc9e800f3b07c099b8a79836e65_192.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_193.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_194.png)

## 总结

![](img/8a54bdc9e800f3b07c099b8a79836e65_195.png)

![](img/8a54bdc9e800f3b07c099b8a79836e65_197.png)

本节课中，我们一起学习了CAPM时间序列回归的完整Python实现流程。我们从数据获取与对齐开始，计算了股票和市场的收益率，设定了无风险利率，并使用 `statsmodels` 库完成了线性回归分析，最终对结果进行了解读和可视化。

![](img/8a54bdc9e800f3b07c099b8a79836e65_199.png)

通过这个过程，我们得到了目标股票相对于市场的系统性风险（β）和超额收益能力（α）的定量估计，这是量化金融分析中的一个基础且重要的工具。