# Python金融分析量化交易：P37：3-因子数据预处理 📊

![](img/894a4256731cb908f0cf746fdfd8475b_0.png)

![](img/894a4256731cb908f0cf746fdfd8475b_2.png)

![](img/894a4256731cb908f0cf746fdfd8475b_4.png)

![](img/894a4256731cb908f0cf746fdfd8475b_6.png)

![](img/894a4256731cb908f0cf746fdfd8475b_8.png)

在本节课中，我们将要学习因子数据预处理的核心三步操作：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，是构建有效量化模型的基础。

![](img/894a4256731cb908f0cf746fdfd8475b_10.png)

![](img/894a4256731cb908f0cf746fdfd8475b_12.png)

![](img/894a4256731cb908f0cf746fdfd8475b_14.png)

![](img/894a4256731cb908f0cf746fdfd8475b_16.png)

## 去极值操作

![](img/894a4256731cb908f0cf746fdfd8475b_18.png)

![](img/894a4256731cb908f0cf746fdfd8475b_20.png)

![](img/894a4256731cb908f0cf746fdfd8475b_22.png)

上一节我们介绍了如何查询因子数据，本节中我们来看看如何对数据进行预处理。第一步是去除数据中的极端值，也称为离群值。极端值可能会对后续的统计分析产生不良影响。

![](img/894a4256731cb908f0cf746fdfd8475b_24.png)

![](img/894a4256731cb908f0cf746fdfd8475b_26.png)

![](img/894a4256731cb908f0cf746fdfd8475b_28.png)

我们采用“三西格玛”方法来处理极值。该方法基于数据的均值和标准差，将超出特定范围的值进行截断或调整。

![](img/894a4256731cb908f0cf746fdfd8475b_30.png)

![](img/894a4256731cb908f0cf746fdfd8475b_32.png)

![](img/894a4256731cb908f0cf746fdfd8475b_34.png)

![](img/894a4256731cb908f0cf746fdfd8475b_36.png)

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/894a4256731cb908f0cf746fdfd8475b_38.png)

![](img/894a4256731cb908f0cf746fdfd8475b_40.png)

![](img/894a4256731cb908f0cf746fdfd8475b_42.png)

![](img/894a4256731cb908f0cf746fdfd8475b_44.png)

1.  计算传入数据序列的均值（`mean`）和标准差（`std`）。
2.  根据公式 **上限 = 均值 + 3 * 标准差** 和 **下限 = 均值 - 3 * 标准差** 确定正常值的范围。
3.  使用 `np.clip` 函数将序列中超出上下限的值替换为边界值，并返回处理后的序列。

![](img/894a4256731cb908f0cf746fdfd8475b_46.png)

![](img/894a4256731cb908f0cf746fdfd8475b_48.png)

![](img/894a4256731cb908f0cf746fdfd8475b_50.png)

```python
import numpy as np

![](img/894a4256731cb908f0cf746fdfd8475b_52.png)

![](img/894a4256731cb908f0cf746fdfd8475b_54.png)

![](img/894a4256731cb908f0cf746fdfd8475b_56.png)

![](img/894a4256731cb908f0cf746fdfd8475b_58.png)

def filter_three_sigma(series, n=3):
    mean = series.mean()
    std = series.std()
    upper = mean + n * std
    lower = mean - n * std
    return np.clip(series, lower, upper)
```

![](img/894a4256731cb908f0cf746fdfd8475b_60.png)

![](img/894a4256731cb908f0cf746fdfd8475b_62.png)

![](img/894a4256731cb908f0cf746fdfd8475b_64.png)

## 标准化操作

![](img/894a4256731cb908f0cf746fdfd8475b_66.png)

![](img/894a4256731cb908f0cf746fdfd8475b_68.png)

![](img/894a4256731cb908f0cf746fdfd8475b_70.png)

![](img/894a4256731cb908f0cf746fdfd8475b_72.png)

完成了去极值后，我们需要对数据进行标准化。标准化的目的是消除不同因子之间量纲和数量级的差异，使它们具有可比性。

![](img/894a4256731cb908f0cf746fdfd8475b_74.png)

![](img/894a4256731cb908f0cf746fdfd8475b_76.png)

![](img/894a4256731cb908f0cf746fdfd8475b_78.png)

标准化通常采用Z-Score方法，将数据转换为均值为0、标准差为1的分布。

![](img/894a4256731cb908f0cf746fdfd8475b_80.png)

![](img/894a4256731cb908f0cf746fdfd8475b_82.png)

![](img/894a4256731cb908f0cf746fdfd8475b_84.png)

![](img/894a4256731cb908f0cf746fdfd8475b_86.png)

以下是标准化函数 `standardize` 的实现步骤：

![](img/894a4256731cb908f0cf746fdfd8475b_88.png)

![](img/894a4256731cb908f0cf746fdfd8475b_90.png)

![](img/894a4256731cb908f0cf746fdfd8475b_92.png)

1.  同样计算数据序列的均值（`mean`）和标准差（`std`）。
2.  对序列中的每个值应用公式 **(值 - 均值) / 标准差**。
3.  返回标准化后的序列。

![](img/894a4256731cb908f0cf746fdfd8475b_94.png)

![](img/894a4256731cb908f0cf746fdfd8475b_96.png)

![](img/894a4256731cb908f0cf746fdfd8475b_98.png)

```python
def standardize(series):
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/894a4256731cb908f0cf746fdfd8475b_100.png)

![](img/894a4256731cb908f0cf746fdfd8475b_102.png)

![](img/894a4256731cb908f0cf746fdfd8475b_104.png)

## 中性化操作

![](img/894a4256731cb908f0cf746fdfd8475b_106.png)

![](img/894a4256731cb908f0cf746fdfd8475b_108.png)

![](img/894a4256731cb908f0cf746fdfd8475b_110.png)

最后一步是中性化处理。在金融分析中，许多因子可能受到市值等常见风险因素的影响。中性化的目的就是剔除这些共性因素的影响，从而得到因子本身的“纯净”预测能力。

![](img/894a4256731cb908f0cf746fdfd8475b_112.png)

![](img/894a4256731cb908f0cf746fdfd8475b_114.png)

![](img/894a4256731cb908f0cf746fdfd8475b_116.png)

我们通过线性回归来实现中性化。以“市净率”因子为例，我们将其对“市值”进行回归，然后取回归的残差作为中性化后的因子值。残差代表了无法被市值解释的部分，即因子独立于市值的影响。

![](img/894a4256731cb908f0cf746fdfd8475b_118.png)

![](img/894a4256731cb908f0cf746fdfd8475b_120.png)

![](img/894a4256731cb908f0cf746fdfd8475b_122.png)

![](img/894a4256731cb908f0cf746fdfd8475b_124.png)

以下是中性化函数 `neutralize` 的实现步骤：

![](img/894a4256731cb908f0cf746fdfd8475b_126.png)

![](img/894a4256731cb908f0cf746fdfd8475b_128.png)

1.  导入 `statsmodels` 库，使用其普通最小二乘法（OLS）进行线性回归。
2.  将待处理的因子序列作为因变量（Y），将需要剔除影响的变量（如市值）作为自变量（X）。
3.  拟合回归模型后，直接获取模型的残差（`resid` 属性）。这个残差序列就是中性化后的因子值。

![](img/894a4256731cb908f0cf746fdfd8475b_130.png)

![](img/894a4256731cb908f0cf746fdfd8475b_132.png)

![](img/894a4256731cb908f0cf746fdfd8475b_134.png)

![](img/894a4256731cb908f0cf746fdfd8475b_136.png)

```python
import statsmodels.api as sm

![](img/894a4256731cb908f0cf746fdfd8475b_138.png)

![](img/894a4256731cb908f0cf746fdfd8475b_140.png)

![](img/894a4256731cb908f0cf746fdfd8475b_142.png)

def neutralize(factor_series, market_cap_series):
    # 确保数据类型为浮点型
    y = factor_series.astype(float)
    x = market_cap_series.astype(float)
    # 添加常数项（截距）
    x = sm.add_constant(x)
    # 建立OLS模型并拟合
    model = sm.OLS(y, x).fit()
    # 返回残差，即中性化后的因子
    return model.resid
```

![](img/894a4256731cb908f0cf746fdfd8475b_144.png)

![](img/894a4256731cb908f0cf746fdfd8475b_146.png)

![](img/894a4256731cb908f0cf746fdfd8475b_148.png)

## 总结

![](img/894a4256731cb908f0cf746fdfd8475b_150.png)

![](img/894a4256731cb908f0cf746fdfd8475b_152.png)

![](img/894a4256731cb908f0cf746fdfd8475b_154.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤。首先，我们使用**三西格玛法**去除极端值；其次，通过**Z-Score标准化**统一数据的尺度；最后，利用**线性回归进行中性化**，剔除市值等共性因素的影响，得到更“纯净”的因子。这三步是构建可靠量化因子、进行有效多因子分析的重要基础。