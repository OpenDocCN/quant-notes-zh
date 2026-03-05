# Python金融分析与量化交易实战：P35：03-3-因子数据预处理 📊

![](img/ff20f2f50f9eede2df73e2179041642b_1.png)

![](img/ff20f2f50f9eede2df73e2179041642b_3.png)

![](img/ff20f2f50f9eede2df73e2179041642b_5.png)

在本节课中，我们将要学习因子数据预处理的核心三步操作：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续量化模型的稳定性和表现。

![](img/ff20f2f50f9eede2df73e2179041642b_7.png)

![](img/ff20f2f50f9eede2df73e2179041642b_9.png)

![](img/ff20f2f50f9eede2df73e2179041642b_11.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/ff20f2f50f9eede2df73e2179041642b_13.png)

## 第一步：去极值 🧹

![](img/ff20f2f50f9eede2df73e2179041642b_15.png)

![](img/ff20f2f50f9eede2df73e2179041642b_17.png)

去极值操作的目的是处理数据中的异常值或离群点，防止它们对整体分析产生过大影响。我们将采用简单有效的“三西格玛”方法。

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/ff20f2f50f9eede2df73e2179041642b_19.png)

![](img/ff20f2f50f9eede2df73e2179041642b_21.png)

```python
def filter_three_sigma(series, n=3):
    mean = series.mean()
    std = series.std()
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    return series.clip(lower_limit, upper_limit)
```

![](img/ff20f2f50f9eede2df73e2179041642b_23.png)

![](img/ff20f2f50f9eede2df73e2179041642b_25.png)

![](img/ff20f2f50f9eede2df73e2179041642b_27.png)

该函数计算序列的均值和标准差，然后设定上下限为均值加减三倍标准差，最后使用 `clip` 方法将超出此范围的值截断至边界。

![](img/ff20f2f50f9eede2df73e2179041642b_29.png)

![](img/ff20f2f50f9eede2df73e2179041642b_31.png)

## 第二步：标准化 📏

![](img/ff20f2f50f9eede2df73e2179041642b_33.png)

标准化旨在消除不同因子数据在量纲和量级上的差异，使其具有可比性。我们采用常见的Z-Score标准化方法。

![](img/ff20f2f50f9eede2df73e2179041642b_35.png)

![](img/ff20f2f50f9eede2df73e2179041642b_37.png)

以下是标准化函数 `standardize` 的实现步骤：

![](img/ff20f2f50f9eede2df73e2179041642b_39.png)

![](img/ff20f2f50f9eede2df73e2179041642b_41.png)

```python
def standardize(series):
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

该函数将序列中的每个值减去其均值，再除以其标准差，使得处理后的数据均值为0，标准差为1。

![](img/ff20f2f50f9eede2df73e2179041642b_43.png)

![](img/ff20f2f50f9eede2df73e2179041642b_45.png)

## 第三步：中性化 ⚖️

![](img/ff20f2f50f9eede2df73e2179041642b_47.png)

![](img/ff20f2f50f9eede2df73e2179041642b_49.png)

中性化是为了剔除某些系统性风险（如市值）对因子的影响，从而得到更“纯净”的因子暴露。我们通过线性回归来实现。

![](img/ff20f2f50f9eede2df73e2179041642b_51.png)

![](img/ff20f2f50f9eede2df73e2179041642b_53.png)

以下是中性化函数 `neutralize` 的实现步骤：

![](img/ff20f2f50f9eede2df73e2179041642b_55.png)

![](img/ff20f2f50f9eede2df73e2179041642b_57.png)

```python
import statsmodels.api as sm

def neutralize(factor_series, market_cap_series):
    # Y是因变量（因子），X是自变量（市值）
    Y = factor_series.astype(float)
    X = market_cap_series.astype(float)
    # 添加常数项，用于拟合截距
    X = sm.add_constant(X)
    # 使用普通最小二乘法进行线性回归
    model = sm.OLS(Y, X).fit()
    # 返回残差，即剔除市值影响后的“纯净”因子值
    return model.resid
```

![](img/ff20f2f50f9eede2df73e2179041642b_59.png)

该函数以因子序列和市值序列为输入，建立线性回归模型 `Y = a * X + b`，然后返回残差。残差代表了因子中无法被市值解释的部分，即完成了中性化。

![](img/ff20f2f50f9eede2df73e2179041642b_61.png)

![](img/ff20f2f50f9eede2df73e2179041642b_63.png)

---

![](img/ff20f2f50f9eede2df73e2179041642b_65.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤：使用**三西格玛法去极值**、使用**Z-Score方法标准化**以及通过**线性回归进行中性化**。掌握这些预处理技术是构建稳健量化模型的基础。