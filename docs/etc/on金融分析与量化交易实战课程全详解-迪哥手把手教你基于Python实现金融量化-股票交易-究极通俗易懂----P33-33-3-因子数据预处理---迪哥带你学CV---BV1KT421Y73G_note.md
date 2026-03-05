# Python金融分析与量化交易实战课程：P33：因子数据预处理 📊

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_1.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_3.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_5.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_7.png)

在本节课中，我们将要学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能帮助我们获得更可靠、更有效的分析结果。

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_9.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_11.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_13.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_15.png)

## 去极值处理 🧹

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_17.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_19.png)

去极值，也称为离群值处理，目的是消除数据中异常极端值的影响。我们采用“三西格玛”法则来实现。

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_21.png)

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_23.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_25.png)

```python
def filter_three_sigma(series, n=3):
    # 计算数据的均值和标准差
    mean = series.mean()
    std = series.std()
    # 计算上限和下限
    upper = mean + n * std
    lower = mean - n * std
    # 将超出界限的值替换为界限值
    return series.clip(lower, upper)
```

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_27.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_29.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_31.png)

这个函数接收一个数据序列 `series` 和倍数 `n`（默认为3）。它首先计算序列的均值（`mean`）和标准差（`std`），然后根据公式 **均值 ± n * 标准差** 确定正常值的范围。最后，使用 `.clip()` 方法将所有超出此范围的值“修剪”到边界上，而不是直接删除，从而保留了数据点的数量。

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_33.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_35.png)

## 标准化处理 📏

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_37.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_39.png)

标准化处理的目的是将不同量纲或量级的指标转换为统一的标准尺度，使其具有可比性。我们采用Z-score标准化方法。

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_41.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_43.png)

以下是标准化函数 `standardize` 的实现：

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_45.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_47.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_49.png)

```python
def standardize(series):
    # 计算数据的均值和标准差
    mean = series.mean()
    std = series.std()
    # 执行Z-score标准化：(值 - 均值) / 标准差
    return (series - mean) / std
```

该函数同样计算序列的均值和标准差。标准化的核心公式是 **`(x - μ) / σ`**，其中 `x` 是原始值，`μ` 是均值，`σ` 是标准差。经过处理的数据将服从均值为0、标准差为1的标准正态分布。

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_51.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_53.png)

## 中性化处理 ⚖️

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_55.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_57.png)

中性化处理的目的是消除因子数据中某些共性影响因素（如市值）的干扰，从而提取出因子“纯粹”的预测能力。我们通过线性回归来实现。

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_59.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_61.png)

以下是中性化函数 `neutralize` 的实现：

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_63.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_65.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_67.png)

```python
import statsmodels.api as sm

def neutralize(factor_series, market_cap_series):
    # Y是因变量，即待中性化的因子
    y = factor_series.astype(float)
    # X是自变量，即需要被剔除的影响因素（如市值）
    X = market_cap_series.astype(float)
    # 添加常数项，用于拟合截距
    X = sm.add_constant(X)
    # 使用普通最小二乘法进行线性回归
    model = sm.OLS(y, X).fit()
    # 返回残差，即剔除市值影响后的“纯净”因子值
    return model.resid
```

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_69.png)

在这个函数中：
1.  `factor_series` 是需要处理的因子数据（例如市净率）。
2.  `market_cap_series` 是需要被剔除的影响因素数据（例如市值）。
3.  我们建立线性回归模型 **`因子 = a * 市值 + b`**。
4.  模型的残差（`resid`）代表了因子中无法被市值解释的部分，即我们需要的“中性化”后的因子值。

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_71.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_73.png)

---

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_75.png)

![](img/b6f39f99bd12fd5ff0bee8e6b663b6e0_77.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤。首先，我们使用**三西格玛法**去除极端值的影响；接着，通过**Z-score标准化**将数据转换到统一尺度；最后，利用**线性回归中性化**剔除如市值等共性因素的干扰。完成这三步后，我们得到的因子数据将更加干净、可比，为后续的量化建模与分析奠定了坚实的基础。