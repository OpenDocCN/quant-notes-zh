# Python金融分析+量化交易：P37：因子数据预处理 📊

![](img/53a5410a030e6214d588f52085b588b4_0.png)

![](img/53a5410a030e6214d588f52085b588b4_2.png)

![](img/53a5410a030e6214d588f52085b588b4_4.png)

![](img/53a5410a030e6214d588f52085b588b4_6.png)

在本节课中，我们将学习因子数据预处理的核心三步操作：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，是构建有效量化模型的基础。

![](img/53a5410a030e6214d588f52085b588b4_8.png)

![](img/53a5410a030e6214d588f52085b588b4_10.png)

![](img/53a5410a030e6214d588f52085b588b4_12.png)

## 去极值操作 🧹

![](img/53a5410a030e6214d588f52085b588b4_14.png)

![](img/53a5410a030e6214d588f52085b588b4_16.png)

![](img/53a5410a030e6214d588f52085b588b4_18.png)

上一节我们介绍了如何查询因子指标，本节中我们来看看如何对查询到的数据进行预处理。第一步是去除数据中的极端值，也称为离群值。

![](img/53a5410a030e6214d588f52085b588b4_20.png)

![](img/53a5410a030e6214d588f52085b588b4_22.png)

![](img/53a5410a030e6214d588f52085b588b4_24.png)

我们采用“三西格玛”方法来实现去极值。该方法基于数据的均值和标准差，将超出特定范围的值进行截断处理。

![](img/53a5410a030e6214d588f52085b588b4_26.png)

![](img/53a5410a030e6214d588f52085b588b4_28.png)

![](img/53a5410a030e6214d588f52085b588b4_30.png)

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/53a5410a030e6214d588f52085b588b4_32.png)

![](img/53a5410a030e6214d588f52085b588b4_34.png)

1.  计算传入数据序列的均值 `mean` 和标准差 `std`。
2.  设定上限 `upper_limit` 为 `mean + 3 * std`。
3.  设定下限 `lower_limit` 为 `mean - 3 * std`。
4.  使用 `np.clip` 函数将序列中超出 `[lower_limit, upper_limit]` 范围的值替换为边界值。

![](img/53a5410a030e6214d588f52085b588b4_36.png)

![](img/53a5410a030e6214d588f52085b588b4_38.png)

```python
import numpy as np

![](img/53a5410a030e6214d588f52085b588b4_40.png)

![](img/53a5410a030e6214d588f52085b588b4_42.png)

![](img/53a5410a030e6214d588f52085b588b4_44.png)

def filter_three_sigma(series, n=3):
    """
    使用三西格玛法去除极值
    :param series: 待处理的数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    mean = series.mean()
    std = series.std()
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    return np.clip(series, lower_limit, upper_limit)
```

![](img/53a5410a030e6214d588f52085b588b4_46.png)

![](img/53a5410a030e6214d588f52085b588b4_48.png)

![](img/53a5410a030e6214d588f52085b588b4_50.png)

## 标准化操作 📏

![](img/53a5410a030e6214d588f52085b588b4_52.png)

![](img/53a5410a030e6214d588f52085b588b4_54.png)

![](img/53a5410a030e6214d588f52085b588b4_56.png)

完成去极值后，我们需要对数据进行标准化。标准化的目的是消除不同因子之间量纲和数量级的差异，使它们具有可比性。

![](img/53a5410a030e6214d588f52085b588b4_58.png)

![](img/53a5410a030e6214d588f52085b588b4_60.png)

![](img/53a5410a030e6214d588f52085b588b4_62.png)

标准化操作通常使用Z-Score方法，将数据转换为均值为0、标准差为1的分布。

![](img/53a5410a030e6214d588f52085b588b4_64.png)

![](img/53a5410a030e6214d588f52085b588b4_66.png)

以下是标准化函数 `standardize` 的实现步骤：

![](img/53a5410a030e6214d588f52085b588b4_68.png)

![](img/53a5410a030e6214d588f52085b588b4_70.png)

1.  计算传入数据序列的均值 `mean` 和标准差 `std`。
2.  对序列中的每个值，执行 `(value - mean) / std` 操作。

![](img/53a5410a030e6214d588f52085b588b4_72.png)

![](img/53a5410a030e6214d588f52085b588b4_74.png)

```python
def standardize(series):
    """
    对数据序列进行标准化（Z-Score）
    :param series: 待处理的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/53a5410a030e6214d588f52085b588b4_76.png)

![](img/53a5410a030e6214d588f52085b588b4_78.png)

![](img/53a5410a030e6214d588f52085b588b4_80.png)

## 中性化操作 ⚖️

![](img/53a5410a030e6214d588f52085b588b4_82.png)

![](img/53a5410a030e6214d588f52085b588b4_84.png)

![](img/53a5410a030e6214d588f52085b588b4_86.png)

最后一步是因子中性化。在金融分析中，许多因子可能受到市值等常见风险因素的影响。中性化的目标就是剔除这些共性影响，保留因子本身的特质信息。

![](img/53a5410a030e6214d588f52085b588b4_88.png)

![](img/53a5410a030e6214d588f52085b588b4_90.png)

我们通过线性回归来实现中性化。以“市净率”因子为例，我们将其对“市值”进行回归，然后取回归的残差作为中性化后的因子值。残差代表了无法被市值解释的部分，即“纯净”的因子暴露。

![](img/53a5410a030e6214d588f52085b588b4_92.png)

![](img/53a5410a030e6214d588f52085b588b4_94.png)

![](img/53a5410a030e6214d588f52085b588b4_96.png)

以下是中性化函数 `neutralize` 的实现步骤：

![](img/53a5410a030e6214d588f52085b588b4_98.png)

1.  导入 `statsmodels` 库的 `OLS`（普通最小二乘法）模块。
2.  将因子数据 `factor_series` 作为因变量 `Y`，将市值数据 `market_cap_series` 作为自变量 `X`。
3.  使用 `OLS` 拟合回归模型 `Y = α + β * X + ε`。
4.  获取模型的残差 `resid`，该残差即为中性化后的因子值。

![](img/53a5410a030e6214d588f52085b588b4_100.png)

![](img/53a5410a030e6214d588f52085b588b4_102.png)

```python
import statsmodels.api as sm

![](img/53a5410a030e6214d588f52085b588b4_104.png)

![](img/53a5410a030e6214d588f52085b588b4_106.png)

![](img/53a5410a030e6214d588f52085b588b4_108.png)

def neutralize(factor_series, market_cap_series):
    """
    对因子进行市值中性化处理
    :param factor_series: 待中性化的因子数据序列
    :param market_cap_series: 市值数据序列
    :return: 中性化后的因子数据序列（回归残差）
    """
    # 添加常数项（截距）
    X = sm.add_constant(market_cap_series.astype(float))
    Y = factor_series.astype(float)

    # 建立OLS回归模型并拟合
    model = sm.OLS(Y, X)
    result = model.fit()

    # 返回残差，即中性化后的因子
    return result.resid
```

![](img/53a5410a030e6214d588f52085b588b4_110.png)

![](img/53a5410a030e6214d588f52085b588b4_112.png)

## 总结 ✨

![](img/53a5410a030e6214d588f52085b588b4_114.png)

![](img/53a5410a030e6214d588f52085b588b4_116.png)

![](img/53a5410a030e6214d588f52085b588b4_117.png)

本节课中我们一起学习了因子数据预处理的完整流程。我们首先使用**三西格玛法**去除极端值，然后通过**Z-Score标准化**统一数据尺度，最后利用**线性回归**对因子进行市值中性化处理，以剥离共性风险的影响。这三步操作是准备高质量因子数据、构建稳健量化模型的关键基础。