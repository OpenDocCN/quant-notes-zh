# 量化交易：P37：因子数据预处理 📊

![](img/2c053eee64d133a2509d966680a1f1ca_0.png)

![](img/2c053eee64d133a2509d966680a1f1ca_2.png)

![](img/2c053eee64d133a2509d966680a1f1ca_4.png)

![](img/2c053eee64d133a2509d966680a1f1ca_6.png)

![](img/2c053eee64d133a2509d966680a1f1ca_8.png)

在本节课中，我们将要学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能帮助我们获得更可靠、更具可比性的因子，为后续的量化模型构建打下坚实基础。

![](img/2c053eee64d133a2509d966680a1f1ca_10.png)

![](img/2c053eee64d133a2509d966680a1f1ca_12.png)

![](img/2c053eee64d133a2509d966680a1f1ca_14.png)

![](img/2c053eee64d133a2509d966680a1f1ca_16.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/2c053eee64d133a2509d966680a1f1ca_18.png)

![](img/2c053eee64d133a2509d966680a1f1ca_20.png)

![](img/2c053eee64d133a2509d966680a1f1ca_22.png)

![](img/2c053eee64d133a2509d966680a1f1ca_24.png)

## 第一步：去极值 🧹

![](img/2c053eee64d133a2509d966680a1f1ca_26.png)

![](img/2c053eee64d133a2509d966680a1f1ca_28.png)

![](img/2c053eee64d133a2509d966680a1f1ca_30.png)

去极值，也称为处理离群值，目的是消除数据中异常大或异常小的极端值，防止它们对后续分析产生过度影响。我们将使用“三西格玛”法则来实现。

![](img/2c053eee64d133a2509d966680a1f1ca_32.png)

![](img/2c053eee64d133a2509d966680a1f1ca_34.png)

![](img/2c053eee64d133a2509d966680a1f1ca_36.png)

![](img/2c053eee64d133a2509d966680a1f1ca_38.png)

**三西格玛法则公式**：
对于一个服从正态分布的数据集，其数值落在均值加减三倍标准差（`μ ± 3σ`）范围内的概率超过99.7%。我们将此范围外的值视为极值并进行处理。

![](img/2c053eee64d133a2509d966680a1f1ca_40.png)

![](img/2c053eee64d133a2509d966680a1f1ca_42.png)

![](img/2c053eee64d133a2509d966680a1f1ca_44.png)

![](img/2c053eee64d133a2509d966680a1f1ca_46.png)

以下是去极值操作的实现代码：

![](img/2c053eee64d133a2509d966680a1f1ca_48.png)

![](img/2c053eee64d133a2509d966680a1f1ca_50.png)

![](img/2c053eee64d133a2509d966680a1f1ca_52.png)

![](img/2c053eee64d133a2509d966680a1f1ca_54.png)

```python
def filter_extreme_3sigma(series, n=3):
    """
    使用三西格玛法则去除极值。
    :param series: 待处理的因子数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    # 计算均值和标准差
    mean = series.mean()
    std = series.std()
    
    # 计算上限和下限
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    
    # 将超出范围的值截断到边界
    return series.clip(lower=lower_limit, upper=upper_limit)
```

![](img/2c053eee64d133a2509d966680a1f1ca_56.png)

![](img/2c053eee64d133a2509d966680a1f1ca_58.png)

![](img/2c053eee64d133a2509d966680a1f1ca_60.png)

![](img/2c053eee64d133a2509d966680a1f1ca_62.png)

## 第二步：标准化 📏

![](img/2c053eee64d133a2509d966680a1f1ca_64.png)

![](img/2c053eee64d133a2509d966680a1f1ca_66.png)

![](img/2c053eee64d133a2509d966680a1f1ca_68.png)

标准化是将数据按比例缩放，使其落入一个特定的标准区间（通常是均值为0，标准差为1），从而消除不同因子之间量纲和数值范围的差异，使它们具有可比性。

![](img/2c053eee64d133a2509d966680a1f1ca_70.png)

![](img/2c053eee64d133a2509d966680a1f1ca_72.png)

![](img/2c053eee64d133a2509d966680a1f1ca_74.png)

![](img/2c053eee64d133a2509d966680a1f1ca_76.png)

**标准化公式**：
`z = (x - μ) / σ`
其中，`x`是原始值，`μ`是均值，`σ`是标准差。

![](img/2c053eee64d133a2509d966680a1f1ca_78.png)

![](img/2c053eee64d133a2509d966680a1f1ca_80.png)

![](img/2c053eee64d133a2509d966680a1f1ca_82.png)

![](img/2c053eee64d133a2509d966680a1f1ca_84.png)

以下是标准化操作的实现代码：

![](img/2c053eee64d133a2509d966680a1f1ca_86.png)

![](img/2c053eee64d133a2509d966680a1f1ca_88.png)

![](img/2c053eee64d133a2509d966680a1f1ca_90.png)

```python
def standardize(series):
    """
    对数据进行标准化处理。
    :param series: 待处理的因子数据序列
    :return: 标准化后的数据序列
    """
    # 计算均值和标准差
    mean = series.mean()
    std = series.std()
    
    # 应用标准化公式
    return (series - mean) / std
```

![](img/2c053eee64d133a2509d966680a1f1ca_92.png)

![](img/2c053eee64d133a2509d966680a1f1ca_94.png)

![](img/2c053eee64d133a2509d966680a1f1ca_96.png)

## 第三步：中性化 🧪

![](img/2c053eee64d133a2509d966680a1f1ca_98.png)

![](img/2c053eee64d133a2509d966680a1f1ca_100.png)

![](img/2c053eee64d133a2509d966680a1f1ca_102.png)

![](img/2c053eee64d133a2509d966680a1f1ca_104.png)

中性化是为了消除因子数据中与其他已知变量（如市值）的相关性，从而提取出因子“纯粹”的预测能力。我们通过线性回归来实现，用因子值对市值进行回归，然后取回归的残差作为中性化后的因子值。

![](img/2c053eee64d133a2509d966680a1f1ca_106.png)

![](img/2c053eee64d133a2509d966680a1f1ca_108.png)

![](img/2c053eee64d133a2509d966680a1f1ca_110.png)

以下是中性化操作的实现步骤：

![](img/2c053eee64d133a2509d966680a1f1ca_112.png)

![](img/2c053eee64d133a2509d966680a1f1ca_114.png)

![](img/2c053eee64d133a2509d966680a1f1ca_116.png)

![](img/2c053eee64d133a2509d966680a1f1ca_118.png)

1.  建立回归模型：`因子值 = α + β * 市值 + ε`
2.  计算残差 `ε`，即中性化后的因子值。

![](img/2c053eee64d133a2509d966680a1f1ca_120.png)

![](img/2c053eee64d133a2509d966680a1f1ca_122.png)

![](img/2c053eee64d133a2509d966680a1f1ca_124.png)

以下是使用 `statsmodels` 库实现中性化的代码：

![](img/2c053eee64d133a2509d966680a1f1ca_126.png)

![](img/2c053eee64d133a2509d966680a1f1ca_128.png)

![](img/2c053eee64d133a2509d966680a1f1ca_130.png)

```python
import statsmodels.api as sm

![](img/2c053eee64d133a2509d966680a1f1ca_132.png)

![](img/2c053eee64d133a2509d966680a1f1ca_134.png)

![](img/2c053eee64d133a2509d966680a1f1ca_136.png)

def neutralize(factor_series, market_cap_series):
    """
    对因子进行市值中性化处理。
    :param factor_series: 待中性化的因子数据序列（Y）
    :param market_cap_series: 市值数据序列（X）
    :return: 中性化后的因子数据序列（残差）
    """
    # 确保数据类型为浮点型
    Y = factor_series.astype(float)
    X = market_cap_series.astype(float)
    
    # 添加常数项（截距）
    X = sm.add_constant(X)
    
    # 使用普通最小二乘法进行线性回归
    model = sm.OLS(Y, X).fit()
    
    # 返回回归残差，即中性化后的因子
    return model.resid
```

![](img/2c053eee64d133a2509d966680a1f1ca_138.png)

![](img/2c053eee64d133a2509d966680a1f1ca_140.png)

![](img/2c053eee64d133a2509d966680a1f1ca_142.png)

![](img/2c053eee64d133a2509d966680a1f1ca_144.png)

## 综合应用与流程总结 🎯

![](img/2c053eee64d133a2509d966680a1f1ca_146.png)

![](img/2c053eee64d133a2509d966680a1f1ca_148.png)

![](img/2c053eee64d133a2509d966680a1f1ca_150.png)

本节课中我们一起学习了因子数据预处理的完整流程。首先，我们使用**三西格玛法**去除数据中的极端值，保护模型不受异常点干扰。接着，通过**标准化**处理，统一了不同因子的量纲，使它们可以公平地进行比较和加权。最后，利用线性回归进行了**市值中性化**，剥离了市值对因子表现的影响，从而得到了更能反映股票自身特性的“纯净”因子。

![](img/2c053eee64d133a2509d966680a1f1ca_152.png)

![](img/2c053eee64d133a2509d966680a1f1ca_154.png)

![](img/2c053eee64d133a2509d966680a1f1ca_156.png)

![](img/2c053eee64d133a2509d966680a1f1ca_158.png)

这三个步骤是构建稳健量化策略的基础，经过预处理后的因子数据将更适用于多因子模型合成、因子测试和策略回测等后续环节。