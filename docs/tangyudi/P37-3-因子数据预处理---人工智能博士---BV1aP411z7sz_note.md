# 人工智能因子数据处理：P37：3-因子数据预处理 📊

![](img/e516f8bde93c083e9fafcbebdc9490fd_1.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_3.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_5.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_7.png)

在本节课中，我们将要学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能帮助我们构建更稳健的量化模型。

![](img/e516f8bde93c083e9fafcbebdc9490fd_9.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_11.png)

上一节我们介绍了如何计算和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/e516f8bde93c083e9fafcbebdc9490fd_13.png)

## 第一步：去极值操作 🧹

![](img/e516f8bde93c083e9fafcbebdc9490fd_15.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_17.png)

去极值操作的目的是处理数据中的异常值或离群点，防止它们对后续分析产生过大影响。我们将使用“三西格玛”法则来实现。

以下是去极值函数 `filter_three_sigma` 的实现步骤：

![](img/e516f8bde93c083e9fafcbebdc9490fd_19.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_21.png)

1.  计算传入数据序列的均值（`mean`）和标准差（`std`）。
2.  根据三西格玛法则，计算数据的上下限：
    *   上限 = `mean + n * std`
    *   下限 = `mean - n * std`
3.  使用 `np.clip` 函数将超出上下限的值截断至边界，而不是直接删除。

![](img/e516f8bde93c083e9fafcbebdc9490fd_23.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_25.png)

```python
def filter_three_sigma(series, n=3):
    mean = series.mean()
    std = series.std()
    upper = mean + n * std
    lower = mean - n * std
    return np.clip(series, lower, upper)
```

![](img/e516f8bde93c083e9fafcbebdc9490fd_27.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_29.png)

## 第二步：标准化操作 📏

![](img/e516f8bde93c083e9fafcbebdc9490fd_31.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_33.png)

标准化操作的目的是将不同量纲或量级的指标数据转换到同一尺度，便于比较和计算。我们采用最常见的Z-Score标准化方法。

![](img/e516f8bde93c083e9fafcbebdc9490fd_35.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_37.png)

以下是标准化函数 `standardize` 的实现步骤：

![](img/e516f8bde93c083e9fafcbebdc9490fd_39.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_41.png)

1.  同样，计算数据序列的均值（`mean`）和标准差（`std`）。
2.  对序列中的每一个值进行变换：`(值 - 均值) / 标准差`。

![](img/e516f8bde93c083e9fafcbebdc9490fd_43.png)

```python
def standardize(series):
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

## 第三步：中性化操作 ⚖️

![](img/e516f8bde93c083e9fafcbebdc9490fd_45.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_47.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_49.png)

中性化操作的目的是剔除因子中与其他风格因子（如市值）相关的部分，从而得到因子本身的“纯净”收益。我们通过线性回归来实现。

![](img/e516f8bde93c083e9fafcbebdc9490fd_51.png)

在本例中，我们以“市净率”作为待处理的因子（`factor`），以“市值”作为需要被剥离的风格因子（`x`）。

![](img/e516f8bde93c083e9fafcbebdc9490fd_53.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_55.png)

以下是中性化函数 `neutralize` 的实现步骤：

![](img/e516f8bde93c083e9fafcbebdc9490fd_57.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_59.png)

1.  导入 `statsmodels` 库，使用其普通最小二乘法（OLS）进行线性回归。
2.  构建回归方程：`factor = a * market_cap + b`。
3.  拟合模型后，模型的残差（`resid`）即为剔除了市值影响后的“中性化”因子值。

```python
import statsmodels.api as sm

def neutralize(factor, market_cap):
    # 为自变量添加常数项（截距）
    X = sm.add_constant(market_cap.astype(float))
    y = factor.astype(float)
    # 构建并拟合OLS模型
    model = sm.OLS(y, X).fit()
    # 返回残差，即中性化后的因子
    return model.resid
```

![](img/e516f8bde93c083e9fafcbebdc9490fd_61.png)

![](img/e516f8bde93c083e9fafcbebdc9490fd_63.png)

---

![](img/e516f8bde93c083e9fafcbebdc9490fd_65.png)

本节课中我们一起学习了因子数据预处理的三个关键步骤。首先，我们使用三西格玛法去除极端值；其次，通过Z-Score方法对数据进行标准化；最后，利用线性回归进行因子中性化，以剥离特定风格（如市值）的影响。掌握这三步是构建有效量化因子模型的重要基础。