# 量化投资基础：P34：因子数据预处理 📊

![](img/350727c3d058c8d2bc680c285a9ba534_1.png)

在本节课中，我们将学习量化投资中一个至关重要的步骤：因子数据的预处理。原始因子数据通常包含极端值、量纲差异等问题，直接用于模型会影响结果的准确性。因此，我们需要通过一系列标准化的处理流程来“净化”数据，使其更适合后续的分析与建模。

![](img/350727c3d058c8d2bc680c285a9ba534_3.png)

![](img/350727c3d058c8d2bc680c285a9ba534_5.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/350727c3d058c8d2bc680c285a9ba534_7.png)

![](img/350727c3d058c8d2bc680c285a9ba534_9.png)

## 预处理三步法 🚶‍♂️

![](img/350727c3d058c8d2bc680c285a9ba534_11.png)

因子数据预处理通常遵循一个清晰的三步流程，以确保数据的质量和可比性。

![](img/350727c3d058c8d2bc680c285a9ba534_13.png)

以下是核心的三个步骤：
1.  **去极值**：处理数据中的异常值或极端值。
2.  **标准化**：消除不同因子之间量纲和数量级的差异。
3.  **中性化**：剔除因子中与其他已知变量（如市值）的相关性，提取其独立信息。

![](img/350727c3d058c8d2bc680c285a9ba534_15.png)

接下来，我们将逐一实现这三个步骤。

![](img/350727c3d058c8d2bc680c285a9ba534_17.png)

## 第一步：去极值处理 🎯

去极值的目的是减少异常数据点对整体分析的干扰。方法有很多种，我们将采用简单且常用的“三西格玛”法。

![](img/350727c3d058c8d2bc680c285a9ba534_19.png)

![](img/350727c3d058c8d2bc680c285a9ba534_21.png)

其核心思想是：对于服从正态分布的数据，绝大多数值（约99.7%）会落在均值加减三倍标准差的范围内。我们将此范围之外的值视为极端值并进行处理。

![](img/350727c3d058c8d2bc680c285a9ba534_23.png)

以下是实现“三西格玛”去极值法的函数代码：

![](img/350727c3d058c8d2bc680c285a9ba534_25.png)

![](img/350727c3d058c8d2bc680c285a9ba534_27.png)

```python
def filter_extreme_3sigma(series, n=3):
    """
    使用三西格玛法去除极端值。
    参数:
        series: 待处理的因子数据序列（pandas Series）。
        n: 西格玛倍数，默认为3。
    返回:
        处理后的数据序列。
    """
    # 计算均值和标准差
    mean = series.mean()
    std = series.std()

    # 计算上限和下限
    upper_limit = mean + n * std
    lower_limit = mean - n * std

    # 将超出界限的值裁剪到边界处，而不是直接删除
    filtered_series = series.clip(lower=lower_limit, upper=upper_limit)

    return filtered_series
```

这个函数计算了数据的均值（`mean`）和标准差（`std`），然后根据公式 **均值 ± n × 标准差** 确定正常值的范围，最后使用 `.clip()` 方法将超出范围的值替换为边界值。

![](img/350727c3d058c8d2bc680c285a9ba534_29.png)

![](img/350727c3d058c8d2bc680c285a9ba534_31.png)

## 第二步：标准化处理 📏

![](img/350727c3d058c8d2bc680c285a9ba534_33.png)

标准化（Standardization）的目的是将不同尺度和量纲的数据转换到统一的标准正态分布上，使其均值为0，标准差为1。这有助于在后续建模时，让所有因子处于平等的权重地位。

![](img/350727c3d058c8d2bc680c285a9ba534_35.png)

![](img/350727c3d058c8d2bc680c285a9ba534_37.png)

标准化的公式为：**z = (x - μ) / σ**，其中 `x` 是原始值，`μ` 是均值，`σ` 是标准差。

以下是标准化操作的函数实现：

![](img/350727c3d058c8d2bc680c285a9ba534_39.png)

![](img/350727c3d058c8d2bc680c285a9ba534_41.png)

```python
def standardize(series):
    """
    对数据进行标准化处理，使其均值为0，标准差为1。
    参数:
        series: 待处理的数据序列（pandas Series）。
    返回:
        标准化后的数据序列。
    """
    # 计算均值和标准差
    mean = series.mean()
    std = series.std()

    # 应用标准化公式
    standardized_series = (series - mean) / std

    return standardized_series
```

该函数同样先计算序列的均值和标准差，然后对序列中的每一个值应用上述公式进行计算。

## 第三步：中性化处理 ⚖️

![](img/350727c3d058c8d2bc680c285a9ba534_43.png)

![](img/350727c3d058c8d2bc680c285a9ba534_45.png)

中性化是因子预处理中关键的一步。它的目标是剔除因子本身与其他已知风格因子（最常见的是市值因子）的相关性，从而得到该因子“纯净”的、独立的信息。例如，我们想知道“市净率”这个因子本身是否有选股能力，而不是因为它和市值相关才表现出能力。

![](img/350727c3d058c8d2bc680c285a9ba534_47.png)

我们通常使用线性回归的方法进行中性化。以剔除市值影响为例：
*   **因变量 (Y)**：待中性化的因子（如市净率）。
*   **自变量 (X)**：需要剔除影响的因子（如市值）。

![](img/350727c3d058c8d2bc680c285a9ba534_49.png)

通过线性回归 **Y = a * X + b + ε**，我们得到残差项 **ε**。这个 **ε** 就是去除了市值影响后的“纯净”因子值。

![](img/350727c3d058c8d2bc680c285a9ba534_51.png)

![](img/350727c3d058c8d2bc680c285a9ba534_53.png)

以下是使用 `statsmodels` 库实现中性化的函数：

![](img/350727c3d058c8d2bc680c285a9ba534_55.png)

```python
import pandas as pd
import statsmodels.api as sm

![](img/350727c3d058c8d2bc680c285a9ba534_57.png)

def neutralize(factor_series, style_series):
    """
    对因子进行中性化处理，剔除指定风格因子的影响。
    参数:
        factor_series: 待中性化的因子数据序列（Y）。
        style_series: 需要剔除影响的风格因子数据序列（X），如市值。
    返回:
        中性化后的因子残差序列。
    """
    # 为回归添加常数项（截距b）
    X = sm.add_constant(style_series.astype(float)) # 确保为浮点类型
    y = factor_series.astype(float)

    # 使用普通最小二乘法进行线性回归
    model = sm.OLS(y, X).fit()

    # 返回回归的残差，即中性化后的因子值
    neutralized_series = pd.Series(model.resid, index=factor_series.index)

    return neutralized_series
```

代码中，`sm.OLS()` 用于建立普通最小二乘回归模型，`model.resid` 属性直接给出了回归的残差，这正是我们需要的、剔除了 `style_series` 影响后的中性化因子。

## 综合应用与总结 🎉

![](img/350727c3d058c8d2bc680c285a9ba534_59.png)

![](img/350727c3d058c8d2bc680c285a9ba534_61.png)

现在，我们已经完成了三个核心预处理函数的编写。在实际操作中，你可以按顺序对因子数据调用这些函数：

![](img/350727c3d058c8d2bc680c285a9ba534_63.png)

```python
# 假设 raw_factor 是原始市净率因子， market_cap 是市值因子
processed_factor = filter_extreme_3sigma(raw_factor) # 1. 去极值
processed_factor = standardize(processed_factor)     # 2. 标准化
processed_factor = neutralize(processed_factor, market_cap) # 3. 中性化（剔除市值影响）
```

本节课中我们一起学习了量化投资中因子数据预处理的完整流程。我们首先明确了**去极值、标准化、中性化**这三个核心步骤。随后，我们逐一用代码实现了：
1.  使用**三西格玛法**进行去极值处理。
2.  应用**z-score公式**进行数据标准化。
3.  借助**线性回归**和 `statsmodels` 库完成因子的中性化处理，以提取其独立信息。

![](img/350727c3d058c8d2bc680c285a9ba534_65.png)

![](img/350727c3d058c8d2bc680c285a9ba534_67.png)

经过这套流程处理后的因子数据，质量更高、可比性更强，能够为后续的因子分析、模型构建打下坚实的基础。记住，干净的数据是产生可靠模型的第一步。