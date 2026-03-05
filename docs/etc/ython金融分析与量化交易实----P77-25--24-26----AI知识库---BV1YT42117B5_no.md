# 量化交易完全可自学教程：P77：25. 24.26.3-MAD法去极值演示

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_0.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_2.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_0.png)

在本节中，我们将学习第二种数据去极值方法——MAD法。上一节我们介绍了百分位法，本节中我们来看看MAD法如何工作。MAD法在公式上看似稍复杂，但计算过程同样简单。

## MAD法原理

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_4.png)

首先，MAD法要求我们计算一个名为MAD的值。以下是计算步骤：

1.  给定一个数据序列，例如 `[1, 2, 3, 4, ...]`。
2.  计算该序列的中位数。假设中位数是 `4`。
3.  对于序列中的每一个数据点，计算其与中位数的**绝对差值**。公式为：`| 数据点 - 中位数 |`。
    *   例如，对于数据点 `1`，其绝对差值为 `|1 - 4| = 3`。
    *   对于数据点 `2`，其绝对差值为 `|2 - 4| = 2`。
4.  由这些绝对差值构成一个新的序列。
5.  计算这个新序列的**中位数**，这个值就是MAD值。

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_4.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_6.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_8.png)

获得MAD值后，我们就可以设定数据的合理范围。具体方法是：

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_10.png)

*   设定一个上界：`上界 = 中位数 + N * MAD`
*   设定一个下界：`下界 = 中位数 - N * MAD`

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_12.png)

其中，`N` 是一个常数，通常取值为 **1.4826**。这个取值与数据服从正态分布有关，我们在此直接使用即可。

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_14.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_16.png)

本质上，MAD法和百分位法的目标一致：**确定一个合理的上下界，将超出此范围的数据视为异常值并进行处理**。区别仅在于确定边界的方法不同。

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_18.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_20.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_6.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_22.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_24.png)

## 代码实现

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_26.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_28.png)

理解了原理后，我们来看看如何用代码实现MAD去极值法。代码逻辑完全遵循上述公式：先计算MAD值，再计算上下界。

以下是实现该功能的函数：

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_30.png)

```python
def filter_MAD(series, n):
    """
    使用MAD法对序列进行去极值处理。
    :param series: 待处理的数据序列
    :param n: 倍数，通常取1.4826
    :return: 处理后的序列
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算每个数据点与中位数的绝对差值，并求其中位数，得到MAD值
    mad = (series - median).abs().quantile(0.5)

    # 3. 计算上下界
    high = median + n * mad
    low = median - n * mad

    # 4. 将超出上下界的数据替换为边界值
    return series.clip(lower=low, upper=high)
```

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_32.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_34.png)

现在，我们可以使用这个函数来处理数据。假设我们有一个名为 `data` 的数据序列：

```python
# 使用通常的N值（1.4826）处理数据
result_MAD = filter_MAD(data, n=1.4826)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_36.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_38.png)

# 可以绘制图像对比处理前后的数据
import matplotlib.pyplot as plt
plt.plot(data, label='原始数据')
plt.plot(result_MAD, label='MAD法处理后')
plt.legend()
plt.show()
```

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_40.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_48.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_42.png)

执行代码后，蓝色线条代表原始数据，橙色线条代表经过MAD法处理后的数据。可以看到，超出计算边界的极端值已被修正到边界位置。

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_44.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_46.png)

## 总结

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_48.png)

![](img/ac752bc78ea12fe7d5a4dcd2eb7a113e_50.png)

本节课中，我们一起学习了第二种去极值方法——**MAD法**。我们首先了解了其计算原理：通过数据中位数和绝对偏差的中位数来定义数据的合理范围。随后，我们一步步实现了对应的Python函数，并验证了其效果。MAD法与之前学习的百分位法都是量化数据预处理中常用的工具，核心目的都是消除异常值对分析的干扰。