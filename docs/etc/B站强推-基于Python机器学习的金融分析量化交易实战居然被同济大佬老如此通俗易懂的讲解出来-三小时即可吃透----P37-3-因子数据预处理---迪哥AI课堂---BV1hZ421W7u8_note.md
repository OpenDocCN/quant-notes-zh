# 金融量化分析：P37：因子数据预处理 📊

![](img/f81dfd42397e4834cf563e993750b8c7_0.png)

![](img/f81dfd42397e4834cf563e993750b8c7_2.png)

![](img/f81dfd42397e4834cf563e993750b8c7_4.png)

![](img/f81dfd42397e4834cf563e993750b8c7_6.png)

![](img/f81dfd42397e4834cf563e993750b8c7_8.png)

在本节课中，我们将要学习因子数据预处理的核心三步：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续量化模型的稳定性和准确性。

![](img/f81dfd42397e4834cf563e993750b8c7_10.png)

![](img/f81dfd42397e4834cf563e993750b8c7_12.png)

![](img/f81dfd42397e4834cf563e993750b8c7_14.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/f81dfd42397e4834cf563e993750b8c7_16.png)

![](img/f81dfd42397e4834cf563e993750b8c7_18.png)

![](img/f81dfd42397e4834cf563e993750b8c7_20.png)

## 第一步：去极值 🧹

![](img/f81dfd42397e4834cf563e993750b8c7_22.png)

![](img/f81dfd42397e4834cf563e993750b8c7_24.png)

![](img/f81dfd42397e4834cf563e993750b8c7_26.png)

![](img/f81dfd42397e4834cf563e993750b8c7_28.png)

去极值操作的目的是处理数据中的异常值或离群点，防止它们对整体分析产生过大影响。我们将采用“三西格玛”法则来实现。

![](img/f81dfd42397e4834cf563e993750b8c7_30.png)

![](img/f81dfd42397e4834cf563e993750b8c7_32.png)

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/f81dfd42397e4834cf563e993750b8c7_34.png)

![](img/f81dfd42397e4834cf563e993750b8c7_36.png)

![](img/f81dfd42397e4834cf563e993750b8c7_38.png)

1.  计算传入数据序列的均值（`mean`）和标准差（`std`）。
2.  根据三西格玛法则，计算数据的上下限：
    *   上限：`mean + 3 * std`
    *   下限：`mean - 3 * std`
3.  使用 `np.clip` 函数将超出上下限的值规范到边界内，而不是直接删除。

![](img/f81dfd42397e4834cf563e993750b8c7_40.png)

![](img/f81dfd42397e4834cf563e993750b8c7_42.png)

![](img/f81dfd42397e4834cf563e993750b8c7_44.png)

![](img/f81dfd42397e4834cf563e993750b8c7_46.png)

```python
import numpy as np

![](img/f81dfd42397e4834cf563e993750b8c7_48.png)

![](img/f81dfd42397e4834cf563e993750b8c7_50.png)

![](img/f81dfd42397e4834cf563e993750b8c7_52.png)

def filter_three_sigma(series, n=3):
    """
    使用三西格玛法则去除极值
    :param series: 输入的数据序列（pandas Series）
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    mean = series.mean()
    std = series.std()
    
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    
    return np.clip(series, lower_limit, upper_limit)
```

![](img/f81dfd42397e4834cf563e993750b8c7_54.png)

![](img/f81dfd42397e4834cf563e993750b8c7_56.png)

![](img/f81dfd42397e4834cf563e993750b8c7_58.png)

![](img/f81dfd42397e4834cf563e993750b8c7_60.png)

## 第二步：标准化 📏

![](img/f81dfd42397e4834cf563e993750b8c7_62.png)

![](img/f81dfd42397e4834cf563e993750b8c7_64.png)

![](img/f81dfd42397e4834cf563e993750b8c7_66.png)

标准化（Standardization）的目的是将不同量纲或量级的指标数据转换为统一的尺度，使其具有可比性。常用方法是Z-Score标准化。

![](img/f81dfd42397e4834cf563e993750b8c7_68.png)

![](img/f81dfd42397e4834cf563e993750b8c7_70.png)

![](img/f81dfd42397e4834cf563e993750b8c7_72.png)

以下是标准化函数 `standardize` 的实现：

![](img/f81dfd42397e4834cf563e993750b8c7_74.png)

![](img/f81dfd42397e4834cf563e993750b8c7_76.png)

![](img/f81dfd42397e4834cf563e993750b8c7_78.png)

1.  同样计算数据序列的均值（`mean`）和标准差（`std`）。
2.  对序列中的每个值应用公式：`(value - mean) / std`。
3.  处理后的数据均值为0，标准差为1。

![](img/f81dfd42397e4834cf563e993750b8c7_80.png)

![](img/f81dfd42397e4834cf563e993750b8c7_82.png)

![](img/f81dfd42397e4834cf563e993750b8c7_84.png)

```python
def standardize(series):
    """
    对数据序列进行Z-Score标准化
    :param series: 输入的数据序列（pandas Series）
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    
    return (series - mean) / std
```

![](img/f81dfd42397e4834cf563e993750b8c7_86.png)

![](img/f81dfd42397e4834cf563e993750b8c7_88.png)

![](img/f81dfd42397e4834cf563e993750b8c7_90.png)

![](img/f81dfd42397e4834cf563e993750b8c7_92.png)

## 第三步：中性化 ⚖️

![](img/f81dfd42397e4834cf563e993750b8c7_94.png)

![](img/f81dfd42397e4834cf563e993750b8c7_96.png)

中性化（Neutralization）的目的是消除因子数据中受其他常见因素（如市值、行业）影响的部分，从而提取因子本身的“纯净”信息。我们通过线性回归来实现。

![](img/f81dfd42397e4834cf563e993750b8c7_98.png)

![](img/f81dfd42397e4834cf563e993750b8c7_100.png)

![](img/f81dfd42397e4834cf563e993750b8c7_102.png)

以下是中性化函数 `neutralize` 的实现思路：

![](img/f81dfd42397e4834cf563e993750b8c7_104.png)

![](img/f81dfd42397e4834cf563e993750b8c7_106.png)

![](img/f81dfd42397e4834cf563e993750b8c7_108.png)

1.  明确自变量（X）和因变量（Y）。例如，我们希望消除市值对市净率（PB）因子的影响，那么：
    *   **Y**：市净率因子数据（`factor`）
    *   **X**：市值数据（`market_cap`）
2.  使用 `statsmodels` 库的普通最小二乘法（OLS）建立 `Y ~ X` 的线性回归模型。
3.  模型的残差（`resid`）即为去除市值影响后的“中性化”因子值。

![](img/f81dfd42397e4834cf563e993750b8c7_110.png)

![](img/f81dfd42397e4834cf563e993750b8c7_112.png)

![](img/f81dfd42397e4834cf563e993750b8c7_114.png)

```python
import statsmodels.api as sm

![](img/f81dfd42397e4834cf563e993750b8c7_116.png)

![](img/f81dfd42397e4834cf563e993750b8c7_118.png)

![](img/f81dfd42397e4834cf563e993750b8c7_120.png)

def neutralize(factor, market_cap):
    """
    对因子进行市值中性化处理
    :param factor: 待中性化的因子数据（pandas Series）
    :param market_cap: 作为自变量的市值数据（pandas Series）
    :return: 中性化后的因子数据（残差序列）
    """
    # 确保数据类型为浮点型
    y = factor.astype(float)
    x = market_cap.astype(float)
    
    # 添加常数项（截距）
    x = sm.add_constant(x)
    
    # 建立OLS模型并拟合
    model = sm.OLS(y, x).fit()
    
    # 返回残差，即中性化后的因子值
    return model.resid
```

![](img/f81dfd42397e4834cf563e993750b8c7_122.png)

![](img/f81dfd42397e4834cf563e993750b8c7_124.png)

## 综合应用与流程总结

![](img/f81dfd42397e4834cf563e993750b8c7_126.png)

![](img/f81dfd42397e4834cf563e993750b8c7_128.png)

![](img/f81dfd42397e4834cf563e993750b8c7_130.png)

在实际操作中，我们需要按顺序对因子数据应用这三个函数。

![](img/f81dfd42397e4834cf563e993750b8c7_132.png)

![](img/f81dfd42397e4834cf563e993750b8c7_134.png)

![](img/f81dfd42397e4834cf563e993750b8c7_136.png)

以下是完整的预处理流程：

![](img/f81dfd42397e4834cf563e993750b8c7_138.png)

![](img/f81dfd42397e4834cf563e993750b8c7_140.png)

![](img/f81dfd42397e4834cf563e993750b8c7_142.png)

![](img/f81dfd42397e4834cf563e993750b8c7_144.png)

1.  **去极值**：首先使用 `filter_three_sigma` 函数处理原始因子数据，平滑异常值。
2.  **标准化**：接着对去极值后的数据使用 `standardize` 函数，将其转换为均值为0、标准差1的标准分布。
3.  **中性化**：最后，将标准化后的因子与市值数据一同传入 `neutralize` 函数，得到不受市值影响的纯净因子值。

![](img/f81dfd42397e4834cf563e993750b8c7_146.png)

![](img/f81dfd42397e4834cf563e993750b8c7_148.png)

```python
# 假设 raw_factor 是原始市净率因子数据， market_cap 是市值数据
processed_factor = neutralize(
    standardize(
        filter_three_sigma(raw_factor)
    ),
    market_cap
)
```

![](img/f81dfd42397e4834cf563e993750b8c7_150.png)

![](img/f81dfd42397e4834cf563e993750b8c7_152.png)

![](img/f81dfd42397e4834cf563e993750b8c7_154.png)

本节课中我们一起学习了金融因子数据预处理的三个核心步骤。**去极值**帮助我们控制异常值的干扰；**标准化**使得不同因子具有可比性；**中性化**则能剥离常见市场因素的影响，提取因子的独立信息。掌握这三步，是构建稳健量化模型的重要基础。