# Python金融量化+股票交易：P37：因子数据预处理 📊

![](img/b863dd34f01e749714cbe694f179865b_0.png)

![](img/b863dd34f01e749714cbe694f179865b_2.png)

![](img/b863dd34f01e749714cbe694f179865b_4.png)

![](img/b863dd34f01e749714cbe694f179865b_6.png)

![](img/b863dd34f01e749714cbe694f179865b_8.png)

在本节课中，我们将要学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续量化模型分析的准确性和稳定性。

![](img/b863dd34f01e749714cbe694f179865b_10.png)

![](img/b863dd34f01e749714cbe694f179865b_12.png)

![](img/b863dd34f01e749714cbe694f179865b_14.png)

![](img/b863dd34f01e749714cbe694f179865b_16.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/b863dd34f01e749714cbe694f179865b_18.png)

![](img/b863dd34f01e749714cbe694f179865b_20.png)

![](img/b863dd34f01e749714cbe694f179865b_22.png)

## 去极值处理 🧹

![](img/b863dd34f01e749714cbe694f179865b_24.png)

![](img/b863dd34f01e749714cbe694f179865b_26.png)

![](img/b863dd34f01e749714cbe694f179865b_28.png)

去极值，也称为离群值处理，旨在消除数据中异常极端值的影响。我们采用“三西格玛”方法来实现。

![](img/b863dd34f01e749714cbe694f179865b_30.png)

![](img/b863dd34f01e749714cbe694f179865b_32.png)

![](img/b863dd34f01e749714cbe694f179865b_34.png)

**三西格玛法原理**：假设数据服从正态分布，将超出均值±3倍标准差范围的值视为异常值，并将其调整到边界值上。

![](img/b863dd34f01e749714cbe694f179865b_36.png)

![](img/b863dd34f01e749714cbe694f179865b_38.png)

![](img/b863dd34f01e749714cbe694f179865b_40.png)

![](img/b863dd34f01e749714cbe694f179865b_42.png)

以下是去极值操作的实现步骤：

![](img/b863dd34f01e749714cbe694f179865b_44.png)

![](img/b863dd34f01e749714cbe694f179865b_46.png)

![](img/b863dd34f01e749714cbe694f179865b_48.png)

1.  计算数据的均值（`mean`）和标准差（`std`）。
2.  设定上限（`upper_limit`）和下限（`lower_limit`）。
    *   **上限公式**：`upper_limit = mean + n * std`
    *   **下限公式**：`lower_limit = mean - n * std`
    *   其中 `n` 通常取 3。
3.  使用 `np.clip` 函数将超出界限的数据值调整到边界上。

![](img/b863dd34f01e749714cbe694f179865b_50.png)

![](img/b863dd34f01e749714cbe694f179865b_52.png)

![](img/b863dd34f01e749714cbe694f179865b_54.png)

```python
import numpy as np

![](img/b863dd34f01e749714cbe694f179865b_56.png)

![](img/b863dd34f01e749714cbe694f179865b_58.png)

![](img/b863dd34f01e749714cbe694f179865b_60.png)

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

![](img/b863dd34f01e749714cbe694f179865b_62.png)

![](img/b863dd34f01e749714cbe694f179865b_64.png)

![](img/b863dd34f01e749714cbe694f179865b_66.png)

![](img/b863dd34f01e749714cbe694f179865b_68.png)

## 标准化处理 📏

![](img/b863dd34f01e749714cbe694f179865b_70.png)

![](img/b863dd34f01e749714cbe694f179865b_72.png)

![](img/b863dd34f01e749714cbe694f179865b_74.png)

标准化（Standardization）的目的是将不同量纲或量级的指标数据转换为具有相同标准尺度的数据，便于比较和分析。我们采用Z-Score标准化方法。

![](img/b863dd34f01e749714cbe694f179865b_76.png)

![](img/b863dd34f01e749714cbe694f179865b_78.png)

![](img/b863dd34f01e749714cbe694f179865b_80.png)

**Z-Score标准化公式**：`z = (x - μ) / σ`
其中，`x` 是原始值，`μ` 是均值，`σ` 是标准差。

![](img/b863dd34f01e749714cbe694f179865b_82.png)

![](img/b863dd34f01e749714cbe694f179865b_84.png)

![](img/b863dd34f01e749714cbe694f179865b_86.png)

![](img/b863dd34f01e749714cbe694f179865b_88.png)

以下是标准化操作的实现步骤：

![](img/b863dd34f01e749714cbe694f179865b_90.png)

![](img/b863dd34f01e749714cbe694f179865b_92.png)

![](img/b863dd34f01e749714cbe694f179865b_94.png)

1.  计算数据的均值（`mean`）和标准差（`std`）。
2.  对序列中的每个值应用Z-Score公式。

![](img/b863dd34f01e749714cbe694f179865b_96.png)

![](img/b863dd34f01e749714cbe694f179865b_98.png)

```python
def standardize(series):
    """
    对数据进行Z-Score标准化
    :param series: 待处理的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/b863dd34f01e749714cbe694f179865b_100.png)

![](img/b863dd34f01e749714cbe694f179865b_102.png)

![](img/b863dd34f01e749714cbe694f179865b_104.png)

## 中性化处理 ⚖️

![](img/b863dd34f01e749714cbe694f179865b_106.png)

![](img/b863dd34f01e749714cbe694f179865b_108.png)

![](img/b863dd34f01e749714cbe694f179865b_110.png)

中性化（Neutralization）是为了消除某些系统性风险因子（如市值）对目标因子的影响，从而得到更“纯净”的因子暴露。我们通过线性回归来实现。

![](img/b863dd34f01e749714cbe694f179865b_112.png)

![](img/b863dd34f01e749714cbe694f179865b_114.png)

![](img/b863dd34f01e749714cbe694f179865b_116.png)

**中性化原理**：以目标因子（如市净率）为因变量 `Y`，以需要剔除影响的因子（如市值）为自变量 `X`，建立线性回归模型 `Y = aX + b + ε`。回归后的残差 `ε` 即为剔除了市值影响后的中性化因子值。

![](img/b863dd34f01e749714cbe694f179865b_118.png)

![](img/b863dd34f01e749714cbe694f179865b_120.png)

以下是中性化操作的实现步骤：

![](img/b863dd34f01e749714cbe694f179865b_122.png)

![](img/b863dd34f01e749714cbe694f179865b_124.png)

![](img/b863dd34f01e749714cbe694f179865b_126.png)

1.  导入 `statsmodels` 库进行回归分析。
2.  将目标因子数据（`factor`）作为因变量 `Y`。
3.  将需要剔除影响的因子数据（如 `market_cap`）作为自变量 `X`。
4.  使用普通最小二乘法（OLS）进行线性回归。
5.  返回回归模型的残差（`resid`），即中性化后的因子值。

![](img/b863dd34f01e749714cbe694f179865b_128.png)

![](img/b863dd34f01e749714cbe694f179865b_130.png)

![](img/b863dd34f01e749714cbe694f179865b_132.png)

```python
import statsmodels.api as sm

![](img/b863dd34f01e749714cbe694f179865b_134.png)

![](img/b863dd34f01e749714cbe694f179865b_136.png)

![](img/b863dd34f01e749714cbe694f179865b_138.png)

def neutralize(factor, market_cap):
    """
    对因子进行市值中性化处理
    :param factor: 待中性化的因子数据序列（如市净率）
    :param market_cap: 需要剔除影响的因子数据序列（如市值）
    :return: 中性化后的因子数据序列
    """
    # 将数据转换为float类型以满足模型要求
    y = factor.astype(float)
    x = market_cap.astype(float)
    # 添加常数项，拟合 Y = aX + b 中的截距b
    x = sm.add_constant(x)
    # 建立OLS模型并拟合
    model = sm.OLS(y, x)
    result = model.fit()
    # 返回残差，即剔除X影响后的Y值
    return result.resid
```

![](img/b863dd34f01e749714cbe694f179865b_140.png)

![](img/b863dd34f01e749714cbe694f179865b_142.png)

![](img/b863dd34f01e749714cbe694f179865b_144.png)

---

![](img/b863dd34f01e749714cbe694f179865b_146.png)

![](img/b863dd34f01e749714cbe694f179865b_148.png)

![](img/b863dd34f01e749714cbe694f179865b_150.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤。首先，我们使用**三西格玛法**去除数据中的极端值。接着，通过**Z-Score标准化**将数据转换到统一的尺度。最后，利用**线性回归进行中性化**处理，以剔除特定风险因子（如市值）的影响，得到更有效的因子暴露。这三个步骤是构建稳健量化模型的重要数据准备环节。