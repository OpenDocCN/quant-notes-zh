# 人生苦短，我学量化！：P32：因子数据预处理

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_0.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_2.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_4.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_0.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_6.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_8.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_10.png)

在本节课中，我们将要学习因子数据预处理的核心三步操作：去极值、标准化和中性化。这些步骤对于清洗和准备金融数据至关重要，能有效提升后续量化模型分析的准确性和稳定性。

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_12.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_14.png)

上一节我们完成了指标的查询，接下来就需要对查询到的数据进行预处理操作。预处理操作遵循之前提到的三步走策略。

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_16.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_18.png)

## 第一步：去极值

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_20.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_22.png)

去极值是为了处理数据中的异常值或离群点。方法有很多种，我们选择简单实用的**三西格玛（3-Sigma）** 方法。

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_24.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_26.png)

以下是去极值操作的实现步骤：

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_28.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_30.png)

1.  计算数据的均值（mean）和标准差（std）。
2.  设定上限和下限。上限为 `均值 + 3 * 标准差`，下限为 `均值 - 3 * 标准差`。
3.  将超出界限的数据值调整到边界上，而不是直接删除，以保留数据分布的整体性。

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_32.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_34.png)

其核心公式可以表示为：
`上限 = mean + n * std`
`下限 = mean - n * std`
（其中 n 通常取 3）

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_36.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_38.png)

对应的代码实现如下：
```python
def filter_extreme(series, n=3):
    mean = series.mean()
    std = series.std()
    max_range = mean + n * std
    min_range = mean - n * std
    return series.clip(min_range, max_range)
```

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_40.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_42.png)

## 第二步：标准化

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_44.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_46.png)

标准化（Standardization）的目的是将不同量纲或量级的指标数据转换到统一的尺度上，便于比较和计算。我们采用 **Z-Score** 标准化方法。

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_48.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_50.png)

以下是标准化操作的实现步骤：

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_52.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_54.png)

1.  同样，计算数据的均值（mean）和标准差（std）。
2.  对每个数据点，执行 `(值 - 均值) / 标准差` 的操作。

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_56.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_58.png)

其核心公式为：
`z = (x - mean) / std`

对应的代码实现如下：
```python
def standardize(series):
    mean = series.mean()
    std = series.std()
    return (series - mean) / std
```

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_60.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_62.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_64.png)

## 第三步：中性化

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_66.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_68.png)

中性化是为了消除因子数据中某些共性因素的影响（例如市值对估值因子的影响），从而提取出因子本身的“纯净”信息。我们通过**线性回归**来实现。

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_70.png)

以消除市值对市净率（PB）因子的影响为例：
*   **Y（因变量）**：需要中性化的因子（如市净率）。
*   **X（自变量）**：需要被消除的影响因素（如市值）。

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_72.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_74.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_76.png)

以下是中性化操作的实现步骤：

1.  建立线性回归模型 `Y = a * X + b`。
2.  使用最小二乘法拟合模型，得到预测值 `Y_pred`。
3.  计算残差 `residual = Y - Y_pred`。这个残差就是去除了市值影响后的“中性化”因子值。

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_78.png)

我们使用 `statsmodels` 库来便捷地完成回归和残差计算。

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_80.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_82.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_84.png)

对应的代码实现如下：
```python
import statsmodels.api as sm

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_86.png)

def neutralize(factor_series, market_cap_series):
    # Y: 因子数据
    y = factor_series.values.astype(float)
    # X: 需要被中性化的指标数据（如市值），并添加常数项
    x = sm.add_constant(market_cap_series.values.astype(float))
    # 建立并拟合普通最小二乘模型
    model = sm.OLS(y, x)
    result = model.fit()
    # 返回残差，即中性化后的因子
    return result.resid
```

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_88.png)

![](img/f8ac0dab9beb8980b488b8ab97a5c0d3_89.png)

本节课中我们一起学习了因子数据预处理的三个核心步骤：使用三西格玛法**去极值**、使用Z-Score方法进行**标准化**，以及通过线性回归进行**中性化**。掌握这些数据处理技术，是构建稳健量化模型的重要基础。接下来，我们就可以使用处理好的“干净”因子进行更深入的分析和策略构建了。