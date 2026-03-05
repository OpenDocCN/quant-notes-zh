# Python金融分析与量化交易实战：P34：因子数据预处理

![](img/976994c0eebcad45164c94a209ea1b6d_0.png)

![](img/976994c0eebcad45164c94a209ea1b6d_2.png)

![](img/976994c0eebcad45164c94a209ea1b6d_4.png)

![](img/976994c0eebcad45164c94a209ea1b6d_6.png)

![](img/976994c0eebcad45164c94a209ea1b6d_8.png)

在本节课中，我们将学习因子数据预处理的核心步骤。因子数据是量化交易模型的基础，但原始数据通常包含极端值、量纲差异和行业/市值等风格暴露。为了构建稳健的模型，我们必须对因子数据进行清洗和标准化处理。本节课将详细介绍去极值、标准化和中性化这三个关键步骤。

![](img/976994c0eebcad45164c94a209ea1b6d_10.png)

![](img/976994c0eebcad45164c94a209ea1b6d_12.png)

![](img/976994c0eebcad45164c94a209ea1b6d_14.png)

## 去极值处理

![](img/976994c0eebcad45164c94a209ea1b6d_16.png)

![](img/976994c0eebcad45164c94a209ea1b6d_18.png)

上一节我们介绍了如何获取因子数据，本节中我们来看看如何处理数据中的极端值。极端值（或称离群值）会严重影响模型的稳定性，因此第一步是进行去极值处理。我们采用简单有效的**三西格玛（3-Sigma）** 方法。

![](img/976994c0eebcad45164c94a209ea1b6d_20.png)

![](img/976994c0eebcad45164c94a209ea1b6d_22.png)

![](img/976994c0eebcad45164c94a209ea1b6d_24.png)

以下是去极值操作的实现步骤：

![](img/976994c0eebcad45164c94a209ea1b6d_26.png)

![](img/976994c0eebcad45164c94a209ea1b6d_28.png)

![](img/976994c0eebcad45164c94a209ea1b6d_30.png)

1.  计算因子数据的均值（`mean`）和标准差（`std`）。
2.  设定上限和下限：`上限 = 均值 + 3 * 标准差`，`下限 = 均值 - 3 * 标准差`。
3.  将超出上限或下限的值，分别替换为上限值或下限值，而不是直接删除。

![](img/976994c0eebcad45164c94a209ea1b6d_32.png)

![](img/976994c0eebcad45164c94a209ea1b6d_34.png)

```python
import numpy as np

![](img/976994c0eebcad45164c94a209ea1b6d_36.png)

![](img/976994c0eebcad45164c94a209ea1b6d_38.png)

def filter_three_sigma(series, n=3):
    """
    使用三西格玛法去除极值
    :param series: 待处理的因子数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    mean = series.mean()
    std = series.std()
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    # 使用np.clip将超出范围的值截断到边界
    return np.clip(series, lower_limit, upper_limit)
```

![](img/976994c0eebcad45164c94a209ea1b6d_40.png)

![](img/976994c0eebcad45164c94a209ea1b6d_42.png)

![](img/976994c0eebcad45164c94a209ea1b6d_44.png)

## 标准化处理

![](img/976994c0eebcad45164c94a209ea1b6d_46.png)

![](img/976994c0eebcad45164c94a209ea1b6d_48.png)

完成去极值后，我们需要解决不同因子之间量纲不同的问题。标准化（Z-Score标准化）可以将数据转换为均值为0、标准差为1的分布，便于不同因子间的比较和合成。

![](img/976994c0eebcad45164c94a209ea1b6d_50.png)

![](img/976994c0eebcad45164c94a209ea1b6d_52.png)

![](img/976994c0eebcad45164c94a209ea1b6d_54.png)

以下是标准化操作的实现步骤：

![](img/976994c0eebcad45164c94a209ea1b6d_56.png)

![](img/976994c0eebcad45164c94a209ea1b6d_58.png)

![](img/976994c0eebcad45164c94a209ea1b6d_60.png)

1.  计算因子数据的均值（`mean`）和标准差（`std`）。
2.  对每个数据点应用公式：`(原始值 - 均值) / 标准差`。

```python
def standardize_series(series):
    """
    对数据进行Z-Score标准化
    :param series: 待标准化的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/976994c0eebcad45164c94a209ea1b6d_62.png)

![](img/976994c0eebcad45164c94a209ea1b6d_64.png)

![](img/976994c0eebcad45164c94a209ea1b6d_66.png)

## 中性化处理

![](img/976994c0eebcad45164c94a209ea1b6d_68.png)

![](img/976994c0eebcad45164c94a209ea1b6d_70.png)

最后一步是中性化处理。一个因子可能同时受到其自身效应和市值、行业等其他风格因素的影响。中性化的目的是剔除这些风格因素的影响，得到因子“纯粹”的收益预测能力。我们通常使用线性回归方法。

![](img/976994c0eebcad45164c94a209ea1b6d_72.png)

![](img/976994c0eebcad45164c94a209ea1b6d_74.png)

![](img/976994c0eebcad45164c94a209ea1b6d_76.png)

以下是中性化操作的实现步骤：

![](img/976994c0eebcad45164c94a209ea1b6d_78.png)

1.  确定因变量（Y）和自变量（X）。例如，我们要剔除市值对市净率（PB）因子的影响，则Y为PB因子值，X为市值。
2.  使用最小二乘法（OLS）拟合线性回归模型：`Y = W * X + b`。
3.  计算残差（`resid`），即 `真实Y值 - 模型预测的Y值`。这个残差就是剔除了市值影响后的“纯净”因子值。

![](img/976994c0eebcad45164c94a209ea1b6d_80.png)

```python
import statsmodels.api as sm

![](img/976994c0eebcad45164c94a209ea1b6d_82.png)

![](img/976994c0eebcad45164c94a209ea1b6d_84.png)

def neutralize_series(factor_series, style_series):
    """
    对因子进行中性化处理，剔除指定风格因素的影响
    :param factor_series: 待中性化的因子数据（Y）
    :param style_series: 风格因子数据，如市值（X）
    :return: 中性化后的因子数据（残差）
    """
    # 为X添加常数项，用于拟合截距b
    X = sm.add_constant(style_series.astype(float))
    Y = factor_series.astype(float)
    # 使用OLS进行线性回归
    model = sm.OLS(Y, X).fit()
    # 返回残差，即中性化后的因子
    return model.resid
```

![](img/976994c0eebcad45164c94a209ea1b6d_86.png)

![](img/976994c0eebcad45164c94a209ea1b6d_88.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤：**去极值**、**标准化**和**中性化**。通过这三个步骤，我们可以将原始的、粗糙的因子数据转化为干净、可比、纯净的标准化因子，为后续的因子合成、选股模型构建打下坚实的基础。在实际操作中，这三个步骤通常按顺序依次执行。