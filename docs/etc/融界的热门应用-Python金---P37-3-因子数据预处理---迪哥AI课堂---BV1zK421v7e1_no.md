# Python金融分析量化交易：P37：3-因子数据预处理 📊

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_0.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_2.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_4.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_6.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_8.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_10.png)

在本节课中，我们将要学习因子数据预处理的核心三步操作：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续量化模型的稳定性和准确性。

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_12.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_14.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_16.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_18.png)

上一节我们完成了因子数据的查询，本节中我们来看看如何对这些原始数据进行预处理。

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_20.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_22.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_24.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_26.png)

## 第一步：去极值操作 🧹

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_28.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_30.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_32.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_34.png)

去极值操作的目的是处理数据中的异常值或离群点，防止它们对整体分析产生过大影响。我们将采用**三西格玛（3-Sigma）** 方法来实现。

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_36.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_38.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_40.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_42.png)

以下是去极值函数 `filter_three_sigma` 的代码实现：

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_44.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_46.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_48.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_50.png)

```python
def filter_three_sigma(series, n=3):
    mean = series.mean()
    std = series.std()
    max_range = mean + n * std
    min_range = mean - n * std
    return series.clip(min_range, max_range)
```

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_52.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_54.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_56.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_58.png)

该函数计算序列的均值和标准差，然后设定上限和下限（均值 ± n倍标准差），最后使用 `clip` 方法将超出范围的值截断至边界，而非直接删除。

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_60.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_62.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_64.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_66.png)

## 第二步：标准化操作 📏

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_68.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_70.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_72.png)

标准化（或称为Z-Score标准化）的目的是将数据转换为均值为0、标准差为1的分布，使得不同量纲或量级的指标可以进行比较和加权。

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_74.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_76.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_78.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_80.png)

以下是标准化函数 `standardize` 的代码实现：

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_82.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_84.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_86.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_88.png)

```python
def standardize(series):
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_90.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_92.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_94.png)

其核心公式为：
**`z = (x - μ) / σ`**
其中，`x` 是原始值，`μ` 是序列均值，`σ` 是序列标准差。

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_96.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_98.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_100.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_102.png)

## 第三步：中性化操作 ⚖️

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_104.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_106.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_108.png)

中性化操作的目的是消除其他常见风险因子（如市值）对当前因子的影响，从而提取出因子“纯粹”的预测能力。我们通过线性回归来实现。

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_110.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_112.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_114.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_116.png)

假设我们要分析市净率因子，并希望剔除市值对它的影响。以下是中性化函数 `neutralize` 的代码实现：

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_118.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_120.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_122.png)

```python
import statsmodels.api as sm

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_124.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_126.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_128.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_130.png)

def neutralize(factor, market_value):
    # 将数据转换为float类型以满足模型要求
    y = factor.astype(float)
    X = market_value.astype(float)
    # 添加常数项（截距）
    X = sm.add_constant(X)
    # 使用普通最小二乘法进行线性回归
    model = sm.OLS(y, X).fit()
    # 返回残差，即剔除市值影响后的“纯净”因子值
    return model.resid
```

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_132.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_134.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_136.png)

在该函数中：
*   **`y`**：是因变量，即我们需要中性化的因子（如市净率）。
*   **`X`**：是自变量，即我们希望剔除其影响的因子（如市值）。
*   模型拟合后，**`model.resid`**（残差）即为真实值与模型预测值之间的差异，它代表了剔除市值影响后因子自身的变化部分。

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_138.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_140.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_142.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_144.png)

---

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_146.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_148.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_150.png)

![](img/779cebcb8f2b7772e761ac2d1ad90c7b_151.png)

本节课中我们一起学习了因子数据预处理的三个关键步骤。首先，我们使用**三西格玛法**去除极端值；其次，通过**Z-Score标准化**统一数据尺度；最后，借助**线性回归进行中性化**处理，剥离了市值等常见风险因子的干扰。完成这三步后，我们得到的因子数据更为“干净”和“纯粹”，为后续构建有效的量化选股模型奠定了坚实的基础。