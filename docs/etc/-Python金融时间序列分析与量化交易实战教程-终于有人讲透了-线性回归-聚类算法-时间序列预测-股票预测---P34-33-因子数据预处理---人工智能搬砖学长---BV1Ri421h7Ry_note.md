# Python金融时间序列分析与量化交易实战教程：P34：33.因子数据预处理 📊

![](img/73face00c7df61bed181958ca9c80a42_0.png)

![](img/73face00c7df61bed181958ca9c80a42_2.png)

![](img/73face00c7df61bed181958ca9c80a42_4.png)

在本节课中，我们将要学习因子数据预处理的核心三步：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续模型分析的准确性和稳定性。

![](img/73face00c7df61bed181958ca9c80a42_6.png)

![](img/73face00c7df61bed181958ca9c80a42_8.png)

上一节我们完成了指标数据的查询，本节中我们来看看如何对这些原始因子数据进行预处理。

![](img/73face00c7df61bed181958ca9c80a42_10.png)

![](img/73face00c7df61bed181958ca9c80a42_12.png)

## 去极值处理 🧹

![](img/73face00c7df61bed181958ca9c80a42_14.png)

![](img/73face00c7df61bed181958ca9c80a42_16.png)

去极值，也称为处理离群值，是为了消除数据中异常极端值对整体分析的影响。一个常用且简单的方法是**三西格玛（3-Sigma）法**。

![](img/73face00c7df61bed181958ca9c80a42_18.png)

![](img/73face00c7df61bed181958ca9c80a42_20.png)

以下是三西格玛去极值法的实现步骤：

![](img/73face00c7df61bed181958ca9c80a42_22.png)

![](img/73face00c7df61bed181958ca9c80a42_24.png)

1.  计算数据的均值（`mean`）和标准差（`std`）。
2.  设定上限和下限：
    *   上限 = `mean` + 3 * `std`
    *   下限 = `mean` - 3 * `std`
3.  将超出界限的数值“拉回”到边界上，而不是直接删除。

![](img/73face00c7df61bed181958ca9c80a42_26.png)

![](img/73face00c7df61bed181958ca9c80a42_28.png)

其核心公式为：
`上限 = 均值 + N * 标准差`
`下限 = 均值 - N * 标准差`
（本例中 N=3）

![](img/73face00c7df61bed181958ca9c80a42_30.png)

![](img/73face00c7df61bed181958ca9c80a42_32.png)

接下来，我们通过代码来实现这个函数。

![](img/73face00c7df61bed181958ca9c80a42_34.png)

![](img/73face00c7df61bed181958ca9c80a42_36.png)

```python
def filter_three_sigma(series, n=3):
    """
    使用三西格玛法去除极值。
    :param series: 待处理的数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    # 计算均值和标准差
    mean = series.mean()
    std = series.std()
    
    # 计算上限和下限
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    
    # 将超出界限的值规范到边界上
    return series.clip(lower=lower_limit, upper=upper_limit)
```

![](img/73face00c7df61bed181958ca9c80a42_38.png)

![](img/73face00c7df61bed181958ca9c80a42_40.png)

## 标准化处理 📏

![](img/73face00c7df61bed181958ca9c80a42_42.png)

![](img/73face00c7df61bed181958ca9c80a42_44.png)

标准化是为了将不同量纲或量级的指标数据转换到同一尺度，使其具有可比性。标准化的常用方法是**Z-Score标准化**。

![](img/73face00c7df61bed181958ca9c80a42_46.png)

![](img/73face00c7df61bed181958ca9c80a42_48.png)

标准化操作的目的是使数据符合均值为0、标准差为1的标准正态分布。

![](img/73face00c7df61bed181958ca9c80a42_50.png)

![](img/73face00c7df61bed181958ca9c80a42_52.png)

![](img/73face00c7df61bed181958ca9c80a42_54.png)

以下是标准化的实现步骤：

![](img/73face00c7df61bed181958ca9c80a42_56.png)

![](img/73face00c7df61bed181958ca9c80a42_58.png)

1.  计算数据的均值（`mean`）和标准差（`std`）。
2.  对每个数据点进行变换：`(原始值 - 均值) / 标准差`

![](img/73face00c7df61bed181958ca9c80a42_60.png)

![](img/73face00c7df61bed181958ca9c80a42_62.png)

其核心公式为：
`标准化值 = (x - 均值) / 标准差`

![](img/73face00c7df61bed181958ca9c80a42_64.png)

![](img/73face00c7df61bed181958ca9c80a42_66.png)

现在，我们来实现标准化函数。

![](img/73face00c7df61bed181958ca9c80a42_68.png)

![](img/73face00c7df61bed181958ca9c80a42_70.png)

```python
def standardize(series):
    """
    对数据序列进行Z-Score标准化。
    :param series: 待处理的数据序列
    :return: 标准化后的数据序列
    """
    # 计算均值和标准差
    mean = series.mean()
    std = series.std()
    
    # 执行标准化操作
    return (series - mean) / std
```

![](img/73face00c7df61bed181958ca9c80a42_72.png)

![](img/73face00c7df61bed181958ca9c80a42_74.png)

## 中性化处理 ⚖️

![](img/73face00c7df61bed181958ca9c80a42_76.png)

![](img/73face00c7df61bed181958ca9c80a42_78.png)

中性化是为了消除因子数据中与某些已知风格因子（如市值、行业）的相关性，从而提取出因子“纯粹”的预测能力。常用方法是**线性回归法**。

![](img/73face00c7df61bed181958ca9c80a42_80.png)

![](img/73face00c7df61bed181958ca9c80a42_82.png)

上一节我们介绍了去极值和标准化，本节中我们来看看最后一步：中性化。它的核心思想是通过回归模型剥离掉其他因素的影响。

例如，我们想分析“市净率”这个因子，但它可能受到“市值”大小的影响。中性化就是要去除“市值”对“市净率”的影响。

![](img/73face00c7df61bed181958ca9c80a42_84.png)

![](img/73face00c7df61bed181958ca9c80a42_86.png)

以下是中性化的实现思路：

![](img/73face00c7df61bed181958ca9c80a42_88.png)

![](img/73face00c7df61bed181958ca9c80a42_90.png)

1.  设定因变量（Y）为待处理的因子（如市净率），自变量（X）为需要剥离的风格因子（如市值）。
2.  使用**最小二乘法（OLS）** 建立Y关于X的线性回归模型：`Y = W * X + b`。
3.  计算回归模型的残差（`resid`），即 `残差 = 真实Y值 - 模型预测的Y值`。这个残差就是去除了X影响后的“纯”因子值。

![](img/73face00c7df61bed181958ca9c80a42_92.png)

![](img/73face00c7df61bed181958ca9c80a42_94.png)

接下来，我们使用 `statsmodels` 库来实现中性化。

![](img/73face00c7df61bed181958ca9c80a42_96.png)

```python
import statsmodels.api as sm

![](img/73face00c7df61bed181958ca9c80a42_98.png)

![](img/73face00c7df61bed181958ca9c80a42_100.png)

![](img/73face00c7df61bed181958ca9c80a42_102.png)

def neutralize(factor_series, style_series):
    """
    对因子数据进行中性化处理，去除指定风格因子的影响。
    :param factor_series: 待中性化的因子数据（Y）
    :param style_series: 需要剥离的风格因子数据（X）
    :return: 中性化后的因子数据（残差）
    """
    # 为自变量X添加常数项，用于拟合截距b
    X = sm.add_constant(style_series.astype(float))
    Y = factor_series.astype(float)
    
    # 使用最小二乘法建立线性回归模型
    model = sm.OLS(Y, X)
    result = model.fit()
    
    # 返回残差，即中性化后的因子值
    return result.resid
```

![](img/73face00c7df61bed181958ca9c80a42_104.png)

## 综合应用示例 🔄

![](img/73face00c7df61bed181958ca9c80a42_106.png)

我们已经定义了三个核心函数，现在可以将它们串联起来，对一个因子进行完整的预处理流程。

![](img/73face00c7df61bed181958ca9c80a42_108.png)

![](img/73face00c7df61bed181958ca9c80a42_110.png)

假设我们有一个市净率因子数据 `pb_ratio` 和市值数据 `market_cap`，预处理流程如下：

![](img/73face00c7df61bed181958ca9c80a42_112.png)

```python
# 1. 去极值
pb_ratio_filtered = filter_three_sigma(pb_ratio)

![](img/73face00c7df61bed181958ca9c80a42_114.png)

![](img/73face00c7df61bed181958ca9c80a42_116.png)

# 2. 标准化
pb_ratio_standardized = standardize(pb_ratio_filtered)

![](img/73face00c7df61bed181958ca9c80a42_118.png)

![](img/73face00c7df61bed181958ca9c80a42_120.png)

# 3. 中性化 (以市值为风格因子)
pb_ratio_neutralized = neutralize(pb_ratio_standardized, market_cap)

![](img/73face00c7df61bed181958ca9c80a42_122.png)

![](img/73face00c7df61bed181958ca9c80a42_124.png)

# pb_ratio_neutralized 即为预处理完毕，可用于后续建模的“纯净”因子
```

![](img/73face00c7df61bed181958ca9c80a42_126.png)

![](img/73face00c7df61bed181958ca9c80a42_128.png)

本节课中我们一起学习了金融因子数据预处理的三个关键步骤：**去极值**、**标准化**和**中性化**。我们不仅理解了每个步骤的目的，还动手实现了对应的函数。通过这三步处理，原始因子数据中的噪声、量纲差异和风格暴露被有效剥离，得到了更干净、更可靠的输入，为后续的量化模型构建打下了坚实的基础。记住这个流程：先消除异常值，再统一数据尺度，最后剥离外部风格影响。