# 金融分析与量化交易：03-3：MAD法去极值演示 📊

![](img/88566cd87a962fc2ba054318216b1b42_0.png)

![](img/88566cd87a962fc2ba054318216b1b42_2.png)

在本节课中，我们将要学习数据预处理中的第二种去极值方法——MAD法。我们将详细解释其原理，并通过Python代码演示如何实现它。

上一节我们介绍了基于百分位的去极值方法，本节中我们来看看另一种基于中位数绝对偏差（MAD）的方法。

![](img/88566cd87a962fc2ba054318216b1b42_4.png)

## MAD法原理介绍

MAD法的核心思想是利用数据的中位数和绝对偏差来设定合理的上下界，从而识别并处理极端值。其计算过程分为两步。

以下是MAD值的计算步骤：
1.  首先，计算原始数据序列的中位数。
2.  然后，计算序列中每个数据点与该中位数的绝对偏差，得到一个新的偏差序列。
3.  最后，计算这个偏差序列的中位数，这个值就是MAD值。

![](img/88566cd87a962fc2ba054318216b1b42_6.png)

![](img/88566cd87a962fc2ba054318216b1b42_8.png)

得到MAD值后，我们就可以设定数据的正常范围。其上下界的计算公式如下：
*   **上界** = 中位数 + N × MAD
*   **下界** = 中位数 - N × MAD

![](img/88566cd87a962fc2ba054318216b1b42_10.png)

![](img/88566cd87a962fc2ba054318216b1b42_12.png)

其中，N是一个常数，通常取值为 **1.4826**。任何超出这个范围的数据点，都可以被视为极端值并进行处理（例如，缩放到边界值）。

![](img/88566cd87a962fc2ba054318216b1b42_14.png)

## Python代码实现

![](img/88566cd87a962fc2ba054318216b1b42_16.png)

![](img/88566cd87a962fc2ba054318216b1b42_18.png)

理解了原理之后，我们来看看如何用Python代码实现MAD去极值。我们将按照上述公式逐步计算。

![](img/88566cd87a962fc2ba054318216b1b42_20.png)

![](img/88566cd87a962fc2ba054318216b1b42_22.png)

以下是实现MAD去极值功能的函数代码：

![](img/88566cd87a962fc2ba054318216b1b42_24.png)

![](img/88566cd87a962fc2ba054318216b1b42_26.png)

```python
def filter_extreme_MAD(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series: 待处理的pandas Series数据序列。
        n: 用于计算边界的倍数，默认为1.4826。
    返回:
        处理后的序列，下界，上界。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算每个数据点与中位数的绝对偏差，并求此偏差序列的中位数，即MAD值
    mad = (series - median).abs().quantile(0.5)

    # 3. 利用MAD值计算上下界
    high = median + n * mad
    low = median - n * mad

    # 4. 将超出边界的数据缩放到边界值
    filtered_series = series.clip(lower=low, upper=high)

    return filtered_series, low, high
```

现在，我们可以使用这个函数来处理实际数据，并可视化处理前后的对比。

以下是调用函数并绘制对比图的示例代码：

![](img/88566cd87a962fc2ba054318216b1b42_28.png)

![](img/88566cd87a962fc2ba054318216b1b42_30.png)

```python
# 假设 `data` 是您的原始数据序列
filtered_data, low_bound, high_bound = filter_extreme_MAD(data)

![](img/88566cd87a962fc2ba054318216b1b42_32.png)

# 可视化对比
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
plt.plot(data.index, data, label=‘原始数据‘, alpha=0.7)
plt.plot(filtered_data.index, filtered_data, label=‘MAD去极值后‘, linewidth=2)
plt.axhline(y=high_bound, color=‘r‘, linestyle=‘--‘, label=‘上界‘)
plt.axhline(y=low_bound, color=‘g‘, linestyle=‘--‘, label=‘下界‘)
plt.legend()
plt.title(‘MAD法去极值效果对比‘)
plt.show()
```

![](img/88566cd87a962fc2ba054318216b1b42_34.png)

![](img/88566cd87a962fc2ba054318216b1b42_36.png)

执行代码后，您将看到原始数据曲线与经过MAD法处理后的数据曲线。图中红色的虚线代表计算出的上界，绿色的虚线代表下界。所有超出红绿边界的数据点都被调整到了边界值上。

![](img/88566cd87a962fc2ba054318216b1b42_38.png)

![](img/88566cd87a962fc2ba054318216b1b42_40.png)

本节课中我们一起学习了MAD去极值法。我们首先了解了其通过中位数和绝对偏差来定义数据正常范围的原理，然后一步步实现了对应的Python函数，并最终通过图表直观地看到了该方法处理极端值的效果。这是数据预处理中一个非常实用且稳健的工具。