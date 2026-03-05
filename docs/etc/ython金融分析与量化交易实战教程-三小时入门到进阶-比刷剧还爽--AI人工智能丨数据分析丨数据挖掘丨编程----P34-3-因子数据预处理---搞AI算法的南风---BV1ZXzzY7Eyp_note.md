# Python金融分析与量化交易实战教程：P34：3-因子数据预处理 📊

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_1.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_3.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_5.png)

在本节课中，我们将要学习因子数据预处理的核心三步操作：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续量化模型的稳定性和准确性。

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_7.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_9.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_11.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_13.png)

## 第一步：去极值 🧹

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_15.png)

去极值操作的目的是处理数据中的异常值或离群点，防止它们对整体分析产生过大影响。我们将采用简单实用的“三西格玛”方法来实现。

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_17.png)

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_19.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_21.png)

1.  计算传入数据序列的均值（`mean`）和标准差（`std`）。
2.  根据三西格玛原则，设定数据的上限（`upper`）和下限（`lower`）。
    *   上限公式：`mean + 3 * std`
    *   下限公式：`mean - 3 * std`
3.  使用 `np.clip` 函数将超出界限的数据裁剪到边界值，而不是直接删除。

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_23.png)

```python
import numpy as np

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_25.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_27.png)

def filter_three_sigma(series, n=3):
    """
    使用三西格玛法去除极值
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

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_29.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_31.png)

## 第二步：标准化 📏

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_33.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_35.png)

标准化旨在消除不同因子之间量纲和数量级的差异，使它们具有可比性。我们采用常见的Z-Score标准化方法。

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_37.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_39.png)

以下是标准化函数 `standardize` 的实现步骤：

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_41.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_43.png)

1.  同样，计算数据序列的均值（`mean`）和标准差（`std`）。
2.  对序列中的每个值应用标准化公式：`(value - mean) / std`。

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_45.png)

```python
def standardize(series):
    """
    对数据序列进行Z-Score标准化
    :param series: 输入的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_47.png)

## 第三步：中性化 ⚖️

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_49.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_51.png)

中性化是为了剔除其他常见风险因子（如市值）对当前因子的影响，从而得到因子本身的“纯净”暴露。我们通过线性回归来实现。

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_53.png)

以下是中性化函数 `neutralize` 的实现步骤：

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_55.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_57.png)

1.  明确自变量（X）和因变量（Y）。在本例中，X是市值因子，Y是需要被中性化的目标因子（如市净率）。
2.  使用 `statsmodels` 库的普通最小二乘法（OLS）建立Y对X的线性回归模型。
3.  获取回归的残差（resid）。残差即Y的真实值与模型预测值之差，它代表了去除X影响后，Y自身的部分。

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_59.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_61.png)

```python
import statsmodels.api as sm

def neutralize(factor_series, market_cap_series):
    """
    对因子进行市值中性化处理
    :param factor_series: 需要中性化的因子序列（Y）
    :param market_cap_series: 作为控制变量的市值序列（X）
    :return: 中性化后的因子序列（残差）
    """
    # 确保数据类型为浮点型
    y = factor_series.astype(float)
    x = market_cap_series.astype(float)
    # 添加常数项（截距）
    x = sm.add_constant(x)
    # 建立OLS回归模型
    model = sm.OLS(y, x)
    result = model.fit()
    # 返回残差，即中性化后的因子
    return result.resid
```

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_63.png)

## 综合应用与总结

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_65.png)

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_67.png)

完成三个核心函数的编写后，我们可以按顺序对因子数据调用它们：先 `filter_three_sigma` 去极值，再 `standardize` 标准化，最后 `neutralize` 中性化。

![](img/e34452f83c2a3ea5aa64f9ec3ffabf93_69.png)

本节课中我们一起学习了因子数据预处理的完整流程。通过去极值、标准化和中性化这三个步骤，我们能够将原始的、粗糙的金融因子数据转化为干净、可比、剔除了常见干扰的“精炼”数据，为构建稳健的量化交易模型打下了坚实的基础。记住，高质量的数据预处理是量化策略成功的关键第一步。