# Python金融分析与量化交易实战课程：第6章：因子数据预处理实例 📊

在本节课中，我们将学习如何对因子数据进行预处理。因子是影响投资决策的关键指标，例如市净率或营收增长率。处理这些数据是构建有效量化策略的基础步骤。我们将通过三个核心步骤来讲解：去极值、标准化和中性化。

## 概述
因子数据预处理是量化分析的关键环节。原始数据中可能存在异常值、量纲不统一或受其他因素干扰等问题，直接使用会影响模型效果。本节将详细介绍三种预处理方法，并以苹果股价数据为例进行演示，最后总结核心要点。

## 因子数据预处理三步走 🚶‍♂️

上一节我们介绍了因子的基本概念，本节中我们来看看具体的预处理流程。以下是因子数据预处理的三个核心步骤。

### 1. 去极值
去极值旨在处理数据中的异常点或离群值。直接删除数据可能造成信息损失，因此更常见的做法是将超出指定界限的数值“拉回”到边界值。

以下是几种常见的去极值方法：

![](img/4072d7fe4d98e7f32b4af5872a030c81_1.png)

*   **分位数法**：此方法基于数据的分布位置设定边界。常用的是四分位距法。
    *   **中位数 (Q2)**：将数据排序后位于中间位置的值，对异常值不敏感。
    *   **下四分位数 (Q1)**：数据中25%位置的值。
    *   **上四分位数 (Q3)**：数据中75%位置的值。
    *   通过计算 **IQR = Q3 - Q1**，可以设定正常值范围为 **[Q1 - k * IQR, Q3 + k * IQR]**，通常k取1.5。超出此范围的值将被调整为边界值。

*   **MAD法 (Median Absolute Deviation)**：基于中位数计算数据的离散程度，对异常值更稳健。
    *   公式为：**MAD = median(|Xi - median(X)|)**。
    *   通常将偏离中位数超过 **n * MAD** 的值视为异常值并进行调整，n常取3。

*   **3σ法**：基于正态分布假设，将超出均值三倍标准差范围的值视为异常值。
    *   公式为：边界 = **均值 ± 3 * 标准差**。
    *   此方法对数据分布有要求，且均值本身易受极值影响。

### 2. 标准化
标准化旨在消除不同因子之间量纲和取值范围的差异，使它们具有可比性。经过标准化处理的数据通常符合均值为0、标准差为1的标准正态分布。

以下是两种常用的标准化方法：

![](img/4072d7fe4d98e7f32b4af5872a030c81_3.png)

*   **Z-Score 标准化**：这是最常用的标准化方法。
    *   公式为：**X_scaled = (X - μ) / σ**，其中μ是均值，σ是标准差。

![](img/4072d7fe4d98e7f32b4af5872a030c81_5.png)

*   **Min-Max 标准化**：将数据线性映射到指定区间（如[0, 1]）。
    *   公式为：**X_scaled = (X - X_min) / (X_max - X_min)**。

![](img/4072d7fe4d98e7f32b4af5872a030c81_7.png)

![](img/4072d7fe4d98e7f32b4af5872a030c81_9.png)

### 3. 中性化
中性化是金融因子处理中特有的步骤，目的是消除因子暴露中其他共性因素的影响（如行业、市值），从而剥离出因子的纯粹收益贡献。

![](img/4072d7fe4d98e7f32b4af5872a030c81_11.png)

常见的做法是使用线性回归进行中性化处理：
1.  将待处理的因子作为因变量 **Y**。
2.  将需要剔除影响的变量（如行业哑变量、市值对数）作为自变量 **X**。
3.  进行回归 **Y = βX + ε**，得到的残差 **ε** 即为中性化后的因子值。它代表了排除了X影响后，Y的独立部分。

![](img/4072d7fe4d98e7f32b4af5872a030c81_13.png)

## 实例演示：以苹果股价数据为例 💻

![](img/4072d7fe4d98e7f32b4af5872a030c81_15.png)

接下来，我们将通过代码演示上述预处理步骤。虽然股价本身不是典型因子，但处理方法完全一致。

首先，导入必要的工具包并读取数据。

![](img/4072d7fe4d98e7f32b4af5872a030c81_17.png)

```python
import pandas as pd
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt

# 读取苹果公司股价数据示例
data = pd.read_csv(‘AAPL_price.csv‘, index_col=‘Date‘, parse_dates=True)
price_series = data[‘Close‘]  # 以收盘价序列为例
```

### 第一步：去极值处理（以MAD法为例）

```python
def mad_outlier(series, n=3):
    median = np.median(series)
    mad = np.median(np.abs(series - median))
    threshold = n * mad
    lower_bound = median - threshold
    upper_bound = median + threshold
    # 将超出边界的值拉回边界
    series_adj = series.clip(lower=lower_bound, upper=upper_bound)
    return series_adj

![](img/4072d7fe4d98e7f32b4af5872a030c81_19.png)

price_mad_adj = mad_outlier(price_series)
```

### 第二步：标准化处理（以Z-Score为例）

```python
def zscore_standardize(series):
    mean = series.mean()
    std = series.std()
    series_std = (series - mean) / std
    return series_std

price_standardized = zscore_standardize(price_mad_adj)
```

### 第三步：中性化处理（示例）

假设我们需要剔除“市场收益率”对个股因子的影响。
```python
# 假设 market_return 是同期市场收益率序列
# 使用线性回归获取残差（中性化后的因子）
import statsmodels.api as sm
market_return = ... # 获取市场收益率数据
X = sm.add_constant(market_return) # 添加常数项
y = price_standardized
model = sm.OLS(y, X).fit()
price_neutralized = model.resid  # 残差即为中性化后的序列
```

## 总结
本节课中我们一起学习了因子数据预处理的三个核心步骤。
1.  **去极值**：处理异常数据点，常用分位数法、MAD法或3σ法，将极端值调整至合理边界。
2.  **标准化**：统一不同因子的量纲，常用Z-Score或Min-Max方法，使数据具有可比性。
3.  **中性化**：消除行业、市值等共性因素对因子的影响，常用线性回归法提取因子的独立信息。

![](img/4072d7fe4d98e7f32b4af5872a030c81_21.png)

经过这三步处理后的因子数据更加干净、可比，能够更真实地反映其与未来收益的关系，为后续的多因子模型构建和选股策略打下坚实基础。在实际的量化平台（如米筐）中，可以方便地获取各类原始因子并应用这些预处理方法。