# Python金融量化分析：P30：MAD法去极值演示 📊

![](img/70fad18b34ef36e37d752b25ab34b784_0.png)

![](img/70fad18b34ef36e37d752b25ab34b784_2.png)

在本节课中，我们将要学习第二种数据去极值的方法——MAD法。我们将详细解释其原理，并通过代码演示如何实现它。

上一节我们介绍了百分位法去极值，本节中我们来看看另一种基于中位数绝对偏差的方法。

![](img/70fad18b34ef36e37d752b25ab34b784_4.png)

## MAD法原理

MAD法的核心思想是利用数据的中位数和“中位数绝对偏差”来设定一个合理的范围，并剔除超出此范围的极端值。其计算过程分为两步。

以下是计算MAD值的具体步骤：
1.  计算原始数据序列的中位数。
2.  计算序列中每个数据点与中位数之差的绝对值，形成一个新的序列。
3.  对这个新的绝对值序列再次求中位数，这个值就是MAD值。

![](img/70fad18b34ef36e37d752b25ab34b784_6.png)

得到MAD值后，我们可以设定数据的上下边界。公式如下：

![](img/70fad18b34ef36e37d752b25ab34b784_8.png)

![](img/70fad18b34ef36e37d752b25ab34b784_10.png)

**上界 = 中位数 + N × MAD值**
**下界 = 中位数 - N × MAD值**

![](img/70fad18b34ef36e37d752b25ab34b784_12.png)

其中，N是一个常数，通常取值为 **1.4826**。任何超出[下界， 上界]范围的数据点，都将被视为异常值并进行处理（例如，将其调整到边界值）。

![](img/70fad18b34ef36e37d752b25ab34b784_14.png)

## 代码实现

![](img/70fad18b34ef36e37d752b25ab34b784_16.png)

![](img/70fad18b34ef36e37d752b25ab34b784_18.png)

![](img/70fad18b34ef36e37d752b25ab34b784_20.png)

理解了原理后，我们来看看如何用Python代码实现MAD去极值法。我们将按照上述步骤编写一个函数。

![](img/70fad18b34ef36e37d752b25ab34b784_22.png)

![](img/70fad18b34ef36e37d752b25ab34b784_24.png)

以下是`mad_filter`函数的完整代码：

![](img/70fad18b34ef36e37d752b25ab34b784_26.png)

![](img/70fad18b34ef36e37d752b25ab34b784_28.png)

```python
import pandas as pd
import numpy as np

def mad_filter(series, n):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series: pandas Series，待处理的数据序列。
        n: float，用于计算边界的倍数，通常为1.4826。
    返回:
        Series: 处理后的数据序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算每个数据点与中位数差的绝对值，并求该绝对值序列的中位数，即MAD值
    mad = (series - median).abs().quantile(0.5)

    # 3. 计算上下边界
    high = median + n * mad
    low = median - n * mad

    # 4. 将超出边界的数据替换为边界值
    filtered_series = series.copy()
    filtered_series[filtered_series > high] = high
    filtered_series[filtered_series < low] = low

    return filtered_series
```

![](img/70fad18b34ef36e37d752b25ab34b784_30.png)

![](img/70fad18b34ef36e37d752b25ab34b784_32.png)

## 函数应用与结果可视化

![](img/70fad18b34ef36e37d752b25ab34b784_34.png)

现在，我们可以使用这个函数来处理实际数据，并观察处理效果。

![](img/70fad18b34ef36e37d752b25ab34b784_36.png)

以下是应用函数并绘制对比图的示例代码：

![](img/70fad18b34ef36e37d752b25ab34b784_38.png)

![](img/70fad18b34ef36e37d752b25ab34b784_40.png)

```python
# 假设df[‘return’]是我们的原始收益率数据序列
# 应用MAD去极值函数，n取1.4826
filtered_data = mad_filter(df[‘return’], n=1.4826)

![](img/70fad18b34ef36e37d752b25ab34b784_42.png)

![](img/70fad18b34ef36e37d752b25ab34b784_44.png)

# 绘制原始数据与处理后数据的对比图
import matplotlib.pyplot as plt

![](img/70fad18b34ef36e37d752b25ab34b784_46.png)

plt.figure(figsize=(10, 6))
plt.plot(df[‘return’].values, label=‘原始数据’, alpha=0.7)
plt.plot(filtered_data.values, label=‘MAD去极值后’, alpha=0.9)
plt.axhline(y=filtered_data.max(), color=‘r’, linestyle=‘--’, label=‘上界’)
plt.axhline(y=filtered_data.min(), color=‘g’, linestyle=‘--’, label=‘下界’)
plt.legend()
plt.title(‘MAD法去极值效果对比’)
plt.show()
```

![](img/70fad18b34ef36e37d752b25ab34b784_48.png)

![](img/70fad18b34ef36e37d752b25ab34b784_50.png)

执行代码后，蓝色曲线代表原始数据，橙色曲线代表经过MAD法处理后的数据。红色和绿色的虚线分别标出了处理时使用的上界和下界。可以看到，原先的极端值（异常高或异常低的点）被拉回到了边界位置。

![](img/70fad18b34ef36e37d752b25ab34b784_52.png)

![](img/70fad18b34ef36e37d752b25ab34b784_54.png)

本节课中我们一起学习了MAD去极值法。我们了解了其通过两次计算中位数来确定数据正常范围的核心逻辑，掌握了其标准公式，并成功编写代码实现了该算法。与百分位法相比，MAD法对极端值不那么敏感，受异常值影响更小，是金融数据预处理中一个稳健的工具。