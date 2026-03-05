# Python金融量化：P32：标准化处理方法 📊

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_1.png)

在本节课中，我们将要学习数据预处理中一个非常重要的技术——标准化。标准化是数据分析和机器学习中常用的方法，旨在消除不同特征（或指标）之间由于量纲和取值范围不同带来的影响，使数据更易于比较和分析。

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_3.png)

## 标准化的目的与原理

上一节我们介绍了数据预处理的重要性，本节中我们来看看标准化的具体原理。

标准化操作的核心目标是使不同维度的数据具有可比性。例如，在一个数据集中，我们可能有多个指标（如X1和X2）。这些指标的取值范围可能差异很大。如果直接使用原始数据进行分析，取值范围大的指标可能会对结果产生不成比例的影响，从而掩盖了其他指标的作用。

标准化的公式如下：

**`z = (x - μ) / σ`**

其中：
*   **`x`** 是原始数据。
*   **`μ`** 是该维度数据的均值。
*   **`σ`** 是该维度数据的标准差。

这个公式可以分解为两个步骤：

1.  **去均值**：`(x - μ)`。这一步将数据的中心移动到零点，使得数据在各个维度上以零为中心对称。
2.  **除标准差**：`/ σ`。这一步根据数据的离散程度（标准差）进行缩放。取值范围大、离散程度高的数据，其标准差也大，除以较大的标准差后，其缩放幅度较大；反之，取值范围小的数据除以较小的标准差，缩放幅度较小。最终目的是让所有维度的数据都缩放到一个相近的数值范围内。

通过这两个步骤，标准化后的数据具有以下特点：
*   均值为0。
*   标准差为1。
*   各维度数据处于相似的尺度上。

## 标准化的代码实现

理解了原理之后，我们来看看如何用代码实现标准化。

以下是使用Python和Pandas实现标准化的一个简单函数：

```python
def standardize_series(series):
    """
    对输入的Pandas Series进行标准化处理。
    参数:
        series: 待标准化的Pandas Series数据。
    返回:
        标准化后的Pandas Series数据。
    """
    # 计算均值和标准差
    mean_val = series.mean()
    std_val = series.std()
    # 应用标准化公式： (x - mean) / std
    standardized_series = (series - mean_val) / std_val
    return standardized_series
```

代码非常简单：首先计算数据的均值和标准差，然后应用标准化公式即可。

## 使用Scikit-learn进行标准化

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_5.png)

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_7.png)

除了手动实现，我们也可以使用成熟的机器学习库来快速完成标准化。

在Scikit-learn库的`preprocessing`模块中，提供了`StandardScaler`工具，可以更方便地进行标准化，尤其适用于处理整个DataFrame。

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_9.png)

以下是使用Scikit-learn进行标准化的示例：

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_11.png)

```python
from sklearn.preprocessing import StandardScaler
import pandas as pd

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_13.png)

# 假设df是你的DataFrame
scaler = StandardScaler()
# 拟合数据并转换
standardized_data = scaler.fit_transform(df)
# 将结果转换回DataFrame（如果需要保持列名和索引）
standardized_df = pd.DataFrame(standardized_data, columns=df.columns, index=df.index)
```

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_15.png)

使用`StandardScaler`的好处是，它能够记住拟合时的均值和标准差，方便后续用同样的参数对新数据进行转换，保证处理的一致性。

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_17.png)

## 标准化效果演示

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_19.png)

让我们通过一个简单的例子来观察标准化的效果。

假设我们有一组原始数据，其数值范围差异较大。

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_21.png)

```python
import pandas as pd

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_23.png)

# 示例原始数据
original_data = pd.Series([30, 25, 120, 80, 15])
print("原始数据：")
print(original_data)
print(f"均值: {original_data.mean():.2f}, 标准差: {original_data.std():.2f}")

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_25.png)

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_27.png)

# 应用我们的标准化函数
standardized_data = standardize_series(original_data)
print("\n标准化后的数据：")
print(standardized_data)
print(f"均值: {standardized_data.mean():.2f}, 标准差: {standardized_data.std():.2f}")
```

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_29.png)

运行上述代码，你将看到原始数据中较大的数值（如120）经过标准化后，被缩放到了与其他数据点相近的范围内。同时，标准化后数据的均值非常接近0，标准差非常接近1。

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_31.png)

## 总结

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_33.png)

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_35.png)

本节课中我们一起学习了数据标准化处理。
*   我们首先了解了标准化的**目的**：消除不同特征间量纲和取值范围差异的影响，使数据具有可比性。
*   然后，我们学习了标准化的核心**公式 `z = (x - μ) / σ`**，并理解其“去均值”和“除标准差”两步操作的意义。
*   接着，我们掌握了两种**实现方法**：手动使用Pandas计算以及调用Scikit-learn的`StandardScaler`工具。
*   最后，通过代码演示，我们直观看到了标准化如何将差异巨大的原始数据转换到统一的尺度上。

![](img/95eeeae5e2af1c2b7c06e7433e93d9c9_37.png)

标准化是数据预处理中极为基础和关键的一步，在金融数据分析、机器学习模型训练等场景中应用广泛。掌握它，能为后续更复杂的数据分析和建模工作打下坚实的基础。