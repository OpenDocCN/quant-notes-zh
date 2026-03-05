# Python金融分析与量化交易实战教程：P33：3-因子数据预处理 📊

![](img/6d552e18489635deb4e57b8e021c4c1a_1.png)

![](img/6d552e18489635deb4e57b8e021c4c1a_3.png)

在本节课中，我们将要学习因子数据预处理的核心三步操作：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续量化模型的稳定性和准确性。

![](img/6d552e18489635deb4e57b8e021c4c1a_5.png)

上一节我们完成了指标的查询，本节中我们来看看如何对这些原始因子数据进行预处理。

![](img/6d552e18489635deb4e57b8e021c4c1a_7.png)

![](img/6d552e18489635deb4e57b8e021c4c1a_9.png)

## 第一步：去极值 🧹

![](img/6d552e18489635deb4e57b8e021c4c1a_11.png)

去极值，也称为处理离群值，目的是消除数据中异常大或异常小的极端值，防止它们对整体分析产生过大影响。方法有很多，这里我们采用简单实用的“三西格玛”法。

![](img/6d552e18489635deb4e57b8e021c4c1a_13.png)

以下是去极值操作的步骤：

![](img/6d552e18489635deb4e57b8e021c4c1a_15.png)

![](img/6d552e18489635deb4e57b8e021c4c1a_17.png)

1.  计算因子数据的均值（`mean`）和标准差（`std`）。
2.  设定上限和下限：
    *   上限 = `mean` + 3 * `std`
    *   下限 = `mean` - 3 * `std`
3.  将超出上下限的值“缩回”到边界上，而不是直接删除，以保留数据分布形态。

我们将上述逻辑封装为一个函数：

![](img/6d552e18489635deb4e57b8e021c4c1a_19.png)

```python
def filter_extreme_3sigma(series, n=3):
    """
    使用三西格玛法去除极值
    :param series: 待处理的因子数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    mean = series.mean()
    std = series.std()
    
    # 计算上下限
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    
    # 将超出范围的值替换为边界值
    series_clipped = series.clip(lower=lower_limit, upper=upper_limit)
    return series_clipped
```

![](img/6d552e18489635deb4e57b8e021c4c1a_21.png)

## 第二步：标准化 📏

![](img/6d552e18489635deb4e57b8e021c4c1a_23.png)

标准化（Standardization）的目的是将不同量纲或量级的因子数据转换到同一尺度，使其均值为0，标准差为1。这有助于在构建多因子模型时公平地比较和组合不同因子。

![](img/6d552e18489635deb4e57b8e021c4c1a_25.png)

![](img/6d552e18489635deb4e57b8e021c4c1a_27.png)

标准化操作的公式为：
**`z = (x - μ) / σ`**
其中，`x`是原始值，`μ`是均值，`σ`是标准差。

![](img/6d552e18489635deb4e57b8e021c4c1a_29.png)

以下是标准化函数的实现：

![](img/6d552e18489635deb4e57b8e021c4c1a_31.png)

![](img/6d552e18489635deb4e57b8e021c4c1a_33.png)

```python
def standardize(series):
    """
    对数据进行标准化处理（Z-Score标准化）
    :param series: 待处理的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    
    # 应用标准化公式
    series_standardized = (series - mean) / std
    return series_standardized
```

![](img/6d552e18489635deb4e57b8e021c4c1a_35.png)

## 第三步：中性化 ⚖️

![](img/6d552e18489635deb4e57b8e021c4c1a_37.png)

中性化（Neutralization）是为了消除因子数据中与其他已知变量（如市值、行业）的相关性。例如，一个因子可能仅仅因为与市值高度相关而表现出预测能力，中性化可以剥离这种影响，得到因子“纯粹”的选股能力。这里我们演示如何对因子进行市值中性化。

![](img/6d552e18489635deb4e57b8e021c4c1a_39.png)

其核心思想是建立一个线性回归模型：
**`因子 = α + β * 市值 + ε`**
然后，我们取回归模型的残差 `ε` 作为中性化后的因子值。`ε` 代表了因子中无法被市值解释的部分，即“提纯”后的因子。

![](img/6d552e18489635deb4e57b8e021c4c1a_41.png)

以下是中性化操作的步骤：

1.  准备数据：将因子数据作为因变量 `Y`，将市值数据作为自变量 `X`。
2.  建立线性回归模型，使用最小二乘法进行拟合。
3.  提取模型的残差（`resid`），即为中性化后的因子值。

![](img/6d552e18489635deb4e57b8e021c4c1a_43.png)

我们需要使用 `statsmodels` 库来实现线性回归：

![](img/6d552e18489635deb4e57b8e021c4c1a_45.png)

![](img/6d552e18489635deb4e57b8e021c4c1a_47.png)

```python
import statsmodels.api as sm

![](img/6d552e18489635deb4e57b8e021c4c1a_49.png)

def neutralize(factor_series, market_cap_series):
    """
    对因子进行市值中性化处理
    :param factor_series: 待中性化的因子数据序列
    :param market_cap_series: 市值数据序列
    :return: 中性化后的因子数据序列（残差）
    """
    # 确保数据类型为浮点型
    Y = factor_series.astype(float)
    X = market_cap_series.astype(float)
    
    # 为X添加常数项（截距）
    X = sm.add_constant(X)
    
    # 使用普通最小二乘法（OLS）进行线性回归
    model = sm.OLS(Y, X)
    result = model.fit()
    
    # 返回回归残差，即中性化后的因子
    neutralized_factor = result.resid
    return neutralized_factor
```

![](img/6d552e18489635deb4e57b8e021c4c1a_51.png)

> **提示**：`statsmodels` 的 `resid` 属性直接提供了真实值与模型预测值之间的差异（残差），这正好是我们需要的中性化结果。

![](img/6d552e18489635deb4e57b8e021c4c1a_53.png)

## 综合应用与总结 🎯

![](img/6d552e18489635deb4e57b8e021c4c1a_55.png)

![](img/6d552e18489635deb4e57b8e021c4c1a_57.png)

完成三个核心函数的编写后，我们可以按顺序对因子数据调用它们：

```python
# 假设 df[‘pb_ratio’] 是市净率因子，df[‘market_cap’] 是市值
factor_raw = df[‘pb_ratio’]

# 1. 去极值
factor_no_extreme = filter_extreme_3sigma(factor_raw)

![](img/6d552e18489635deb4e57b8e021c4c1a_59.png)

# 2. 标准化
factor_standardized = standardize(factor_no_extreme)

![](img/6d552e18489635deb4e57b8e021c4c1a_61.png)

![](img/6d552e18489635deb4e57b8e021c4c1a_63.png)

# 3. 市值中性化
factor_neutralized = neutralize(factor_standardized, df[‘market_cap’])

# 最终处理好的因子
processed_factor = factor_neutralized
```

![](img/6d552e18489635deb4e57b8e021c4c1a_65.png)

本节课中我们一起学习了因子数据预处理的完整流程。我们首先通过**三西格玛法去极值**，消除了异常值的干扰；接着通过**Z-Score标准化**，将数据统一到标准尺度；最后通过**线性回归市值中性化**，剥离了市值对因子的影响，得到了更为“纯净”的因子暴露。这三步是构建稳健量化模型的基础，处理后的因子能更真实地反映其本身的预测能力。