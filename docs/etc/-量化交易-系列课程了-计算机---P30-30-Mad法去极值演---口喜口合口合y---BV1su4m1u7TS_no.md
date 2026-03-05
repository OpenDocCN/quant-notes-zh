# Python金融分析+量化交易：P30：MAD法去极值演示 📊

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_0.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_2.png)

在本节课中，我们将要学习数据预处理中的第二种去极值方法——MAD法。我们将详细讲解其原理、计算步骤，并通过代码演示如何实现它。

上一节我们介绍了百分位法去极值，本节中我们来看看另一种基于中位数绝对偏差的方法。

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_4.png)

## MAD法原理

MAD法的核心思想是利用数据的中位数和“中位数绝对偏差”来设定一个合理的范围，从而识别并处理极端值。其计算过程分为两步。

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_6.png)

以下是MAD值的计算步骤：
1.  计算原始数据序列的中位数。
2.  计算序列中每个数据点与中位数之差的绝对值，得到一个新的序列。
3.  对这个新的绝对值序列再次求中位数，这个值就是MAD值。

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_8.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_10.png)

得到MAD值后，我们可以设定数据的正常范围。通常，上界和下界由以下公式决定：
**上界 = 中位数 + N × MAD**
**下界 = 中位数 - N × MAD**

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_12.png)

其中，N是一个常数，通常取值为 **1.4826**。任何超出此范围的数据点都可能被视为极端值并进行处理。

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_14.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_16.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_18.png)

## 代码实现

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_20.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_22.png)

理解了原理后，我们来看看如何用Python代码实现MAD去极值。我们将按照上述步骤编写一个函数。

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_24.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_26.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_28.png)

以下是`mad_filter`函数的实现代码：

```python
def mad_filter(series, n):
    """
    使用MAD法对序列进行去极值处理。
    :param series: 待处理的数据序列
    :param n: 用于计算范围的倍数，通常为1.4826
    :return: 处理后的数据序列
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算每个数据点与中位数差的绝对值，再求该绝对值序列的中位数，即MAD值
    mad = (series - median).abs().quantile(0.5)

    # 3. 计算上界和下界
    high = median + n * mad
    low = median - n * mad

    # 4. 将超出范围的值替换为边界值
    return series.clip(lower=low, upper=high)
```

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_30.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_32.png)

现在，我们可以使用这个函数来处理数据。假设我们有一个名为`data`的Pandas Series数据序列。

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_34.png)

以下是调用函数并可视化结果的示例：

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_36.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_38.png)

```python
# 应用MAD过滤器，n取常用值1.4826
filtered_data = mad_filter(data, n=1.4826)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_40.png)

# 可视化原始数据与处理后的数据（可选）
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
plt.plot(data, label='原始数据', alpha=0.7)
plt.plot(filtered_data, label='MAD处理后的数据')
plt.legend()
plt.show()
```

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_42.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_44.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_46.png)

执行代码后，蓝色线条代表原始数据，橙色线条代表经过MAD法处理后的数据。可以看到，超出计算边界的极端值已被拉回至边界附近。

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_48.png)

![](img/4d3e7df78abf1cad1d8a22ca2ed7ebd0_50.png)

本节课中我们一起学习了MAD去极值法。我们首先了解了其通过两次计算中位数来定义数据正常范围的原理，然后逐步实现了对应的Python函数，并进行了结果验证。这是数据预处理中另一种有效且稳健的离群值处理方法。