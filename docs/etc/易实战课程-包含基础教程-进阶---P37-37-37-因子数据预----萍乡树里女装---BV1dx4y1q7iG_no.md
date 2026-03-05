# 量化交易教程：P37：因子数据预处理 📊

![](img/f21c88113dc7616a18b11c724240c325_0.png)

![](img/f21c88113dc7616a18b11c724240c325_2.png)

![](img/f21c88113dc7616a18b11c724240c325_4.png)

![](img/f21c88113dc7616a18b11c724240c325_5.png)

![](img/f21c88113dc7616a18b11c724240c325_6.png)

在本节课中，我们将学习因子数据预处理的三个核心步骤：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能确保后续分析的准确性和有效性。

![](img/f21c88113dc7616a18b11c724240c325_8.png)

![](img/f21c88113dc7616a18b11c724240c325_9.png)

![](img/f21c88113dc7616a18b11c724240c325_10.png)

![](img/f21c88113dc7616a18b11c724240c325_11.png)

![](img/f21c88113dc7616a18b11c724240c325_12.png)

上一节我们介绍了如何查询因子数据，本节中我们来看看如何对这些数据进行预处理。

![](img/f21c88113dc7616a18b11c724240c325_13.png)

![](img/f21c88113dc7616a18b11c724240c325_15.png)

![](img/f21c88113dc7616a18b11c724240c325_16.png)

## 第一步：去极值 🧹

![](img/f21c88113dc7616a18b11c724240c325_17.png)

![](img/f21c88113dc7616a18b11c724240c325_18.png)

![](img/f21c88113dc7616a18b11c724240c325_20.png)

![](img/f21c88113dc7616a18b11c724240c325_21.png)

![](img/f21c88113dc7616a18b11c724240c325_22.png)

去极值的目的是处理数据中的异常值，防止它们对分析结果产生过大影响。我们将使用**3-Sigma原则**来实现。

![](img/f21c88113dc7616a18b11c724240c325_24.png)

![](img/f21c88113dc7616a18b11c724240c325_25.png)

以下是去极值操作的代码实现：

![](img/f21c88113dc7616a18b11c724240c325_27.png)

![](img/f21c88113dc7616a18b11c724240c325_28.png)

![](img/f21c88113dc7616a18b11c724240c325_29.png)

![](img/f21c88113dc7616a18b11c724240c325_31.png)

```python
def filter_3sigma(factor, n=3):
    """
    使用3-Sigma原则去除因子数据中的极值。
    :param factor: 输入的因子数据（Pandas Series或DataFrame）
    :param n: Sigma倍数，默认为3
    :return: 处理后的因子数据
    """
    # 计算均值和标准差
    mean = factor.mean()
    std = factor.std()

    # 计算上限和下限
    upper_bound = mean + n * std
    lower_bound = mean - n * std

    # 将超出界限的值替换为界限值
    factor_filtered = factor.clip(lower=lower_bound, upper=upper_bound)
    return factor_filtered
```

![](img/f21c88113dc7616a18b11c724240c325_33.png)

![](img/f21c88113dc7616a18b11c724240c325_34.png)

![](img/f21c88113dc7616a18b11c724240c325_35.png)

![](img/f21c88113dc7616a18b11c724240c325_37.png)

## 第二步：标准化 📏

![](img/f21c88113dc7616a18b11c724240c325_39.png)

![](img/f21c88113dc7616a18b11c724240c325_40.png)

![](img/f21c88113dc7616a18b11c724240c325_42.png)

![](img/f21c88113dc7616a18b11c724240c325_43.png)

标准化的目的是将数据缩放到一个统一的尺度，通常是均值为0、标准差为1。这有助于不同因子之间的比较。

![](img/f21c88113dc7616a18b11c724240c325_45.png)

![](img/f21c88113dc7616a18b11c724240c325_46.png)

以下是标准化操作的代码实现：

![](img/f21c88113dc7616a18b11c724240c325_48.png)

![](img/f21c88113dc7616a18b11c724240c325_49.png)

![](img/f21c88113dc7616a18b11c724240c325_50.png)

![](img/f21c88113dc7616a18b11c724240c325_52.png)

```python
def standardize(factor):
    """
    对因子数据进行标准化处理（Z-Score标准化）。
    :param factor: 输入的因子数据
    :return: 标准化后的因子数据
    """
    # 计算均值和标准差
    mean = factor.mean()
    std = factor.std()

    # 执行标准化公式: (x - mean) / std
    factor_standardized = (factor - mean) / std
    return factor_standardized
```

![](img/f21c88113dc7616a18b11c724240c325_54.png)

![](img/f21c88113dc7616a18b11c724240c325_55.png)

![](img/f21c88113dc7616a18b11c724240c325_56.png)

![](img/f21c88113dc7616a18b11c724240c325_57.png)

## 第三步：中性化 ⚖️

![](img/f21c88113dc7616a18b11c724240c325_59.png)

![](img/f21c88113dc7616a18b11c724240c325_60.png)

![](img/f21c88113dc7616a18b11c724240c325_62.png)

中性化的目的是消除其他已知变量（如市值）对当前因子的影响，从而提取出因子的“纯粹”信息。我们通过线性回归来实现。

![](img/f21c88113dc7616a18b11c724240c325_64.png)

![](img/f21c88113dc7616a18b11c724240c325_65.png)

![](img/f21c88113dc7616a18b11c724240c325_66.png)

以下是中性化操作的代码实现：

![](img/f21c88113dc7616a18b11c724240c325_68.png)

![](img/f21c88113dc7616a18b11c724240c325_69.png)

![](img/f21c88113dc7616a18b11c724240c325_70.png)

![](img/f21c88113dc7616a18b11c724240c325_72.png)

```python
import statsmodels.api as sm

![](img/f21c88113dc7616a18b11c724240c325_74.png)

![](img/f21c88113dc7616a18b11c724240c325_75.png)

def neutralize(factor, market_cap):
    """
    对因子进行市值中性化处理。
    :param factor: 待中性化的因子数据（Y）
    :param market_cap: 市值数据（X）
    :return: 中性化后的因子残差
    """
    # 确保数据类型为浮点型
    Y = factor.astype(float)
    X = market_cap.astype(float)

    # 为X添加常数项（截距）
    X = sm.add_constant(X)

    # 使用普通最小二乘法（OLS）进行线性回归
    model = sm.OLS(Y, X).fit()

    # 返回残差，即去除市值影响后的“纯净”因子
    return model.resid
```

![](img/f21c88113dc7616a18b11c724240c325_76.png)

![](img/f21c88113dc7616a18b11c724240c325_77.png)

![](img/f21c88113dc7616a18b11c724240c325_78.png)

![](img/f21c88113dc7616a18b11c724240c325_79.png)

![](img/f21c88113dc7616a18b11c724240c325_80.png)

## 综合应用与总结 🎯

![](img/f21c88113dc7616a18b11c724240c325_82.png)

![](img/f21c88113dc7616a18b11c724240c325_83.png)

![](img/f21c88113dc7616a18b11c724240c325_84.png)

![](img/f21c88113dc7616a18b11c724240c325_85.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤。首先，我们使用**3-Sigma方法**去除极端值；其次，通过**Z-Score标准化**将数据调整到统一尺度；最后，利用**线性回归**进行市值中性化，提取因子的独立信息。掌握这些预处理技术是构建稳健量化模型的基础。