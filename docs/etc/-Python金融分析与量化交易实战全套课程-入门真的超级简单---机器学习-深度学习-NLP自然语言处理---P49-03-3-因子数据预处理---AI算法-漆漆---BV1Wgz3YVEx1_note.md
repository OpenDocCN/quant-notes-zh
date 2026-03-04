# 金融分析与量化交易：P49：03-3-因子数据预处理 📊

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_1.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_3.png)

在本节课中，我们将要学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能帮助我们获得更稳定、更有效的因子信号。

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_5.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_7.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_9.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_11.png)

## 第一步：去极值 🧹

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_13.png)

去极值操作的目的是处理数据中的异常值或离群点，防止它们对后续分析产生过大影响。我们将采用“三西格玛”方法来实现。

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_15.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_17.png)

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_19.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_21.png)

1.  计算传入数据序列的均值（`mean`）和标准差（`std`）。
2.  根据三西格玛原则，计算上限和下限：
    *   上限 = `mean + 3 * std`
    *   下限 = `mean - 3 * std`
3.  使用 `np.clip` 函数将序列中超出界限的值截断至边界值，而不是直接删除。

```python
import numpy as np

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_23.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_25.png)

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
    
    # 将超出范围的值替换为边界值
    return np.clip(series, lower_limit, upper_limit)
```

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_27.png)

## 第二步：标准化 📏

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_29.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_31.png)

标准化操作的目的是消除数据量纲的影响，将不同尺度的数据转换到同一尺度，便于比较和建模。我们采用常见的Z-Score标准化方法。

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_33.png)

以下是标准化函数 `standardize` 的实现步骤：

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_35.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_37.png)

1.  计算传入数据序列的均值（`mean`）和标准差（`std`）。
2.  对序列中的每个值应用公式：`(值 - mean) / std`。

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_39.png)

```python
def standardize(series):
    """
    对数据序列进行Z-Score标准化
    :param series: 输入的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    
    # 应用标准化公式
    return (series - mean) / std
```

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_41.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_43.png)

## 第三步：中性化 ⚖️

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_45.png)

中性化操作的目的是剔除因子中与其他已知风险因子（如市值）相关的部分，从而获得因子本身的“纯净”收益。我们通过线性回归来实现。

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_47.png)

以下是中性化函数 `neutralize` 的实现思路：

1.  明确自变量（X）和因变量（Y）。在本例中，我们将**因子值**（如市净率）作为因变量Y，将需要剔除影响的**风险因子**（如市值）作为自变量X。
2.  使用`statsmodels`库的普通最小二乘法（OLS）建立Y对X的线性回归模型。
3.  模型的残差（resid）即为中性化后的因子值，它代表了剔除市值影响后，因子自身的部分。

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_49.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_51.png)

```python
import statsmodels.api as sm

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_53.png)

def neutralize(factor_series, risk_factor_series):
    """
    对因子进行市值中性化处理
    :param factor_series: 待中性化的因子序列（Y）
    :param risk_factor_series: 风险因子序列，如市值（X）
    :return: 中性化后的因子序列（残差）
    """
    # 为X添加常数项，用于拟合截距
    X = sm.add_constant(risk_factor_series.astype(float))
    Y = factor_series.astype(float)
    
    # 建立OLS回归模型
    model = sm.OLS(Y, X)
    result = model.fit()
    
    # 返回残差，即中性化后的因子
    return result.resid
```

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_55.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_57.png)

## 整合与应用

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_59.png)

现在，我们已经定义了三个核心预处理函数。在实际应用中，可以按顺序对因子数据调用这些函数。

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_61.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_63.png)

假设我们有一个名为 `pb_ratio` 的市净率因子序列和一个名为 `market_cap` 的市值序列，预处理流程如下：

```python
# 1. 去极值
pb_filtered = filter_three_sigma(pb_ratio)

# 2. 标准化
pb_standardized = standardize(pb_filtered)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_65.png)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_67.png)

# 3. 中性化 (以市值为风险因子)
pb_neutralized = neutralize(pb_standardized, market_cap)

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_69.png)

# pb_neutralized 即为预处理完毕、可供模型使用的因子
```

![](img/6f26efe6cc993b1a4d466bf9f0e44d58_71.png)

本节课中我们一起学习了因子数据预处理的完整流程。我们首先通过**去极值**处理了异常数据，然后通过**标准化**统一了数据尺度，最后通过**中性化**剔除了常见风险因子的影响。这三步是构建高质量量化因子的基础，能显著提升后续策略回测和模型训练的稳定性与效果。