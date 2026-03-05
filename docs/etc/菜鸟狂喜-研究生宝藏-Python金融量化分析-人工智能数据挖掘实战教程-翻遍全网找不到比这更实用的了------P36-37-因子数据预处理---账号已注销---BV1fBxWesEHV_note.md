# Python金融量化分析：P36：因子数据预处理 📊

![](img/dc6cf38a2629e23c76af0780e72bb9e6_0.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_2.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_4.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_6.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_8.png)

在本节课中，我们将要学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能够帮助我们获得更可靠的分析结果。

![](img/dc6cf38a2629e23c76af0780e72bb9e6_10.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_12.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_14.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_16.png)

上一节我们介绍了如何查询因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/dc6cf38a2629e23c76af0780e72bb9e6_18.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_20.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_22.png)

## 第一步：去极值 🧹

![](img/dc6cf38a2629e23c76af0780e72bb9e6_24.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_26.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_28.png)

去极值，也称为处理离群值，目的是消除数据中异常大或异常小的极端值，防止它们对后续分析产生过大影响。我们将使用“三西格玛”方法来实现。

![](img/dc6cf38a2629e23c76af0780e72bb9e6_30.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_32.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_34.png)

以下是去极值操作的实现步骤：

![](img/dc6cf38a2629e23c76af0780e72bb9e6_36.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_38.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_40.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_42.png)

1.  计算数据的均值（`mean`）和标准差（`std`）。
2.  设定上限和下限。上限为 `均值 + 3 * 标准差`，下限为 `均值 - 3 * 标准差`。
3.  使用 `np.clip` 函数将超出界限的数据“修剪”到边界值，而不是直接删除。

![](img/dc6cf38a2629e23c76af0780e72bb9e6_44.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_46.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_48.png)

其核心公式和代码如下：

![](img/dc6cf38a2629e23c76af0780e72bb9e6_50.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_52.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_54.png)

```python
def filter_three_sigma(series, n=3):
    mean = series.mean()
    std = series.std()
    upper = mean + n * std
    lower = mean - n * std
    return np.clip(series, lower, upper)
```

![](img/dc6cf38a2629e23c76af0780e72bb9e6_56.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_58.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_60.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_62.png)

## 第二步：标准化 📏

![](img/dc6cf38a2629e23c76af0780e72bb9e6_64.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_66.png)

标准化操作的目的是将不同量纲或量级的指标数据转换到同一尺度，使其具有可比性。我们采用Z-score标准化方法。

![](img/dc6cf38a2629e23c76af0780e72bb9e6_68.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_70.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_72.png)

以下是标准化操作的实现步骤：

![](img/dc6cf38a2629e23c76af0780e72bb9e6_74.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_76.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_78.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_80.png)

1.  同样计算数据的均值（`mean`）和标准差（`std`）。
2.  对每个数据点应用公式：`(原始值 - 均值) / 标准差`。

![](img/dc6cf38a2629e23c76af0780e72bb9e6_82.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_84.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_86.png)

其核心公式和代码如下：

![](img/dc6cf38a2629e23c76af0780e72bb9e6_88.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_90.png)

```python
def standardize(series):
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/dc6cf38a2629e23c76af0780e72bb9e6_92.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_94.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_96.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_98.png)

## 第三步：中性化 🧪

![](img/dc6cf38a2629e23c76af0780e72bb9e6_100.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_102.png)

中性化操作的目的是剔除因子中受其他常见因素（如市值、行业）影响的部分，从而得到因子本身的“纯净”收益。我们通过线性回归来实现。

![](img/dc6cf38a2629e23c76af0780e72bb9e6_104.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_106.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_108.png)

以下是中性化操作的实现步骤：

![](img/dc6cf38a2629e23c76af0780e72bb9e6_110.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_112.png)

1.  确定自变量（X）和因变量（Y）。例如，我们希望剔除市值对市净率因子的影响，那么**Y是因子数据（市净率）**，**X是控制变量数据（市值）**。
2.  使用 `statsmodels` 库的普通最小二乘法（OLS）建立回归模型 `Y = W * X + b`。
3.  计算残差，即 `真实Y值 - 模型预测的Y值`。这个残差就是剔除了X（市值）影响后的中性化因子。

![](img/dc6cf38a2629e23c76af0780e72bb9e6_114.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_116.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_118.png)

其核心代码如下：

![](img/dc6cf38a2629e23c76af0780e72bb9e6_120.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_122.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_124.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_126.png)

```python
import statsmodels.api as sm

![](img/dc6cf38a2629e23c76af0780e72bb9e6_128.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_130.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_132.png)

def neutralize(factor_series, control_series):
    # Y: 需要中性化的因子
    y = factor_series.astype(float)
    # X: 控制变量（如市值），需要添加常数项
    x = sm.add_constant(control_series.astype(float))

    model = sm.OLS(y, x).fit()
    # 返回残差，即中性化后的因子
    return model.resid
```

![](img/dc6cf38a2629e23c76af0780e72bb9e6_134.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_136.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_138.png)

---

![](img/dc6cf38a2629e23c76af0780e72bb9e6_140.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_142.png)

![](img/dc6cf38a2629e23c76af0780e72bb9e6_144.png)

本节课中我们一起学习了因子数据预处理的三个关键步骤。我们首先通过**去极值**处理了异常数据，然后通过**标准化**统一了数据尺度，最后通过**中性化**回归剔除了其他常见因素的影响。完成这三步后，我们就得到了干净、可比、更能反映因子自身特性的数据，为后续的量化分析和建模打下了坚实的基础。