# Python金融分析与量化交易实战：P37：37.3-因子数据预处理 📊

![](img/ffc38188c49f14d0cec459b6a8e793fd_0.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_2.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_4.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_6.png)

在本节课中，我们将要学习因子数据预处理的核心步骤。因子数据是量化交易模型的基础，但原始数据通常包含极端值、量纲差异和行业/市值等风格暴露。为了构建稳健的模型，我们必须对因子数据进行清洗和调整。本节将详细介绍去极值、标准化和中性化这三个关键预处理操作。

![](img/ffc38188c49f14d0cec459b6a8e793fd_8.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_10.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_12.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理，使其更适合用于建模分析。

![](img/ffc38188c49f14d0cec459b6a8e793fd_14.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_16.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_18.png)

## 第一步：去极值 🧹

![](img/ffc38188c49f14d0cec459b6a8e793fd_20.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_22.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_24.png)

去极值（或称离群值处理）是预处理的第一步。极端值可能会扭曲数据的统计特性，影响后续分析的准确性。我们将采用简单有效的“三西格玛”法则来处理。

![](img/ffc38188c49f14d0cec459b6a8e793fd_26.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_28.png)

**三西格玛法则**基于正态分布假设，认为绝大多数数据（约99.7%）落在均值加减三倍标准差的范围内。我们将此范围之外的值调整到边界上。

![](img/ffc38188c49f14d0cec459b6a8e793fd_30.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_32.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_34.png)

以下是去极值操作的代码实现：

![](img/ffc38188c49f14d0cec459b6a8e793fd_36.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_38.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_40.png)

```python
def filter_extreme_3sigma(series, n=3):
    """
    使用三西格玛法则去除极端值。
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
    
    # 将超出界限的值调整到边界，而不是直接删除
    new_series = series.clip(lower=lower_limit, upper=upper_limit)
    
    return new_series
```

![](img/ffc38188c49f14d0cec459b6a8e793fd_42.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_44.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_46.png)

## 第二步：标准化 📏

![](img/ffc38188c49f14d0cec459b6a8e793fd_48.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_50.png)

标准化（Standardization）的目的是消除不同因子之间量纲和数量级的差异，使它们具有可比性。我们采用最常用的Z-Score标准化方法。

![](img/ffc38188c49f14d0cec459b6a8e793fd_52.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_54.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_56.png)

**Z-Score标准化公式**为：`(x - μ) / σ`，其中`μ`是均值，`σ`是标准差。标准化后的数据均值为0，标准差为1。

![](img/ffc38188c49f14d0cec459b6a8e793fd_58.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_60.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_62.png)

以下是标准化操作的代码实现：

![](img/ffc38188c49f14d0cec459b6a8e793fd_64.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_66.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_68.png)

```python
def standardize(series):
    """
    对数据进行Z-Score标准化。
    参数:
        series: 待标准化的数据序列（pandas Series）。
    返回:
        标准化后的数据序列。
    """
    # 计算均值和标准差
    mean = series.mean()
    std = series.std()
    
    # 应用Z-Score公式
    new_series = (series - mean) / std
    
    return new_series
```

![](img/ffc38188c49f14d0cec459b6a8e793fd_70.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_72.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_74.png)

## 第三步：中性化 ⚖️

![](img/ffc38188c49f14d0cec459b6a8e793fd_76.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_78.png)

中性化（Neutralization）是为了剔除因子中与某些风格特征（如市值、行业）相关的部分，得到因子本身的“纯”收益预测能力。我们通过线性回归来实现。

![](img/ffc38188c49f14d0cec459b6a8e793fd_80.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_82.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_84.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_86.png)

以剔除市值影响为例，我们将因子值作为因变量`Y`，市值作为自变量`X`，进行线性回归。回归的残差即为中性化后的因子值，它代表了剔除市值影响后，因子自身的特性。

![](img/ffc38188c49f14d0cec459b6a8e793fd_88.png)

以下是中性化操作的代码实现，需要使用`statsmodels`库：

![](img/ffc38188c49f14d0cec459b6a8e793fd_90.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_92.png)

```python
import statsmodels.api as sm

![](img/ffc38188c49f14d0cec459b6a8e793fd_94.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_96.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_98.png)

def neutralize(series, market_cap_series):
    """
    对因子进行市值中性化处理。
    参数:
        series: 待中性化的因子数据序列（pandas Series）。
        market_cap_series: 对应的市值数据序列（pandas Series）。
    返回:
        中性化后的因子数据序列（残差序列）。
    """
    # 确保数据类型为浮点型
    y = series.astype(float)
    x = market_cap_series.astype(float)
    
    # 为自变量添加常数项（截距）
    x = sm.add_constant(x)
    
    # 使用普通最小二乘法进行线性回归
    model = sm.OLS(y, x).fit()
    
    # 返回回归的残差，即中性化后的因子值
    neutralized_series = model.resid
    
    return neutralized_series
```

![](img/ffc38188c49f14d0cec459b6a8e793fd_100.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_102.png)

## 综合应用与流程整合

![](img/ffc38188c49f14d0cec459b6a8e793fd_104.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_106.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_108.png)

现在，我们已经定义了三个核心函数。在实际应用中，我们需要按顺序对因子数据调用这些函数。

![](img/ffc38188c49f14d0cec459b6a8e793fd_110.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_112.png)

假设我们有一个市净率因子数据`pb_ratio`和对应的市值数据`market_cap`，完整的预处理流程如下：

![](img/ffc38188c49f14d0cec459b6a8e793fd_114.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_116.png)

```python
# 1. 去极值
pb_ratio_filtered = filter_extreme_3sigma(pb_ratio)

![](img/ffc38188c49f14d0cec459b6a8e793fd_118.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_120.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_122.png)

# 2. 标准化
pb_ratio_standardized = standardize(pb_ratio_filtered)

![](img/ffc38188c49f14d0cec459b6a8e793fd_124.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_126.png)

# 3. 中性化 (以市值为例)
pb_ratio_neutralized = neutralize(pb_ratio_standardized, market_cap)

![](img/ffc38188c49f14d0cec459b6a8e793fd_128.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_130.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_132.png)

# pb_ratio_neutralized 就是最终预处理好的、可用于建模的因子数据
```

![](img/ffc38188c49f14d0cec459b6a8e793fd_134.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_136.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_138.png)

**流程总结**：
1.  **去极值**：使用三西格玛法将极端值拉回合理边界，防止其对模型造成过度影响。
2.  **标准化**：使用Z-Score方法统一所有因子的量纲，使其均值为0，标准差为1，便于比较和合成。
3.  **中性化**：通过线性回归剔除因子中与市值（或其他风格因子）相关的部分，得到因子自身的“阿尔法”收益预测能力。

![](img/ffc38188c49f14d0cec459b6a8e793fd_140.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_142.png)

![](img/ffc38188c49f14d0cec459b6a8e793fd_144.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤：去极值、标准化和中性化。这三个步骤是构建高质量量化因子、提升模型预测能力的基础。经过预处理后的因子数据，其稳定性、可比性和纯净度都得到了显著提升，为后续的因子合成、选股模型构建等步骤打下了坚实的基础。记住，好的数据是成功模型的一半。