# Python金融量化分析：P37：因子数据预处理 📊

![](img/4e50700acabd521218ff743aff4b8093_0.png)

![](img/4e50700acabd521218ff743aff4b8093_2.png)

![](img/4e50700acabd521218ff743aff4b8093_4.png)

![](img/4e50700acabd521218ff743aff4b8093_5.png)

![](img/4e50700acabd521218ff743aff4b8093_6.png)

在本节课中，我们将学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能帮助我们获得更可靠的分析结果。

![](img/4e50700acabd521218ff743aff4b8093_8.png)

![](img/4e50700acabd521218ff743aff4b8093_9.png)

![](img/4e50700acabd521218ff743aff4b8093_10.png)

![](img/4e50700acabd521218ff743aff4b8093_11.png)

上一节我们完成了指标的查询，本节中我们来看看如何对这些指标数据进行预处理。

![](img/4e50700acabd521218ff743aff4b8093_12.png)

![](img/4e50700acabd521218ff743aff4b8093_13.png)

## 第一步：去极值处理 🧹

![](img/4e50700acabd521218ff743aff4b8093_15.png)

![](img/4e50700acabd521218ff743aff4b8093_16.png)

![](img/4e50700acabd521218ff743aff4b8093_17.png)

![](img/4e50700acabd521218ff743aff4b8093_18.png)

去极值处理的目的是消除数据中异常值的影响。我们将采用常用的3西格玛方法。

![](img/4e50700acabd521218ff743aff4b8093_20.png)

![](img/4e50700acabd521218ff743aff4b8093_21.png)

![](img/4e50700acabd521218ff743aff4b8093_22.png)

以下是去极值操作的实现步骤：

![](img/4e50700acabd521218ff743aff4b8093_24.png)

![](img/4e50700acabd521218ff743aff4b8093_25.png)

![](img/4e50700acabd521218ff743aff4b8093_27.png)

1.  计算数据的均值（`mean`）和标准差（`std`）。
2.  根据3西格玛原则，计算数据的上限（`upper_limit`）和下限（`lower_limit`）。
    *   上限公式：`mean + 3 * std`
    *   下限公式：`mean - 3 * std`
3.  使用 `np.clip` 函数将超出界限的数据值规范到边界内。

![](img/4e50700acabd521218ff743aff4b8093_28.png)

![](img/4e50700acabd521218ff743aff4b8093_29.png)

![](img/4e50700acabd521218ff743aff4b8093_31.png)

![](img/4e50700acabd521218ff743aff4b8093_32.png)

```python
def filter_3sigma(data, n=3):
    """
    使用3西格玛方法去除极值
    :param data: 输入的数据序列
    :param n: 西格玛倍数，默认为3
    :return: 处理后的数据
    """
    mean = data.mean()
    std = data.std()
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    return np.clip(data, lower_limit, upper_limit)
```

![](img/4e50700acabd521218ff743aff4b8093_34.png)

![](img/4e50700acabd521218ff743aff4b8093_35.png)

![](img/4e50700acabd521218ff743aff4b8093_36.png)

## 第二步：标准化处理 📏

![](img/4e50700acabd521218ff743aff4b8093_38.png)

![](img/4e50700acabd521218ff743aff4b8093_40.png)

![](img/4e50700acabd521218ff743aff4b8093_41.png)

![](img/4e50700acabd521218ff743aff4b8093_43.png)

标准化处理的目的是将数据缩放到均值为0、标准差为1的标准正态分布，便于不同量纲的因子进行比较。

![](img/4e50700acabd521218ff743aff4b8093_45.png)

![](img/4e50700acabd521218ff743aff4b8093_46.png)

以下是标准化操作的实现步骤：

![](img/4e50700acabd521218ff743aff4b8093_48.png)

![](img/4e50700acabd521218ff743aff4b8093_49.png)

1.  计算数据的均值（`mean`）和标准差（`std`）。
2.  对每个数据点应用标准化公式：`(data - mean) / std`

![](img/4e50700acabd521218ff743aff4b8093_51.png)

![](img/4e50700acabd521218ff743aff4b8093_52.png)

![](img/4e50700acabd521218ff743aff4b8093_53.png)

```python
def standardize(data):
    """
    对数据进行标准化处理
    :param data: 输入的数据序列
    :return: 标准化后的数据
    """
    mean = data.mean()
    std = data.std()
    return (data - mean) / std
```

![](img/4e50700acabd521218ff743aff4b8093_55.png)

![](img/4e50700acabd521218ff743aff4b8093_56.png)

## 第三步：中性化处理 ⚖️

![](img/4e50700acabd521218ff743aff4b8093_58.png)

![](img/4e50700acabd521218ff743aff4b8093_59.png)

![](img/4e50700acabd521218ff743aff4b8093_60.png)

中性化处理的目的是消除其他已知变量（如市值）对当前因子的影响，从而提取出因子“纯粹”的预测能力。我们通过线性回归来实现。

![](img/4e50700acabd521218ff743aff4b8093_62.png)

![](img/4e50700acabd521218ff743aff4b8093_63.png)

![](img/4e50700acabd521218ff743aff4b8093_64.png)

以下是中性化操作的实现步骤：

![](img/4e50700acabd521218ff743aff4b8093_66.png)

1.  明确自变量（X）和因变量（Y）。在本例中，X是市值，Y是需要中性化的因子（如市净率）。
2.  使用 `statsmodels` 库的普通最小二乘法（OLS）建立回归模型：`Y = W * X + b`。
3.  计算模型的残差（`resid`），即真实Y值与模型预测值之间的差异。这个残差就是去除了市值影响后的“中性化”因子值。

![](img/4e50700acabd521218ff743aff4b8093_68.png)

![](img/4e50700acabd521218ff743aff4b8093_69.png)

![](img/4e50700acabd521218ff743aff4b8093_70.png)

![](img/4e50700acabd521218ff743aff4b8093_71.png)

![](img/4e50700acabd521218ff743aff4b8093_72.png)

![](img/4e50700acabd521218ff743aff4b8093_73.png)

```python
import statsmodels.api as sm

![](img/4e50700acabd521218ff743aff4b8093_74.png)

![](img/4e50700acabd521218ff743aff4b8093_76.png)

def neutralize(factor, market_value):
    """
    对因子进行市值中性化处理
    :param factor: 需要中性化的因子序列（如市净率）
    :param market_value: 作为自变量的市值序列
    :return: 中性化后的因子序列（残差）
    """
    # 将数据转换为float类型以满足模型要求
    Y = factor.astype(float)
    X = market_value.astype(float)
    # 添加常数项以拟合截距b
    X = sm.add_constant(X)
    # 建立OLS回归模型并拟合
    model = sm.OLS(Y, X).fit()
    # 返回残差，即中性化后的因子
    return model.resid
```

![](img/4e50700acabd521218ff743aff4b8093_77.png)

![](img/4e50700acabd521218ff743aff4b8093_78.png)

![](img/4e50700acabd521218ff743aff4b8093_79.png)

![](img/4e50700acabd521218ff743aff4b8093_80.png)

![](img/4e50700acabd521218ff743aff4b8093_81.png)

本节课中我们一起学习了因子数据预处理的三个关键步骤：使用3西格玛法**去极值**、通过 `(x - mean)/std` 公式进行**标准化**，以及利用线性回归残差实现**市值中性化**。掌握这些方法能有效提升金融量化模型中因子的质量与稳定性。