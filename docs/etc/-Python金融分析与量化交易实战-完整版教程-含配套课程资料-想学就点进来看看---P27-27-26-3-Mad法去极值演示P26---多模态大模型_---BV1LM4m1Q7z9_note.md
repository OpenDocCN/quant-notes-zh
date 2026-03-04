# Python金融分析与量化交易实战：P27：27.26.3-MAD法去极值演示

![](img/5491a1680cd2ea33bcc6878df94c99a8_0.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_2.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_0.png)

在本节课中，我们将学习第二种数据去极值的方法——MAD法。我们将详细介绍其原理、公式，并通过代码演示其实现过程。

![](img/5491a1680cd2ea33bcc6878df94c99a8_4.png)

上一节我们介绍了百分位法去极值，本节中我们来看看另一种基于中位数绝对偏差的方法。

## MAD法原理

MAD法的核心思想是利用数据的中位数和“中位数绝对偏差”来设定合理的上下边界，从而识别并处理极端值。

![](img/5491a1680cd2ea33bcc6878df94c99a8_6.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_8.png)

以下是MAD法的计算步骤：

![](img/5491a1680cd2ea33bcc6878df94c99a8_10.png)

首先，计算原始数据序列的中位数。假设我们有一个数据序列 `[1, 2, 3, 4, 5, 6, 7, ...]`，其中位数为 `4`。

![](img/5491a1680cd2ea33bcc6878df94c99a8_12.png)

接着，计算每个数据点与中位数之差的绝对值，形成一个新的序列。例如，对于数据点 `1`，其绝对偏差为 `|1 - 4| = 3`。

![](img/5491a1680cd2ea33bcc6878df94c99a8_14.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_16.png)

然后，对这个由绝对偏差构成的新序列，再次计算其中位数。这个值被称为“中位数绝对偏差”，即MAD值。

![](img/5491a1680cd2ea33bcc6878df94c99a8_18.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_20.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_22.png)

最后，利用中位数和MAD值设定上下界。上界通常为 `中位数 + N * MAD`，下界为 `中位数 - N * MAD`。其中，`N` 是一个常数，通常取值为 `1.4826`。任何超出此范围的数据点将被视为极值并进行处理。

![](img/5491a1680cd2ea33bcc6878df94c99a8_24.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_26.png)

**核心公式**：
*   `MAD = median(|X_i - median(X)|)`
*   `上界 = median(X) + N * MAD`
*   `下界 = median(X) - N * MAD`

![](img/5491a1680cd2ea33bcc6878df94c99a8_28.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_30.png)

## 代码实现

![](img/5491a1680cd2ea33bcc6878df94c99a8_32.png)

理解了原理后，我们来看看如何用Python代码实现MAD去极值法。

![](img/5491a1680cd2ea33bcc6878df94c99a8_34.png)

以下是实现MAD去极值功能的函数代码：

![](img/5491a1680cd2ea33bcc6878df94c99a8_36.png)

```python
def filter_extreme_MAD(series, n):
    """
    使用MAD法对序列进行去极值处理。
    :param series: 待处理的数据序列
    :param n: MAD的倍数，通常为1.4826
    :return: 处理后的数据序列
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算中位数绝对偏差 (MAD)
    # 先计算每个数据点与中位数的绝对偏差
    abs_deviation = (series - median).abs()
    # 再计算这些绝对偏差的中位数，即MAD值
    mad = abs_deviation.quantile(0.5)
    
    # 3. 计算上下界
    high = median + n * mad
    low = median - n * mad
    
    # 4. 将超出范围的值替换为边界值
    filtered_series = series.clip(lower=low, upper=high)
    
    return filtered_series
```

![](img/5491a1680cd2ea33bcc6878df94c99a8_38.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_40.png)

现在，我们可以使用这个函数来处理数据。假设我们有一个名为 `data` 的Pandas Series数据序列。

![](img/5491a1680cd2ea33bcc6878df94c99a8_42.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_44.png)

```python
# 应用MAD法去极值，n取常用值1.4826
filtered_data_MAD = filter_extreme_MAD(data, n=1.4826)

![](img/5491a1680cd2ea33bcc6878df94c99a8_46.png)

# 可以绘制图表对比原始数据和处理后的数据（绘图非重点，仅作示意）
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 5))
plt.plot(data.index, data.values, label='原始数据', alpha=0.7)
plt.plot(filtered_data_MAD.index, filtered_data_MAD.values, label='MAD法处理后', alpha=0.9)
plt.legend()
plt.show()
```

![](img/5491a1680cd2ea33bcc6878df94c99a8_48.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_50.png)

执行上述代码后，`filtered_data_MAD` 就是经过MAD法处理后的数据。在图表中，超出上下界的极端值会被“拉回”到边界上（显示为蓝色线段的平坦部分），而大部分正常数据则保持不变。

![](img/5491a1680cd2ea33bcc6878df94c99a8_52.png)

![](img/5491a1680cd2ea33bcc6878df94c99a8_54.png)

本节课中我们一起学习了MAD去极值法。我们了解了其通过计算中位数和MAD值来动态确定数据正常范围的核心思想，掌握了其计算步骤和公式，并成功用Python代码实现了这一功能。MAD法对异常值不敏感，在数据存在偏态或异常值较多时，有时比基于均值和标准差的方法更稳健。