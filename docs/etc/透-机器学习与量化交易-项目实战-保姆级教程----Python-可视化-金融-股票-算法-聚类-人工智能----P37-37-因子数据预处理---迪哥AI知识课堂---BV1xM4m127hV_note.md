# 机器学习与量化交易：P37：因子数据预处理 📊

![](img/a934331aa9f05b58d42278607ee9ca40_0.png)

![](img/a934331aa9f05b58d42278607ee9ca40_2.png)

![](img/a934331aa9f05b58d42278607ee9ca40_4.png)

![](img/a934331aa9f05b58d42278607ee9ca40_6.png)

![](img/a934331aa9f05b58d42278607ee9ca40_8.png)

在本节课中，我们将要学习因子数据预处理的核心步骤。预处理是量化交易中至关重要的一环，它能够清洗和标准化原始数据，为后续的模型构建打下坚实基础。我们将重点介绍三个关键操作：去极值、标准化和中性化。

![](img/a934331aa9f05b58d42278607ee9ca40_10.png)

![](img/a934331aa9f05b58d42278607ee9ca40_12.png)

![](img/a934331aa9f05b58d42278607ee9ca40_14.png)

上一节我们完成了指标的查询，本节中我们来看看如何对这些原始因子数据进行预处理。

![](img/a934331aa9f05b58d42278607ee9ca40_16.png)

![](img/a934331aa9f05b58d42278607ee9ca40_18.png)

![](img/a934331aa9f05b58d42278607ee9ca40_20.png)

## 去极值操作 🧹

![](img/a934331aa9f05b58d42278607ee9ca40_22.png)

![](img/a934331aa9f05b58d42278607ee9ca40_24.png)

![](img/a934331aa9f05b58d42278607ee9ca40_26.png)

去极值，也称为处理离群值，目的是消除数据中异常大或异常小的极端值，防止它们对后续分析产生过大影响。我们将采用“三西格玛”方法来实现。

![](img/a934331aa9f05b58d42278607ee9ca40_28.png)

![](img/a934331aa9f05b58d42278607ee9ca40_30.png)

![](img/a934331aa9f05b58d42278607ee9ca40_32.png)

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/a934331aa9f05b58d42278607ee9ca40_34.png)

![](img/a934331aa9f05b58d42278607ee9ca40_36.png)

![](img/a934331aa9f05b58d42278607ee9ca40_38.png)

1.  **计算均值和标准差**：首先，我们需要计算输入数据序列的均值（mean）和标准差（std）。
    *   均值公式：`mean = data.mean()`
    *   标准差公式：`std = data.std()`

![](img/a934331aa9f05b58d42278607ee9ca40_40.png)

![](img/a934331aa9f05b58d42278607ee9ca40_42.png)

![](img/a934331aa9f05b58d42278607ee9ca40_44.png)

2.  **设定上下限**：根据三西格玛原则，我们将上限设定为 `均值 + 3 * 标准差`，下限设定为 `均值 - 3 * 标准差`。
    *   上限公式：`upper = mean + 3 * std`
    *   下限公式：`lower = mean - 3 * std`

![](img/a934331aa9f05b58d42278607ee9ca40_46.png)

![](img/a934331aa9f05b58d42278607ee9ca40_48.png)

![](img/a934331aa9f05b58d42278607ee9ca40_50.png)

3.  **处理极值**：使用 `numpy.clip` 函数，将所有超出上下限的数据值“裁剪”到边界上，而不是直接删除。这保证了数据维度的完整性。
    *   代码：`np.clip(series, lower, upper)`

![](img/a934331aa9f05b58d42278607ee9ca40_52.png)

![](img/a934331aa9f05b58d42278607ee9ca40_54.png)

![](img/a934331aa9f05b58d42278607ee9ca40_56.png)

```python
import numpy as np

![](img/a934331aa9f05b58d42278607ee9ca40_58.png)

![](img/a934331aa9f05b58d42278607ee9ca40_60.png)

![](img/a934331aa9f05b58d42278607ee9ca40_62.png)

def filter_three_sigma(series, n=3):
    """
    使用三西格玛方法去除极值
    :param series: 输入的数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    mean = series.mean()
    std = series.std()
    upper = mean + n * std
    lower = mean - n * std
    return np.clip(series, lower, upper)
```

![](img/a934331aa9f05b58d42278607ee9ca40_64.png)

![](img/a934331aa9f05b58d42278607ee9ca40_66.png)

![](img/a934331aa9f05b58d42278607ee9ca40_68.png)

## 标准化操作 📏

![](img/a934331aa9f05b58d42278607ee9ca40_70.png)

![](img/a934331aa9f05b58d42278607ee9ca40_72.png)

![](img/a934331aa9f05b58d42278607ee9ca40_74.png)

标准化操作的目的是将不同量纲或量级的指标数据转换到同一尺度，使其具有可比性。我们采用Z-score标准化方法。

![](img/a934331aa9f05b58d42278607ee9ca40_76.png)

![](img/a934331aa9f05b58d42278607ee9ca40_78.png)

![](img/a934331aa9f05b58d42278607ee9ca40_80.png)

以下是标准化函数 `standardize` 的实现步骤：

![](img/a934331aa9f05b58d42278607ee9ca40_82.png)

![](img/a934331aa9f05b58d42278607ee9ca40_84.png)

![](img/a934331aa9f05b58d42278607ee9ca40_86.png)

1.  **计算均值和标准差**：与去极值操作相同，首先计算数据的均值和标准差。
2.  **执行标准化**：对数据序列中的每一个值，减去均值，再除以标准差。这样处理后的数据均值为0，标准差为1。
    *   标准化公式：`(series - mean) / std`

![](img/a934331aa9f05b58d42278607ee9ca40_88.png)

![](img/a934331aa9f05b58d42278607ee9ca40_90.png)

![](img/a934331aa9f05b58d42278607ee9ca40_92.png)

```python
def standardize(series):
    """
    对数据进行Z-score标准化
    :param series: 输入的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/a934331aa9f05b58d42278607ee9ca40_94.png)

![](img/a934331aa9f05b58d42278607ee9ca40_96.png)

## 中性化操作 ⚖️

![](img/a934331aa9f05b58d42278607ee9ca40_98.png)

![](img/a934331aa9f05b58d42278607ee9ca40_100.png)

![](img/a934331aa9f05b58d42278607ee9ca40_102.png)

中性化操作的目的是消除因子数据中与市值等风格因子的相关性，从而得到更“纯净”、更能反映股票自身特性的因子值。我们通过线性回归来实现。

![](img/a934331aa9f05b58d42278607ee9ca40_104.png)

![](img/a934331aa9f05b58d42278607ee9ca40_106.png)

![](img/a934331aa9f05b58d42278607ee9ca40_108.png)

以下是中性化函数 `neutralize` 的实现步骤：

![](img/a934331aa9f05b58d42278607ee9ca40_110.png)

![](img/a934331aa9f05b58d42278607ee9ca40_112.png)

1.  **明确变量关系**：假设我们要对“市净率”因子进行中性化，目标是消除“市值”对它的影响。在这个回归模型中：
    *   **因变量 (Y)**：待处理的因子数据（如市净率）。
    *   **自变量 (X)**：需要被剥离的影响因素（如市值）。

![](img/a934331aa9f05b58d42278607ee9ca40_114.png)

![](img/a934331aa9f05b58d42278607ee9ca40_116.png)

![](img/a934331aa9f05b58d42278607ee9ca40_118.png)

2.  **构建回归模型**：使用 `statsmodels` 库的普通最小二乘法（OLS）建立线性回归模型 `Y = W * X + b`，并拟合数据。

![](img/a934331aa9f05b58d42278607ee9ca40_120.png)

![](img/a934331aa9f05b58d42278607ee9ca40_122.png)

3.  **提取残差**：模型拟合后，计算真实Y值与模型预测Y值之间的差异，即残差。这个残差就是去除了市值影响后的“中性化”因子值。
    *   残差公式：`residual = Y - Y_predicted`

![](img/a934331aa9f05b58d42278607ee9ca40_124.png)

![](img/a934331aa9f05b58d42278607ee9ca40_126.png)

![](img/a934331aa9f05b58d42278607ee9ca40_128.png)

```python
import statsmodels.api as sm

![](img/a934331aa9f05b58d42278607ee9ca40_130.png)

![](img/a934331aa9f05b58d42278607ee9ca40_132.png)

![](img/a934331aa9f05b58d42278607ee9ca40_134.png)

def neutralize(factor_series, market_cap_series):
    """
    对因子进行市值中性化处理
    :param factor_series: 待中性化的因子序列（如市净率）
    :param market_cap_series: 市值序列
    :return: 中性化后的因子序列（残差）
    """
    # 将数据转换为float类型以满足模型要求
    Y = factor_series.astype(float)
    X = market_cap_series.astype(float)
    
    # 添加常数项，用于拟合截距b
    X = sm.add_constant(X)
    
    # 建立并拟合OLS线性回归模型
    model = sm.OLS(Y, X).fit()
    
    # 返回残差，即中性化后的因子值
    return model.resid
```

![](img/a934331aa9f05b58d42278607ee9ca40_136.png)

![](img/a934331aa9f05b58d42278607ee9ca40_138.png)

![](img/a934331aa9f05b58d42278607ee9ca40_140.png)

**核心概念解释**：`model.resid` 属性直接返回了回归模型的残差，它等价于 `factor_series - model.predict(X)`，即真实值减去模型预测值，完美实现了中性化的目标。

![](img/a934331aa9f05b58d42278607ee9ca40_142.png)

![](img/a934331aa9f05b58d42278607ee9ca40_144.png)

![](img/a934331aa9f05b58d42278607ee9ca40_146.png)

---

![](img/a934331aa9f05b58d42278607ee9ca40_148.png)

![](img/a934331aa9f05b58d42278607ee9ca40_150.png)

![](img/a934331aa9f05b58d42278607ee9ca40_152.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤：**去极值**、**标准化**和**中性化**。我们不仅理解了每个步骤的目的，还动手实现了对应的函数。通过这三步处理，原始的因子数据变得更加干净、可比且独立，为后续构建有效的量化交易模型做好了充分准备。记住，良好的数据预处理是成功建模的一半。