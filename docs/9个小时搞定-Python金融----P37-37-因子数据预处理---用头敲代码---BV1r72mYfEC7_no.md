# Python金融量化分析：P37：因子数据预处理 📊

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_0.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_2.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_4.png)

在本节课中，我们将学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能帮助我们获得更可靠的分析结果。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_6.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_8.png)

## 概述

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_10.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_12.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_14.png)

在获取了因子指标数据后，我们需要对其进行预处理，以消除异常值、统一量纲并剥离无关因素的影响。本节将分三步完成这一过程。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_16.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_18.png)

---

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_20.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_22.png)

## 第一步：去极值 🧹

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_24.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_26.png)

上一节我们介绍了如何查询因子数据，本节中我们来看看如何清洗这些数据。第一步是去除极端值，也称为离群值。极端值可能会扭曲统计分析的结果，因此需要处理。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_28.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_30.png)

我们采用“三西格玛”法则进行去极值操作。该方法基于数据的均值和标准差，将超出特定范围的值进行截断处理。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_32.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_34.png)

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_36.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_38.png)

1.  计算传入数据序列的均值 (`mean`) 和标准差 (`std`)。
2.  设定上限 (`up`) 和下限 (`down`)。公式为：
    *   `up = mean + n * std`
    *   `down = mean - n * std`
    （其中 `n` 通常取 3，即“三西格玛”）
3.  使用 `np.clip` 函数将序列中超出 `[down, up]` 范围的值替换为边界值，并返回处理后的序列。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_40.png)

```python
import numpy as np

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_42.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_44.png)

def filter_three_sigma(series, n=3):
    """
    使用三西格玛法则去除极值
    :param series: 待处理的数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    mean = series.mean()
    std = series.std()
    up = mean + n * std
    down = mean - n * std
    return np.clip(series, down, up)
```

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_46.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_48.png)

---

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_50.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_52.png)

## 第二步：标准化 📏

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_54.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_56.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_58.png)

完成去极值后，我们需要将数据标准化。标准化的目的是消除不同因子之间量纲和数量级的差异，使它们具有可比性。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_60.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_62.png)

标准化操作通常是指将数据转换为均值为0、标准差为1的分布。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_64.png)

以下是标准化函数 `standardize` 的实现步骤：

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_66.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_68.png)

1.  计算传入数据序列的均值 (`mean`) 和标准差 (`std`)。
2.  对序列中的每个值应用公式：`(value - mean) / std`。
3.  返回处理后的序列。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_70.png)

```python
def standardize(series):
    """
    对数据序列进行标准化（Z-Score标准化）
    :param series: 待处理的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_72.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_74.png)

---

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_76.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_78.png)

## 第三步：中性化 ⚖️

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_80.png)

最后一步是中性化处理。在金融因子分析中，许多因子会受到市值等常见风险因素的影响。中性化的目标就是剔除这些因素的影响，从而得到因子本身的“纯净”暴露。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_82.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_84.png)

我们通过线性回归来实现中性化。以市净率因子和市值的关系为例：

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_86.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_88.png)

*   **因变量 (Y)**：待中性化的因子（如市净率）。
*   **自变量 (X)**：需要被剥离的影响因素（如市值）。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_90.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_92.png)

以下是中性化函数 `neutralize` 的实现步骤：

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_94.png)

1.  建立因变量 `Y`（因子数据）和自变量 `X`（如市值数据）的线性回归模型。
2.  使用 `statsmodels` 库的普通最小二乘法 (`OLS`) 进行拟合。
3.  模型拟合后，其残差 (`resid`) 即为剔除了 `X` 影响后的“纯净”因子值。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_96.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_98.png)

```python
import statsmodels.api as sm

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_100.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_102.png)

def neutralize(factor_series, market_cap_series):
    """
    对因子进行市值中性化处理
    :param factor_series: 待中性化的因子数据序列
    :param market_cap_series: 市值数据序列
    :return: 中性化后的因子数据序列（残差）
    """
    # 将数据转换为float类型以满足模型要求
    Y = factor_series.astype(float)
    X = market_cap_series.astype(float)
    
    # 添加常数项（截距）
    X = sm.add_constant(X)
    
    # 建立并拟合OLS模型
    model = sm.OLS(Y, X)
    result = model.fit()
    
    # 返回残差，即中性化后的因子值
    return result.resid
```

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_104.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_106.png)

---

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_108.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_110.png)

## 总结

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_112.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_114.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_116.png)

本节课中我们一起学习了因子数据预处理的完整流程。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_118.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_120.png)

1.  **去极值**：使用三西格玛法则剔除异常值，保护模型不受极端数据干扰。
2.  **标准化**：通过Z-Score方法统一数据量纲，使不同因子具备可比性。
3.  **中性化**：借助线性回归模型，剥离市值等常见风险因素的影响，提取因子的独立信息。

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_122.png)

![](img/eebe5d02a2cf7233f5b0e5486bb8835b_124.png)

按顺序执行这三个步骤，即可将原始的因子数据转化为干净、可比、有效的分析输入，为后续的量化策略构建打下坚实基础。