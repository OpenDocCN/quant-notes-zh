# Python金融分析与量化交易实战：P37：3-因子数据预处理 📊

![](img/9bb9010142d70f9adb9b325cf9ded4fe_0.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_2.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_4.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_6.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_8.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_10.png)

在本节课中，我们将要学习因子数据预处理的核心三步操作：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续量化模型分析的准确性和稳定性。

![](img/9bb9010142d70f9adb9b325cf9ded4fe_12.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_14.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_16.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_18.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/9bb9010142d70f9adb9b325cf9ded4fe_20.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_22.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_24.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_26.png)

## 第一步：去极值 🧹

![](img/9bb9010142d70f9adb9b325cf9ded4fe_28.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_30.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_32.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_34.png)

去极值，也称为处理离群值，目的是消除数据中异常极端值的影响。一个常用且简单的方法是**三西格玛法**。

![](img/9bb9010142d70f9adb9b325cf9ded4fe_36.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_38.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_40.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_42.png)

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/9bb9010142d70f9adb9b325cf9ded4fe_44.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_46.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_48.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_50.png)

1.  计算传入数据序列的均值（`mean`）和标准差（`std`）。
2.  根据公式 **上限 = 均值 + 3 * 标准差** 和 **下限 = 均值 - 3 * 标准差** 确定正常值的范围。
3.  使用 `np.clip` 函数将超出范围的值截断至边界值，而不是直接删除。

![](img/9bb9010142d70f9adb9b325cf9ded4fe_52.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_54.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_56.png)

```python
import numpy as np

![](img/9bb9010142d70f9adb9b325cf9ded4fe_58.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_60.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_62.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_64.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_66.png)

def filter_three_sigma(series, n=3):
    """
    使用三西格玛法去除极值
    :param series: 输入的数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    mean = series.mean()
    std = series.std()
    
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    
    # 将超出上下限的值替换为边界值
    return np.clip(series, lower_limit, upper_limit)
```

![](img/9bb9010142d70f9adb9b325cf9ded4fe_68.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_70.png)

## 第二步：标准化 📏

![](img/9bb9010142d70f9adb9b325cf9ded4fe_72.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_74.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_76.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_78.png)

标准化是将数据按比例缩放，使之落入一个特定的区间（通常是均值为0，标准差为1），以消除量纲的影响。我们采用**Z-Score标准化**方法。

![](img/9bb9010142d70f9adb9b325cf9ded4fe_80.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_82.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_84.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_86.png)

以下是标准化函数 `standardize` 的实现：

![](img/9bb9010142d70f9adb9b325cf9ded4fe_88.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_90.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_92.png)

1.  同样计算数据序列的均值（`mean`）和标准差（`std`）。
2.  应用公式 **(原始值 - 均值) / 标准差** 对每一个数据进行转换。

![](img/9bb9010142d70f9adb9b325cf9ded4fe_94.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_96.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_98.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_100.png)

```python
def standardize(series):
    """
    对数据序列进行Z-Score标准化
    :param series: 输入的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    
    # Z-Score 标准化公式
    return (series - mean) / std
```

![](img/9bb9010142d70f9adb9b325cf9ded4fe_102.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_104.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_106.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_108.png)

## 第三步：中性化 🧪

![](img/9bb9010142d70f9adb9b325cf9ded4fe_110.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_112.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_114.png)

中性化是为了消除因子数据中其他常见风险因子（如市值、行业）的影响，从而得到因子本身的“纯净”暴露。我们通过**线性回归**来实现。

![](img/9bb9010142d70f9adb9b325cf9ded4fe_116.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_118.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_120.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_122.png)

以消除市值影响为例，以下是中性化函数 `neutralize` 的实现思路：

![](img/9bb9010142d70f9adb9b325cf9ded4fe_124.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_126.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_128.png)

1.  将待处理的因子（如市净率）作为因变量 **Y**。
2.  将需要被中性化的因子（如市值）作为自变量 **X**。
3.  使用 `statsmodels` 库的普通最小二乘法（OLS）进行线性回归 **Y = W * X + b**。
4.  取回归的残差（residuals），即 **真实Y值 - 模型预测Y值**。这个残差就是去除了市值影响后的“中性化”因子值。

![](img/9bb9010142d70f9adb9b325cf9ded4fe_130.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_132.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_134.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_136.png)

```python
import statsmodels.api as sm

![](img/9bb9010142d70f9adb9b325cf9ded4fe_138.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_140.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_142.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_144.png)

def neutralize(factor_series, market_cap_series):
    """
    对因子进行市值中性化处理
    :param factor_series: 待中性化的因子序列（如市净率）
    :param market_cap_series: 市值序列
    :return: 中性化后的因子序列
    """
    # 将市值作为自变量X，需要添加常数项
    X = sm.add_constant(market_cap_series.astype(float))
    # 将因子作为因变量Y
    Y = factor_series.astype(float)
    
    # 建立OLS回归模型
    model = sm.OLS(Y, X)
    result = model.fit()
    
    # 回归残差即为中性化后的因子值
    return result.resid
```

![](img/9bb9010142d70f9adb9b325cf9ded4fe_146.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_148.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_150.png)

---

![](img/9bb9010142d70f9adb9b325cf9ded4fe_152.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_154.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_156.png)

![](img/9bb9010142d70f9adb9b325cf9ded4fe_158.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤：**去极值**、**标准化**和**中性化**。我们不仅理解了它们的目的，还动手实现了对应的函数。这三步是构建可靠量化因子、进行有效多因子分析的基础，请务必掌握其原理和代码实现。接下来，我们就可以将这些处理后的“干净”因子用于更深入的因子评价和策略构建了。