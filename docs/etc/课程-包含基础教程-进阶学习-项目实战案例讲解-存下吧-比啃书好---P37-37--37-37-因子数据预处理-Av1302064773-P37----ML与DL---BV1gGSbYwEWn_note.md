# 量化交易教程：P37：因子数据预处理 📊

![](img/f97ab1060618eeb5a4f6db620e3d6b45_0.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_2.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_4.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_6.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_8.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_10.png)

在本节课中，我们将要学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续量化模型的稳定性和准确性。

![](img/f97ab1060618eeb5a4f6db620e3d6b45_12.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_14.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_16.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_18.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_20.png)

上一节我们介绍了如何查询因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/f97ab1060618eeb5a4f6db620e3d6b45_22.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_24.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_26.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_28.png)

## 第一步：去极值 🧹

![](img/f97ab1060618eeb5a4f6db620e3d6b45_30.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_32.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_34.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_36.png)

去极值（或称去离群值）的目的是处理数据中异常大或异常小的值，防止它们对整体分析产生过大影响。我们将使用“三西格玛”方法来实现。

![](img/f97ab1060618eeb5a4f6db620e3d6b45_38.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_40.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_42.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_44.png)

以下是去极值操作的实现步骤：

![](img/f97ab1060618eeb5a4f6db620e3d6b45_46.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_48.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_50.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_52.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_54.png)

1.  计算传入数据序列的均值（`mean`）和标准差（`std`）。
2.  根据公式 **上限 = 均值 + N * 标准差** 和 **下限 = 均值 - N * 标准差** 计算正常值的范围。通常N取3。
3.  使用`np.clip`函数将序列中超出上下限的值“裁剪”到边界上，而不是直接删除。

![](img/f97ab1060618eeb5a4f6db620e3d6b45_56.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_58.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_60.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_62.png)

```python
def filter_three_sigma(series, n=3):
    mean = series.mean()
    std = series.std()
    upper = mean + n * std
    lower = mean - n * std
    return np.clip(series, lower, upper)
```

![](img/f97ab1060618eeb5a4f6db620e3d6b45_64.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_66.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_68.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_70.png)

## 第二步：标准化 📏

![](img/f97ab1060618eeb5a4f6db620e3d6b45_72.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_74.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_76.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_78.png)

标准化（Standardization）的目的是将不同量纲或量级的指标数据转换到统一的尺度上，使其具有可比性。我们采用Z-Score标准化方法。

![](img/f97ab1060618eeb5a4f6db620e3d6b45_80.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_82.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_84.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_86.png)

以下是标准化操作的实现步骤：

![](img/f97ab1060618eeb5a4f6db620e3d6b45_88.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_90.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_92.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_94.png)

1.  同样计算数据序列的均值（`mean`）和标准差（`std`）。
2.  对序列中的每一个值，应用公式 **(值 - 均值) / 标准差**。
3.  处理后的数据将符合均值为0、标准差为1的标准正态分布。

![](img/f97ab1060618eeb5a4f6db620e3d6b45_96.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_98.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_100.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_102.png)

```python
def standardize(series):
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/f97ab1060618eeb5a4f6db620e3d6b45_104.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_106.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_108.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_110.png)

## 第三步：中性化 ⚖️

![](img/f97ab1060618eeb5a4f6db620e3d6b45_112.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_114.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_116.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_118.png)

中性化（Neutralization）的目的是消除某些系统性因素（如市值）对因子本身的影响，从而得到更“纯净”的因子暴露。我们通过线性回归的方法来实现。

![](img/f97ab1060618eeb5a4f6db620e3d6b45_120.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_122.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_124.png)

以下是中性化操作的实现步骤：

![](img/f97ab1060618eeb5a4f6db620e3d6b45_126.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_128.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_130.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_132.png)

1.  明确自变量（X）和因变量（Y）。例如，我们希望消除市值对市净率（PB）因子的影响，那么**市值是X，市净率因子值是Y**。
2.  使用`statsmodels`库的普通最小二乘法（OLS）建立Y对X的线性回归模型。
3.  模型拟合后，其残差（resid）即为去除X（市值）影响后的“中性化”因子值。

![](img/f97ab1060618eeb5a4f6db620e3d6b45_134.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_136.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_138.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_140.png)

```python
import statsmodels.api as sm
import numpy as np

![](img/f97ab1060618eeb5a4f6db620e3d6b45_142.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_144.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_146.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_148.png)

def neutralize(factor, market_cap):
    # Y: 需要中性化的因子 (如PB)
    y = np.array(factor).astype(float)
    # X: 需要被消除影响的变量 (如市值)，并添加常数项
    X = sm.add_constant(np.array(market_cap).astype(float))

    # 建立OLS回归模型并拟合
    model = sm.OLS(y, X)
    result = model.fit()

    # 返回残差，即中性化后的因子值
    return result.resid
```

![](img/f97ab1060618eeb5a4f6db620e3d6b45_150.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_152.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_154.png)

![](img/f97ab1060618eeb5a4f6db620e3d6b45_156.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤。首先，我们使用**三西格玛法**去除极端值的影响；接着，通过**Z-Score标准化**将数据转换到统一尺度；最后，利用**线性回归中性化**消除特定系统性风险（如市值）对因子的干扰。掌握这些预处理技术是构建稳健量化模型的重要基础。