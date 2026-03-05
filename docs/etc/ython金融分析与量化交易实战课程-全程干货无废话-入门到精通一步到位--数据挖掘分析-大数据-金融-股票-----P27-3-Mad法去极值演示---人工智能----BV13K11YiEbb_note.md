# Python金融分析与量化交易实战课程：P27：3-MAD法去极值演示 📊

![](img/5c50b2808dcf3828baee4beaf657771a_0.png)

![](img/5c50b2808dcf3828baee4beaf657771a_2.png)

在本节课中，我们将要学习数据预处理中的第二种去极值方法——MAD法。我们将详细解释其原理，并通过代码演示如何实现。

上一节我们介绍了基于百分位的去极值方法，本节中我们来看看另一种基于中位数绝对偏差（MAD）的方法。

![](img/5c50b2808dcf3828baee4beaf657771a_4.png)

## MAD法原理介绍

MAD法的核心思想是利用数据的中位数和绝对偏差来设定合理的上下边界，从而识别并处理极端值。其计算过程分为两步。

![](img/5c50b2808dcf3828baee4beaf657771a_6.png)

以下是MAD值的计算步骤：
1.  首先，计算原始数据序列的中位数。
2.  然后，计算序列中每个数据点与该中位数的绝对偏差，形成一个新的偏差序列。
3.  最后，计算这个偏差序列的中位数，这个值就是MAD值。

![](img/5c50b2808dcf3828baee4beaf657771a_8.png)

![](img/5c50b2808dcf3828baee4beaf657771a_10.png)

得到MAD值后，我们可以设定数据的正常范围。其上下界的计算公式为：
**上界 = 中位数 + N × MAD**
**下界 = 中位数 - N × MAD**

![](img/5c50b2808dcf3828baee4beaf657771a_12.png)

其中，N是一个常数因子，通常取值为 **1.4826**。任何超出此上下界的数据点，都可以被视为极端值并进行处理（如截断或替换）。

![](img/5c50b2808dcf3828baee4beaf657771a_14.png)

![](img/5c50b2808dcf3828baee4beaf657771a_16.png)

## 代码实现演示

![](img/5c50b2808dcf3828baee4beaf657771a_18.png)

![](img/5c50b2808dcf3828baee4beaf657771a_20.png)

![](img/5c50b2808dcf3828baee4beaf657771a_22.png)

理解了原理之后，我们来看看如何用Python代码实现MAD去极值。我们将按照上述步骤，先计算MAD值，再确定上下边界。

![](img/5c50b2808dcf3828baee4beaf657771a_24.png)

![](img/5c50b2808dcf3828baee4beaf657771a_26.png)

以下是实现MAD去极值功能的函数代码：

```python
def filter_extreme_MAD(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。
    :param series: 待处理的数据序列（pandas Series）
    :param n: 倍数因子，默认为1.4826
    :return: 处理后的数据序列
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算每个数据点与中位数的绝对偏差，并求该偏差序列的中位数（即MAD值）
    mad = (series - median).abs().quantile(0.5)

    # 3. 利用MAD值计算上下界
    high = median + n * mad
    low = median - n * mad

    # 4. 将超出边界的数据截断至边界
    return series.clip(lower=low, upper=high)
```

现在，我们可以调用这个函数来处理实际数据，并可视化处理前后的对比。

![](img/5c50b2808dcf3828baee4beaf657771a_28.png)

![](img/5c50b2808dcf3828baee4beaf657771a_30.png)

```python
# 假设 `data_series` 是您的原始数据序列
filtered_series = filter_extreme_MAD(data_series)

![](img/5c50b2808dcf3828baee4beaf657771a_32.png)

# 可视化对比
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
plt.plot(data_series, label='原始数据', alpha=0.7)
plt.plot(filtered_series, label='MAD法处理后', alpha=0.9)
plt.legend()
plt.title('MAD法去极值效果对比')
plt.show()
```

![](img/5c50b2808dcf3828baee4beaf657771a_34.png)

![](img/5c50b2808dcf3828baee4beaf657771a_36.png)

执行代码后，您将看到处理后的数据（例如图中的蓝色线）中，原先的极端值已被拉回至由MAD法确定的合理边界之内。

![](img/5c50b2808dcf3828baee4beaf657771a_38.png)

![](img/5c50b2808dcf3828baee4beaf657771a_40.png)

本节课中我们一起学习了MAD去极值法。我们了解了其通过中位数和绝对偏差中位数来定义数据正常范围的原理，并掌握了用Python实现该方法的完整步骤。这是数据预处理中一种稳健的异常值处理技术。