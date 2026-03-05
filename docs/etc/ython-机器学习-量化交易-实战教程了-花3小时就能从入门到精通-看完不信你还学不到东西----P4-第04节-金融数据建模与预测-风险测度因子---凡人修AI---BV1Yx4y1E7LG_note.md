# Python量化交易与机器学习：P4：金融数据建模与预测-风险测度因子 📈

在本节课中，我们将学习金融数据建模与预测的核心概念，特别是风险测度因子。课程将涵盖统计学习技术概述、金融时间序列分析、预测方法以及绩效与风险评估。我们将通过Python代码示例，帮助初学者理解并应用这些概念。

## 统计学习技术概述 📊

上一节我们介绍了课程的整体框架，本节中我们来看看统计学习的主要技术。统计学习主要分为以下几个核心内容。

### 回归分析

回归分析主要用于预测和分类。

**预测** 主要与因子模型相关。例如，预测标普500指数的明日价格，可以将其与多个因子（如标普500的历史序列、道琼斯指数、WTI原油价格、天然气价格等）建立关系。利用历史数据进行学习，最简单的模型是线性回归。

当因子数量众多时，为了筛选主要因子，可以使用**岭回归**或**拉索回归**。这两种方法在最小二乘目标函数后添加了惩罚项：
*   岭回归添加平方项惩罚。
*   拉索回归添加绝对值项惩罚。

它们的目的是在达到一定收敛条件时，将许多因子的系数收缩至零，从而辅助因子选择。

另一种降维方式是**主成分分析**和**因子模型**。它们的思想与上述回归不同，通过对原始特征进行线性组合，构造出新的因子。例如，将100个原始因子组合成10个新因子，再基于这些新因子对标普500的明日价格进行回归。这主要用于预测下一步的数值。

**分类** 问题不关心标普500明日的具体数值，只关心其涨跌或持平。这涉及到分类问题。

以下是几种常用的分类模型：
1.  **逻辑回归**：虽然名为回归，但实际是一个分类器。
2.  **判别分析**：包括线性和二次判别分析。
3.  **支持向量机**：通过寻找决策边界，使分类点尽可能远离边界，同时允许一定的错分，有助于防止过拟合和减小方差。

这三种分类器将在后续课程中详细介绍。

![](img/a612e3528c465cdf4e432319f8fe8f66_1.png)

### 贝叶斯模型

![](img/a612e3528c465cdf4e432319f8fe8f66_3.png)

贝叶斯模型与前述分类方法不同。分类完全基于历史市场数据，而贝叶斯模型引入了**先验**，即个人对参数分布或可能取值的主观判断。市场数据作为**后验**信息，与先验结合，形成贝叶斯估计。我们将在第六节课详细介绍。

### 时间序列分析

![](img/a612e3528c465cdf4e432319f8fe8f66_5.png)

时间序列分析与前述方法不同，它根据序列自身的滞后项对未来值进行一步、两步甚至K步预测。

时间序列分析主要包括三部分：
1.  **平稳时间序列**：具有均值、滞后项相关系数、方差均可估计的优秀特性。
2.  **异方差模型**：用于处理方差不可估的情况，可能需要引入协变量等。
3.  **卡尔曼滤波**：一种动态的状态空间模型，其预测参数会随着每日新样本的进入而自动更新，克服了传统模型参数固定不变的局限性。

### 树形模型与神经网络

近年来，树形模型和神经网络也被广泛应用于分类问题（如预测指数涨跌）。由于模型结构复杂，它们属于半黑箱的学习模型。

![](img/a612e3528c465cdf4e432319f8fe8f66_7.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_9.png)

其优点在于能够适应更复杂的内在机制。缺点也显而易见：过多的变量和层次使得许多参数难以调整甚至不可控。例如，在随机森林中，惩罚项或装袋法的参数选择往往难以达到最优。

![](img/a612e3528c465cdf4e432319f8fe8f66_11.png)

## 金融时间序列分析 ⏳

![](img/a612e3528c465cdf4e432319f8fe8f66_13.png)

上一节我们概述了统计学习技术，本节中我们来看看金融时间序列的一些具体市场特征。此处的时间序列分析主要介绍交易序列的市场特性，如平稳性、均值回复以及配对交易所体现的特征。在将序列套用特定模型前，需要先检验其特性（如是否平稳、是否异方差），以便于下一步建模。我们将使用小程序进行辅助分析。

![](img/a612e3528c465cdf4e432319f8fe8f66_15.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_17.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_19.png)

### 均值回复

![](img/a612e3528c465cdf4e432319f8fe8f66_21.png)

均值回复是统计套利中一个非常重要的概念。它指价格或收益率在一段时间内会显示出回归历史平均值的趋势。当序列价格当前较低时，可以预期其将上涨并回复到均值，从而考虑买入；反之，当价格远高于均值时，则考虑卖空以获利。构建复杂模型时，我们也希望其残差序列具有均值回复特性。

![](img/a612e3528c465cdf4e432319f8fe8f66_23.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_25.png)

在讨论均值回复前，先了解一个基础过程：**随机游走**。虽然股价序列通常不满足纯粹的随机游走，但它是许多序列的理论基础。

![](img/a612e3528c465cdf4e432319f8fe8f66_27.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_29.png)

随机游走的表达式为其差分项等于 \( dW_t \)，其中 \( dW_t \) 是一个均值为0、方差为 \( dt \) 的高斯过程（正态分布）。注意，\( dW_t \) 的标准差是 \( \sqrt{dt} \)，这个特性在衍生品定价和伊藤过程中非常重要。

![](img/a612e3528c465cdf4e432319f8fe8f66_31.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_33.png)

与随机游走不同，均值回复过程（又称**奥恩斯坦-乌伦贝克过程**）的漂移项包含一个将价格拉向长期均值 \( \mu \) 的力，其速率由 \( \theta \) 控制，\( \sigma \) 代表波动率。

鉴于均值回复良好的交易特性，我们常希望获得的残差序列本身具有此性质。最常见的检验方法是**单位根检验**。

单位根检验基于以下事实：如果序列 \( Y_t \) 具有均值回复，那么其差分 \( \Delta Y_t \) 与一阶滞后项 \( Y_{t-1} \) 的关系中，系数 \( \gamma \) 应小于0。如果 \( \gamma = 0 \)，则序列类似于随机游走过程。

![](img/a612e3528c465cdf4e432319f8fe8f66_35.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_37.png)

因此，检验均值回复就是检验原假设 \( H_0: \gamma = 0 \)（非平稳）是否被拒绝，从而接受备择假设 \( H_1: \gamma < 0 \)（平稳，均值回复）。

![](img/a612e3528c465cdf4e432319f8fe8f66_39.png)

让我们看一个Python示例。我们使用`statsmodels`库中的`adfuller`函数对亚马逊过去10年的调整后收盘价进行ADF检验。

![](img/a612e3528c465cdf4e432319f8fe8f66_41.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_43.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_45.png)

```python
from statsmodels.tsa.stattools import adfuller
import yfinance as yf

![](img/a612e3528c465cdf4e432319f8fe8f66_47.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_49.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_51.png)

# 获取亚马逊数据
amzn = yf.download('AMZN', start='2013-01-01', end='2023-01-01')
ts = amzn['Adj Close']

![](img/a612e3528c465cdf4e432319f8fe8f66_53.png)

# 执行ADF检验
result = adfuller(ts)
print('ADF Statistic: %f' % result[0])
print('p-value: %f' % result[1])
```
输出结果中，p值远大于0.05，因此不能拒绝原假设，即亚马逊的收盘价序列不满足均值回复。这与市场直觉一致，几乎所有的金融价格序列都不满足均值回复，它们更接近几何布朗运动或更激进的过程。

![](img/a612e3528c465cdf4e432319f8fe8f66_55.png)

那么讨论均值回复有何用？在配对交易或基于自回归的时间序列交易中，我们希望模型的**残差**是均值回复的，并利用残差的回复特性进行套利。

![](img/a612e3528c465cdf4e432319f8fe8f66_57.png)

### 平稳性

平稳性序列需满足三个条件：
1.  均值为常数。
2.  方差为常数。
3.  序列与其滞后项之间的协方差仅与时间间隔有关，与起始时间点无关。

![](img/a612e3528c465cdf4e432319f8fe8f66_59.png)

平稳序列的好处在于，基于历史一段数据建立的模型对未来预测具有意义。如果预测序列不平稳，那么预测出的均值、方差等统计量将失去意义，基于此进行的风险评估也将无效。因此，我们希望处理后的过程具有平稳性。

![](img/a612e3528c465cdf4e432319f8fe8f66_61.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_63.png)

接下来引入一个比单位根检验更常用的统计量：**赫斯特指数**。它通过评估价格对数方差与时间差之间的关系 \( \text{Var}(\log(P_t)) \propto t^{2H} \) 来提供一个标量值 \( H \)。
*   \( H < 0.5 \)：序列为均值回复。
*   \( H = 0.5 \)：序列为几何布朗运动（类似随机游走）。
*   \( H > 0.5 \)：序列具有趋势性。

![](img/a612e3528c465cdf4e432319f8fe8f66_65.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_67.png)

其具体方法是对价格对数方差和时间差取对数后进行回归，求得 \( H \) 值。

![](img/a612e3528c465cdf4e432319f8fe8f66_69.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_71.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_73.png)

我们构造几何布朗运动、均值回复和带趋势的三个时间序列进行检验。

```python
import numpy as np
import pandas as pd

![](img/a612e3528c465cdf4e432319f8fe8f66_75.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_77.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_79.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_81.png)

def hurst(ts):
    """计算赫斯特指数"""
    lags = range(2, 100)
    tau = [np.sqrt(np.std(np.subtract(ts[lag:], ts[:-lag]))) for lag in lags]
    poly = np.polyfit(np.log(lags), np.log(tau), 1)
    return poly[0]*2.0

![](img/a612e3528c465cdf4e432319f8fe8f66_83.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_85.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_87.png)

# 生成示例序列
gbm = np.log(np.cumsum(np.random.randn(100000)) + 1000) # 几何布朗运动
mr = np.zeros(100000) # 均值回复
for i in range(1, len(mr)):
    mr[i] = mr[i-1]*0.9 + np.random.randn()
trend = np.log(np.cumsum(np.random.randn(100000) + 0.1) + 1000) # 带趋势

![](img/a612e3528c465cdf4e432319f8fe8f66_89.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_91.png)

print("GBM Hurst: %.3f" % hurst(gbm))
print("Mean Reversion Hurst: %.3f" % hurst(mr))
print("Trending Hurst: %.3f" % hurst(trend))
```
可以看到，几何布朗运动的H值接近0.5，均值回复的接近0，带趋势的大于0.5。对于亚马逊股价，其H值接近但略小于0.5，说明它基本满足几何布朗运动假设，且比标准几何布朗运动稍保守。

![](img/a612e3528c465cdf4e432319f8fe8f66_93.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_95.png)

### 配对交易

![](img/a612e3528c465cdf4e432319f8fe8f66_97.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_99.png)

通过上面的例子，我们看到很难找到单只具有均值回复特性的可交易资产。因此，经典的解决方案是**配对交易**，即构建一个资产组合，使其价差序列具有均值回复特性。

![](img/a612e3528c465cdf4e432319f8fe8f66_101.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_103.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_105.png)

理论上，应选择业务相似、受共同市场因素影响的公司（如苹果与微软，谷歌与脸书），或同一标的的不同合约（如黄金现货与期货）。它们的相对价格可能会因事件或时间差出现短暂分歧，并逐渐恢复，这满足了均值回复的市场前提。通过适当的回归，它们的残差序列大概率是均值回复的。

其核心是一个简单的线性回归：\( Y_t = \alpha + \beta X_t + \epsilon_t \)，并检验残差 \( \epsilon_t \) 是否平稳或均值回复。但ADF检验不提供对冲比率 \( \beta \)。

因此，需要引入**增强的迪基-富勒检验**，它能自动对两个时间序列进行回归，确定最优的对冲比率 \( \beta \)，并在此线性组合下自动检验残差的平稳性。需要注意的是，由于市场动态变化，对冲比率 \( \beta \) 应随时间调整，通常采用滚动窗口（如每月一次）进行重新估计，以避免重大损失。

![](img/a612e3528c465cdf4e432319f8fe8f66_107.png)

让我们看一个例子，使用埃克森美孚和雪佛龙进行配对交易分析。

![](img/a612e3528c465cdf4e432319f8fe8f66_109.png)

```python
import numpy as np
import pandas as pd
import yfinance as yf
from statsmodels.tsa.stattools import coint
import matplotlib.pyplot as plt

def plot_price_series(df, ts1, ts2):
    """绘制价格序列"""
    fig, ax = plt.subplots()
    ax.plot(df.index, df[ts1], label=ts1)
    ax.plot(df.index, df[ts2], label=ts2)
    ax.set_xlabel('Date')
    ax.set_ylabel('Price')
    ax.legend()
    plt.show()

![](img/a612e3528c465cdf4e432319f8fe8f66_111.png)

def plot_scatter_series(df, ts1, ts2):
    """绘制散点图"""
    plt.scatter(df[ts1], df[ts2])
    plt.xlabel(ts1)
    plt.ylabel(ts2)
    plt.show()

![](img/a612e3528c465cdf4e432319f8fe8f66_113.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_115.png)

def plot_residuals(df):
    """绘制残差序列"""
    fig, ax = plt.subplots()
    ax.plot(df.index, df['res'], label='Residuals')
    ax.axhline(0, color='black')
    ax.set_xlabel('Date')
    ax.set_ylabel('Residual')
    ax.legend()
    plt.show()

![](img/a612e3528c465cdf4e432319f8fe8f66_117.png)

# 获取数据
xom = yf.download('XOM', start='2020-01-01', end='2023-01-01')['Adj Close']
cvx = yf.download('CVX', start='2020-01-01', end='2023-01-01')['Adj Close']
df = pd.DataFrame({'XOM': xom, 'CVX': cvx}).dropna()

![](img/a612e3528c465cdf4e432319f8fe8f66_119.png)

# 绘图
plot_price_series(df, 'XOM', 'CVX')
plot_scatter_series(df, 'XOM', 'CVX')

# CADF检验并获取对冲比率
score, pvalue, _ = coint(df['XOM'], df['CVX'])
print('Cointegration p-value: %.4f' % pvalue)

![](img/a612e3528c465cdf4e432319f8fe8f66_121.png)

# 通过线性回归计算对冲比率 (Beta) 和残差
beta = np.polyfit(df['XOM'], df['CVX'], 1)[0] # 斜率即为对冲比率
df['res'] = df['CVX'] - beta * df['XOM']

![](img/a612e3528c465cdf4e432319f8fe8f66_123.png)

plot_residuals(df)

# 对残差进行ADF检验
result = adfuller(df['res'])
print('Residual ADF p-value: %.4f' % result[1])
```
结果显示，协整检验的p值约为0.08，大于0.05但小于0.1。这意味着我们有约92%的信心认为残差序列具有均值回复特性，配对交易策略大概率符合市场猜想。

## 预测方法与评估 📉

上一节我们探讨了时间序列的特性，本节中我们来看看基本的预测手法以及如何评估预测效果和策略风险。

![](img/a612e3528c465cdf4e432319f8fe8f66_125.png)

### 基本预测手法

![](img/a612e3528c465cdf4e432319f8fe8f66_127.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_129.png)

在Python中，`scikit-learn`库提供了许多现成的机器学习技术，如逻辑回归、判别分析、支持向量机、决策树、随机森林及各种提升树算法。对于基本的神经网络也有包含，但更高级的卷积神经网络等建议使用Keras。我们将以标普500的价格方向预测为例进行简单应用。

首先介绍两个评估指标：
1.  **命中率**：预测值与真实值相等的样本数占总样本数的比例。越接近1，说明训练效果越好。
    ```python
    hit_rate = np.mean(y_pred == y_true)
    ```
2.  **混淆矩阵**：一个2x2矩阵，用于展示分类结果的详细情况。
    *   **真正例**：实际为上涨，预测也为上涨。
    *   **真负例**：实际为下跌，预测也为下跌。
    *   **假正例**：实际为下跌，预测为上涨（第一类错误）。
    *   **假负例**：实际为上涨，预测为下跌（第二类错误）。

    我们希望主对角线上的值（真正例和真负例）越大越好，非对角线上的值越小越好。在风控中，控制第一类错误（假正例）通常比第二类错误更重要。

接下来简要介绍几种分类器的核心思想，详细理论将在后续机器学习章节展开。

![](img/a612e3528c465cdf4e432319f8fe8f66_131.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_133.png)

**逻辑回归**的核心思想是假设后验分布（给定特征X后，Y属于某一类的概率）服从一个指数型的逻辑函数。通过最大化所有观测值的联合条件概率分布（似然函数）来估计参数。

![](img/a612e3528c465cdf4e432319f8fe8f66_135.png)

**判别分析**与逻辑回归不同，它首先假设特征X在每一类别下服从特定的分布（通常是多元高斯分布），并结合每个类别的先验概率，利用贝叶斯定理计算后验概率。当假设每个类别的协方差矩阵相同时，为线性判别分析；否则为二次判别分析。

![](img/a612e3528c465cdf4e432319f8fe8f66_137.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_139.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_141.png)

**支持向量机** 的基本思想是基于线性二分类，寻找一个能最大化分类间隔的最优决策边界。现实中数据可能线性不可分，因此采用**软间隔**分类器，允许一些样本落在间隔内或错分。对于非线性问题，可通过**核函数**（如多项式核、高斯核）将数据映射到高维空间实现线性可分。

![](img/a612e3528c465cdf4e432319f8fe8f66_143.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_145.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_147.png)

**决策树与随机森林**：决策树是一种树形分类器，通过递归地选择特征将数据二分，直到满足停止条件。特征选择通常基于信息增益或基尼不纯度。决策树的缺点是可能过于贪婪导致过拟合。**随机森林**通过构建多棵决策树并集成结果来降低过拟合。每棵树在构建时，从特征集中随机抽取一个子集来寻找最优分裂特征。

![](img/a612e3528c465cdf4e432319f8fe8f66_149.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_151.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_153.png)

其他高级方法还包括提升法、神经网络以及自然语言处理等。

![](img/a612e3528c465cdf4e432319f8fe8f66_155.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_157.png)

现在，我们应用上述机器学习方法对标普500第二天的涨跌进行预测。自变量使用其自身的滞后项。

![](img/a612e3528c465cdf4e432319f8fe8f66_159.png)

```python
import numpy as np
import pandas as pd
import yfinance as yf
from sklearn.linear_model import LogisticRegression
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis, QuadraticDiscriminantAnalysis
from sklearn.svm import SVC
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import confusion_matrix, accuracy_score

def create_lagged_series(symbol, start_date, end_date, lags=5):
    """创建带滞后项的数据序列"""
    ts = yf.download(symbol, start=start_date, end=end_date)['Adj Close']
    tsreturns = ts.pct_change() * 100.0 # 计算收益率百分比

    # 创建滞后序列的数据框
    d = pd.DataFrame()
    d['Today'] = tsreturns
    d['Direction'] = np.where(d['Today'] >= 0, 1, 0) # 1代表上涨或持平，0代表下跌

    for i in range(0, lags):
        d['Lag'+str(i+1)] = tsreturns.shift(i+1)

    d = d.dropna()
    return d

![](img/a612e3528c465cdf4e432319f8fe8f66_161.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_163.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_165.png)

# 获取数据
data = create_lagged_series('^GSPC', '2000-01-01', '2023-01-01', lags=5)

![](img/a612e3528c465cdf4e432319f8fe8f66_167.png)

# 划分训练集和测试集 (使用最后1000天作为测试集)
X = data[['Lag1', 'Lag2', 'Lag3', 'Lag4', 'Lag5']]
y = data['Direction']
X_train, X_test = X[:-1000], X[-1000:]
y_train, y_test = y[:-1000], y[-1000:]

![](img/a612e3528c465cdf4e432319f8fe8f66_169.png)

# 定义模型
models = {
    'Logistic Regression': LogisticRegression(penalty='l2', C=1.0, solver='liblinear'),
    'LDA': LinearDiscriminantAnalysis(),
    'QDA': QuadraticDiscriminantAnalysis(),
    'SVM (Linear)': SVC(kernel='linear', C=1.0),
    'SVM (RBF)': SVC(kernel='rbf', C=1.0, gamma='scale'),
    'Random Forest': RandomForestClassifier(n_estimators=100, max_depth=5, random_state=42)
}

![](img/a612e3528c465cdf4e432319f8fe8f66_171.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_173.png)

# 训练、预测并评估
for name, model in models.items():
    model.fit(X_train, y_train)
    y_pred = model.predict(X_test)
    hit_rate = accuracy_score(y_test, y_pred)
    cm = confusion_matrix(y_test, y_pred)
    print(f"{name} - Hit Rate: {hit_rate:.4f}")
    print(f"Confusion Matrix:\n{cm}\n")
```
在这个例子中，二次判别分析和高斯核支持向量机可能表现较好。关于模型参数（如逻辑回归的惩罚项C、支持向量机的核函数gamma）的设置，需要根据具体问题进行调整。

![](img/a612e3528c465cdf4e432319f8fe8f66_175.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_176.png)

## 绩效与风险评估 🛡️

上一节我们介绍了预测方法，本节中我们来看看如何评估策略的整体绩效和风险。早期评估策略主要看年化收益率，但这种方法忽略了波动风险。2008年金融危机后，人们更加重视将收益与风险结合评估。

![](img/a612e3528c465cdf4e432319f8fe8f66_178.png)

### 夏普比率

![](img/a612e3528c465cdf4e432319f8fe8f66_180.png)

夏普比率很好地结合了收益率与波动率风险。其表达式为：
\[
\text{Sharpe Ratio} = \frac{E[R_p - R_b]}{\sigma_p}
\]
其中，\( R_p \) 是投资组合收益率，\( R_b \) 是基准收益率（通常用无风险利率），\( \sigma_p \) 是投资组合收益率的标准差（波动率）。它表示投资者每承担一单位风险所获得的超额回报。夏普比率为正且越高越好，经验上超过1的策略算不错，能达到3则是相当优秀的策略。

![](img/a612e3528c465cdf4e432319f8fe8f66_182.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_184.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_186.png)

计算夏普比率的Python函数如下：

![](img/a612e3528c465cdf4e432319f8fe8f66_188.png)

```python
def annualised_sharpe(returns, N=252):
    """
    计算年化夏普比率
    N: 每年交易期数 (默认为252个交易日)
    """
    if returns.std() == 0:
        return 0
    return np.sqrt(N) * returns.mean() / returns.std()

![](img/a612e3528c465cdf4e432319f8fe8f66_190.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_192.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_194.png)

# 示例：计算苹果股票买入持有策略的夏普比率
aapl = yf.download('AAPL', start='2011-01-01', end='2013-12-31')['Adj Close']
aapl_returns = aapl.pct_change().dropna()
sharpe = annualised_sharpe(aapl_returns)
print(f"AAPL Sharpe Ratio (2011-2013): {sharpe:.2f}")
```

### 回撤分析

![](img/a612e3528c465cdf4e432319f8fe8f66_196.png)

回撤分析也是常用的风险度量指标。
*   **回撤**：投资峰值与后续谷底之间的跌幅。
*   **最大回撤**：考察期内最大的回撤值。
*   **回撤持续时间**：从峰值到恢复至该峰值水平所经历的时间。

![](img/a612e3528c465cdf4e432319f8fe8f66_198.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_200.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_202.png)

### 风险价值

![](img/a612e3528c465cdf4e432319f8fe8f66_204.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_206.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_208.png)

银行系统特别常用的一个风险指标是**风险价值**。其定义是：在给定的置信水平下，某一资产在未来特定时间内可能的最大损失。例如，95%置信水平下VaR为5万元，意味着有95%的信心认为未来一年内的最大损失不会超过5万元。

![](img/a612e3528c465cdf4e432319f8fe8f66_210.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_212.png)

根据定义，VaR有不同的计算方法。

![](img/a612e3528c465cdf4e432319f8fe8f66_214.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_216.png)

**方差-协方差法** 假设收益率服从正态分布。那么95%置信水平下的VaR为：
\[
\text{VaR} = \text{均值} - 1.96 \times \text{标准差}
\]
（通常关注损失，故取负号）。这种方法实现简单：

![](img/a612e3528c465cdf4e432319f8fe8f66_218.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_220.png)

```python
from scipy.stats import norm

![](img/a612e3528c465cdf4e432319f8fe8f66_222.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_224.png)

![](img/a612e3528c465cdf4e432319f8fe8f66_226.png)

def var_cov_var(P, c, mu, sigma):
    """
    方差-协方差法计算VaR
    P: 投资组合价值
    c: 置信水平 (如0.95)
    mu: 收益率均值
    sigma: 收益率标准差
    """
    alpha = norm.ppf(1-c)
    return P - P*(mu + alpha*sigma)

![](img/a612e3528c465cdf4e432319f8fe8f66_228.png)

# 示例：计算花旗集团股票的VaR
c = yf.download('C', start='2010-01-01', end='2014-12-31')['Adj Close']
returns = c.pct_change().dropna()
P = 1000000 # 假设投资组合价值100万
mu, sigma = returns.mean(), returns.std()
var = var_cov_var(P, 0.95, mu, sigma)
print(f"95% VaR (Variance-Covariance): ${var:,.2f}")
```
但该方法假设正态分布，而实际金融收益率常呈现厚尾特征，因此可能低估风险。

![](img/a612e3528c465cdf4e432319f8fe8f66_230.png)

**历史模拟法** 思路更直观：假设未来重复历史情景。例如，基于过去500天的收益率数据，构造未来500种可能的情景路径，计算每种路径下的最终损益，然后取损益分布中对应置信水平的分位数作为VaR估计。这种方法不依赖于具体的分布假设。

## 总结 🎯

本节课我们一起学习了金融数据建模与预测的核心内容。我们首先概述了统计学习的主要技术，包括回归分析、贝叶斯模型、时间序列分析以及树模型和神经网络。接着，我们深入探讨了金融时间序列的关键特性，如均值回复和平稳性，并介绍了配对交易的原理与实践。然后，我们讲解了常见的预测方法（如逻辑回归、SVM）及其评估指标（命中率、混淆矩阵）。最后，我们学习了如何全面评估策略的绩效与风险，重点介绍了夏普比率和风险价值这两个重要指标。通过本课的学习，你应该对如何开始构建和评估一个量化交易策略有了基本的认识。

![](img/a612e3528c465cdf4e432319f8fe8f66_232.png)

**本节课作业**：使用历史模拟法计算花旗集团股票的风险价值，并与方差-协方差法的结果进行比较。