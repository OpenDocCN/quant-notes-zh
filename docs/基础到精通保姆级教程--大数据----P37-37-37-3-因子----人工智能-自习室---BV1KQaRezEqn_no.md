# Python金融量化交易：P37：因子数据预处理 📊

![](img/ffd42714b4cddc6355dc6f2a207e0d06_0.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_2.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_4.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_6.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_8.png)

在本节课中，我们将要学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能帮助我们获得更可靠、更具可比性的因子，为后续的量化模型分析打下坚实基础。

![](img/ffd42714b4cddc6355dc6f2a207e0d06_10.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_12.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_14.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_16.png)

上一节我们介绍了如何查询和获取因子数据，本节中我们来看看如何对这些原始数据进行预处理。

![](img/ffd42714b4cddc6355dc6f2a207e0d06_18.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_20.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_22.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_24.png)

## 第一步：去极值 🧹

![](img/ffd42714b4cddc6355dc6f2a207e0d06_26.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_28.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_30.png)

去极值，也称为处理离群值，目的是消除数据中异常大或异常小的极端值，防止它们对后续分析产生过大影响。我们将使用“三西格玛”方法来实现。

![](img/ffd42714b4cddc6355dc6f2a207e0d06_32.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_34.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_36.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_38.png)

以下是去极值操作的实现步骤：

![](img/ffd42714b4cddc6355dc6f2a207e0d06_40.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_42.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_44.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_46.png)

1.  计算数据的均值（`mean`）和标准差（`std`）。
2.  设定上限和下限。上限为 `均值 + 3 * 标准差`，下限为 `均值 - 3 * 标准差`。
3.  将超出上限或下限的数据值，分别替换为上限值或下限值，而不是直接删除。

![](img/ffd42714b4cddc6355dc6f2a207e0d06_48.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_50.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_52.png)

```python
import numpy as np

![](img/ffd42714b4cddc6355dc6f2a207e0d06_54.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_56.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_58.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_60.png)

def filter_extreme_3sigma(series, n=3):
    """
    使用三西格玛法去除极值
    :param series: 待处理的数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据序列
    """
    mean = series.mean()
    std = series.std()
    max_range = mean + n * std
    min_range = mean - n * std
    # 使用np.clip将超出范围的值替换为边界值
    return np.clip(series, min_range, max_range)
```

![](img/ffd42714b4cddc6355dc6f2a207e0d06_62.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_64.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_66.png)

## 第二步：标准化 📏

![](img/ffd42714b4cddc6355dc6f2a207e0d06_68.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_70.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_72.png)

标准化是将数据按比例缩放，使之落入一个特定的区间（通常是均值为0，标准差为1），以消除不同因子之间量纲和数值范围的影响，使它们具有可比性。

![](img/ffd42714b4cddc6355dc6f2a207e0d06_74.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_76.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_78.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_80.png)

以下是标准化操作的实现步骤：

![](img/ffd42714b4cddc6355dc6f2a207e0d06_82.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_84.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_86.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_88.png)

1.  计算数据的均值（`mean`）和标准差（`std`）。
2.  对每个数据点执行操作：`(原始值 - 均值) / 标准差`。

![](img/ffd42714b4cddc6355dc6f2a207e0d06_90.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_92.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_94.png)

```python
def standardize(series):
    """
    对数据进行标准化（Z-Score标准化）
    :param series: 待处理的数据序列
    :return: 标准化后的数据序列
    """
    mean = series.mean()
    std = series.std()
    # 执行 (x - mean) / std 操作
    return (series - mean) / std
```

![](img/ffd42714b4cddc6355dc6f2a207e0d06_96.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_98.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_100.png)

## 第三步：中性化 🧪

![](img/ffd42714b4cddc6355dc6f2a207e0d06_102.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_104.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_106.png)

中性化是为了消除因子中与某些已知风险因素（如市值、行业）相关的部分，从而得到因子本身的“纯净”预测能力。我们以消除市值影响为例，使用线性回归方法。

![](img/ffd42714b4cddc6355dc6f2a207e0d06_108.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_110.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_112.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_114.png)

以下是中性化操作的实现步骤：

![](img/ffd42714b4cddc6355dc6f2a207e0d06_116.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_118.png)

1.  将待处理的因子（如市净率）作为因变量 `Y`。
2.  将需要被剥离的影响因素（如市值）作为自变量 `X`。
3.  使用最小二乘法（OLS）进行线性回归，拟合 `Y = aX + b` 的模型。
4.  计算残差，即 `残差 = 真实Y值 - 模型预测的Y值`。这个残差就是剔除了市值影响后的“中性化”因子。

![](img/ffd42714b4cddc6355dc6f2a207e0d06_120.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_122.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_124.png)

```python
import statsmodels.api as sm

![](img/ffd42714b4cddc6355dc6f2a207e0d06_126.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_128.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_130.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_132.png)

def neutralize(factor_series, market_cap_series):
    """
    对因子进行市值中性化处理
    :param factor_series: 待中性化的因子数据（如市净率）
    :param market_cap_series: 作为自变量的数据（如市值）
    :return: 中性化后的因子数据（残差序列）
    """
    # 确保数据类型为浮点型
    Y = factor_series.astype(float)
    X = market_cap_series.astype(float)
    # 添加常数项，用于拟合截距b
    X = sm.add_constant(X)
    # 使用最小二乘法进行线性回归
    model = sm.OLS(Y, X).fit()
    # 返回残差，即中性化后的因子
    return model.resid
```

![](img/ffd42714b4cddc6355dc6f2a207e0d06_134.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_136.png)

---

![](img/ffd42714b4cddc6355dc6f2a207e0d06_138.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_140.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_142.png)

![](img/ffd42714b4cddc6355dc6f2a207e0d06_144.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤。首先，我们使用**三西格玛法**去除数据中的极端值；接着，通过**Z-Score标准化**使不同因子具有可比性；最后，利用**线性回归**对因子进行**市值中性化**处理，剥离其与市值的相关性。通过这三步操作，我们能够将原始的、粗糙的因子数据转化为干净、可比、更具分析价值的“纯净”因子，为构建有效的量化交易策略做好准备。