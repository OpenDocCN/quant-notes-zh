# Python金融分析与量化交易实战课程：P33：3-因子数据预处理 📊

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_1.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_3.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_5.png)

在本节课中，我们将要学习因子数据预处理的核心三步操作：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能确保后续分析的准确性和有效性。

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_7.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_9.png)

## 概述

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_11.png)

在查询完指标数据后，我们需要对数据进行预处理。预处理操作遵循三步流程：第一步是去极值，第二步是标准化，第三步是中性化。接下来，我们将逐一实现这三个步骤。

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_13.png)

## 第一步：去极值操作 🧹

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_15.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_17.png)

去极值的目的是处理数据中的异常值或离群点，防止它们对分析结果产生过大影响。方法有很多，这里我们采用简单且常用的“三西格玛”方法。

以下是去极值操作的实现步骤：

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_19.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_21.png)

1.  计算传入数据序列的均值和标准差。
2.  根据三西格玛原则，计算数据的上下限。上限为 `均值 + 3 * 标准差`，下限为 `均值 - 3 * 标准差`。
3.  使用 `numpy.clip` 函数将超出上下限的值规范到边界内，而不是直接删除。

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_23.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_25.png)

```python
import numpy as np

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_27.png)

def filter_extreme_3sigma(series, n=3):
    """
    使用三西格玛法去除极值
    :param series: 待处理的数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    # 计算均值和标准差
    mean = series.mean()
    std = series.std()
    
    # 计算上下限
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    
    # 使用clip函数将数据规范到上下限之间
    series_clipped = np.clip(series, lower_limit, upper_limit)
    
    return series_clipped
```

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_29.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_31.png)

## 第二步：标准化操作 📏

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_33.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_35.png)

标准化（或称为Z-Score标准化）的目的是将不同尺度和量纲的数据转换到统一的标准正态分布上，使其均值为0，标准差为1。这有助于不同因子之间的比较和组合。

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_37.png)

上一节我们介绍了去极值，本节中我们来看看标准化。其核心公式是：`(数据值 - 均值) / 标准差`。

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_39.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_41.png)

以下是标准化操作的实现：

```python
def standardize(series):
    """
    对数据序列进行标准化（Z-Score标准化）
    :param series: 待处理的数据序列
    :return: 标准化后的数据序列
    """
    # 计算均值和标准差
    mean = series.mean()
    std = series.std()
    
    # 应用标准化公式
    series_standardized = (series - mean) / std
    
    return series_standardized
```

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_43.png)

## 第三步：中性化操作 ⚖️

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_45.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_47.png)

中性化操作的目的是消除因子数据中与其他已知变量（如市值）的相关性，从而提取出因子“纯粹”的预测能力。例如，我们想分析市净率因子本身的价值，就需要剔除市值对它的影响。

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_49.png)

以下是实现中性化操作的步骤：

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_51.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_53.png)

1.  明确自变量（X）和因变量（Y）。在本例中，**Y** 是我们需要中性化的因子（如市净率），**X** 是希望剔除其影响的变量（如市值）。
2.  使用线性回归（最小二乘法）建立 **Y** 关于 **X** 的模型。
3.  计算回归模型的残差（即 `真实Y值 - 模型预测的Y值`）。这个残差就是剔除了 **X** 影响后的“中性化”因子值。

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_55.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_57.png)

```python
import statsmodels.api as sm

def neutralize(factor_series, market_cap_series):
    """
    对因子进行市值中性化处理
    :param factor_series: 待中性化的因子数据序列（如市净率）
    :param market_cap_series: 作为自变量的数据序列（如市值）
    :return: 中性化后的因子序列
    """
    # 确保数据类型为浮点型
    Y = factor_series.astype(float)
    X = market_cap_series.astype(float)
    
    # 为X添加常数项（截距）
    X = sm.add_constant(X)
    
    # 使用普通最小二乘法进行线性回归
    model = sm.OLS(Y, X).fit()
    
    # 返回残差，即中性化后的因子值
    neutralized_factor = model.resid
    
    return neutralized_factor
```

## 总结

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_59.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_61.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_63.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤。

1.  **去极值**：使用三西格玛法处理异常值，保护模型不受极端数据干扰。
2.  **标准化**：通过Z-Score方法将数据转化为均值为0、标准差为1的标准分布，便于不同因子比较。
3.  **中性化**：运用线性回归剔除因子与特定变量（如市值）的相关性，提取因子的独立信息。

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_65.png)

![](img/6f64af9dc3dd1f8c9ffbdc227a1241e3_67.png)

完成这三步预处理后，因子数据将变得更加“干净”和“可靠”，为后续的因子分析、建模和策略构建奠定了坚实的基础。