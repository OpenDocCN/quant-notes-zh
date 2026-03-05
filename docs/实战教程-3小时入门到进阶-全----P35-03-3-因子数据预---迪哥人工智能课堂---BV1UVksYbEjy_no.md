# Python金融分析与量化交易实战教程：P35：03-3-因子数据预处理 📊

![](img/d95360a2d3a806d835c20886033d39e7_1.png)

![](img/d95360a2d3a806d835c20886033d39e7_3.png)

![](img/d95360a2d3a806d835c20886033d39e7_5.png)

在本节课中，我们将要学习因子数据预处理的核心三步操作：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续量化模型的稳定性和准确性。

![](img/d95360a2d3a806d835c20886033d39e7_7.png)

![](img/d95360a2d3a806d835c20886033d39e7_9.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/d95360a2d3a806d835c20886033d39e7_11.png)

![](img/d95360a2d3a806d835c20886033d39e7_13.png)

## 第一步：去极值 🧹

![](img/d95360a2d3a806d835c20886033d39e7_15.png)

去极值，也称为处理离群值，目的是消除数据中异常大或异常小的极端值，防止它们对模型造成过大影响。我们将使用经典的**三西格玛（3-Sigma）**方法来实现。

![](img/d95360a2d3a806d835c20886033d39e7_17.png)

以下是去极值操作的实现步骤：

![](img/d95360a2d3a806d835c20886033d39e7_19.png)

1.  计算传入数据序列的均值（`mean`）和标准差（`std`）。
2.  根据三西格玛原则，设定数据的上下限：
    *   上限 = `mean + 3 * std`
    *   下限 = `mean - 3 * std`
3.  使用`numpy.clip`函数，将超出上下限的值截断至边界值，而非直接删除。

![](img/d95360a2d3a806d835c20886033d39e7_21.png)

```python
import numpy as np

![](img/d95360a2d3a806d835c20886033d39e7_23.png)

![](img/d95360a2d3a806d835c20886033d39e7_25.png)

![](img/d95360a2d3a806d835c20886033d39e7_27.png)

def filter_extreme_3sigma(series, n=3):
    """
    使用三西格玛法去除极值
    :param series: 待处理的数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    mean = series.mean()
    std = series.std()
    
    # 计算上下限
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    
    # 使用clip函数截断极值
    new_series = series.clip(lower_limit, upper_limit)
    return new_series
```

![](img/d95360a2d3a806d835c20886033d39e7_29.png)

## 第二步：标准化 📏

![](img/d95360a2d3a806d835c20886033d39e7_31.png)

![](img/d95360a2d3a806d835c20886033d39e7_33.png)

标准化（Standardization）的目的是将不同量纲或量级的指标数据转换到统一的尺度上，使其具有均值为0、标准差为1的分布。这有助于模型更公平地对待每一个特征。

![](img/d95360a2d3a806d835c20886033d39e7_35.png)

![](img/d95360a2d3a806d835c20886033d39e7_37.png)

以下是标准化操作的实现步骤：

![](img/d95360a2d3a806d835c20886033d39e7_39.png)

![](img/d95360a2d3a806d835c20886033d39e7_41.png)

1.  计算传入数据序列的均值（`mean`）和标准差（`std`）。
2.  对序列中的每一个值，执行 `(值 - 均值) / 标准差` 的运算。

![](img/d95360a2d3a806d835c20886033d39e7_43.png)

![](img/d95360a2d3a806d835c20886033d39e7_45.png)

```python
def standardize(series):
    """
    对数据序列进行标准化（Z-Score标准化）
    :param series: 待处理的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    
    # 执行标准化公式: (x - mean) / std
    new_series = (series - mean) / std
    return new_series
```

## 第三步：中性化 🧪

![](img/d95360a2d3a806d835c20886033d39e7_47.png)

![](img/d95360a2d3a806d835c20886033d39e7_49.png)

中性化（Neutralization）是金融因子处理中特有的一步，目的是消除因子中与市值等风格因子的相关性，从而得到因子本身的“纯净”收益预测能力。我们通过线性回归来实现。

![](img/d95360a2d3a806d835c20886033d39e7_51.png)

以下是中性化操作的实现步骤：

![](img/d95360a2d3a806d835c20886033d39e7_53.png)

![](img/d95360a2d3a806d835c20886033d39e7_55.png)

1.  明确自变量（X）和因变量（Y）。在本例中，我们用**市值**（风格因子）来解释**市净率**（待处理因子）。因此，X是市值，Y是市净率。
2.  使用`statsmodels`库的普通最小二乘法（OLS）建立`Y ~ X`的线性回归模型。
3.  计算回归模型的残差（`resid`）。残差即`Y`的真实值与回归模型预测值之间的差值，它代表了去除市值影响后，市净率因子所包含的独立信息。

![](img/d95360a2d3a806d835c20886033d39e7_57.png)

![](img/d95360a2d3a806d835c20886033d39e7_59.png)

```python
import statsmodels.api as sm

![](img/d95360a2d3a806d835c20886033d39e7_61.png)

def neutralize(factor_series, style_series):
    """
    对因子进行市值中性化处理
    :param factor_series: 待中性化的因子序列（如市净率），作为因变量Y
    :param style_series: 风格因子序列（如市值），作为自变量X
    :return: 中性化后的因子序列（残差）
    """
    # 确保数据类型为浮点型
    Y = factor_series.astype(float)
    X = style_series.astype(float)
    
    # 添加常数项（截距）
    X = sm.add_constant(X)
    
    # 建立OLS回归模型并拟合
    model = sm.OLS(Y, X)
    result = model.fit()
    
    # 返回残差，即中性化后的因子
    neutralized_factor = result.resid
    return neutralized_factor
```

## 综合应用与总结 🎯

![](img/d95360a2d3a806d835c20886033d39e7_63.png)

![](img/d95360a2d3a806d835c20886033d39e7_65.png)

现在，我们已经完成了三个核心预处理函数的编写。在实际项目中，你可以按顺序对因子数据调用这些函数：

![](img/d95360a2d3a806d835c20886033d39e7_67.png)

```python
# 假设 df[‘pb_ratio’] 是市净率因子，df[‘market_cap’] 是市值
processed_factor = df[‘pb_ratio’].copy()
processed_factor = filter_extreme_3sigma(processed_factor)  # 1. 去极值
processed_factor = standardize(processed_factor)            # 2. 标准化
processed_factor = neutralize(processed_factor, df[‘market_cap’]) # 3. 中性化
```

![](img/d95360a2d3a806d835c20886033d39e7_69.png)

本节课中我们一起学习了因子数据预处理的完整流程。我们首先通过**三西格玛法**去除极端值的影响，然后使用**Z-Score标准化**将数据缩放到统一尺度，最后通过**线性回归中性化**剥离了市值风格的影响，从而得到了一个相对“纯净”、可用于构建量化模型的因子。这三步是构建稳健量化策略不可或缺的数据准备环节。