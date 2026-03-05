# 金融量化分析：P30：MAD法去极值演示

![](img/300b634af774614bfc3d876ef3d35212_0.png)

![](img/300b634af774614bfc3d876ef3d35212_2.png)

在本节课中，我们将学习数据去极值的第二种方法——MAD法。我们将详细解释其核心概念、计算步骤，并通过Python代码演示如何实现该方法。

## 概述

上一节我们介绍了基于百分位法的去极值方法。本节中，我们来看看另一种稳健的去极值方法：MAD法。MAD是“Median Absolute Deviation”的缩写，即中位数绝对偏差。该方法通过计算数据与其中位数偏差的中位数来定义数据的正常范围，从而识别并处理极端值。

![](img/300b634af774614bfc3d876ef3d35212_4.png)

## MAD法核心原理

MAD法的核心思想是计算一个基于数据分布稳健性的“尺度”来界定正常值的范围。

![](img/300b634af774614bfc3d876ef3d35212_6.png)

![](img/300b634af774614bfc3d876ef3d35212_7.png)

其计算过程分为两步：
1.  计算原始数据的中位数。
2.  计算每个数据点与中位数之差的绝对值，然后求这些绝对值的中位数，这个值就是MAD。

![](img/300b634af774614bfc3d876ef3d35212_8.png)

得到MAD值后，我们可以设定正常值的上下界。通常，上界为 **中位数 + N * MAD**，下界为 **中位数 - N * MAD**。其中，N是一个常数，通常取值为 **1.4826**。任何超出此范围的数据点都将被视为极值并进行处理（例如缩放到边界值）。

![](img/300b634af774614bfc3d876ef3d35212_10.png)

![](img/300b634af774614bfc3d876ef3d35212_12.png)

## 代码实现步骤

![](img/300b634af774614bfc3d876ef3d35212_14.png)

以下是使用Python实现MAD法去极值的具体步骤。我们将按照上述原理，逐步编写一个函数来完成计算。

![](img/300b634af774614bfc3d876ef3d35212_15.png)

首先，我们需要导入必要的库并准备一个示例数据序列。

![](img/300b634af774614bfc3d876ef3d35212_17.png)

![](img/300b634af774614bfc3d876ef3d35212_18.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

![](img/300b634af774614bfc3d876ef3d35212_19.png)

![](img/300b634af774614bfc3d876ef3d35212_20.png)

![](img/300b634af774614bfc3d876ef3d35212_21.png)

# 假设我们有一个数据序列
data = pd.Series([1, 2, 3, 4, 5, 6, 7, 100])  # 包含一个可能的极值100
```

![](img/300b634af774614bfc3d876ef3d35212_23.png)

接下来，我们定义MAD去极值函数。该函数接收一个数据序列和一个倍数N作为参数。

![](img/300b634af774614bfc3d876ef3d35212_24.png)

![](img/300b634af774614bfc3d876ef3d35212_25.png)

```python
def mad_filter(data_series, n=1.4826):
    """
    使用MAD法对数据进行去极值处理。
    参数:
        data_series: pandas Series, 输入数据序列。
        n: float, 用于计算边界的倍数，默认为1.4826。
    返回:
        filtered_series: pandas Series, 处理后的数据序列。
        lower_bound: float, 计算出的下界。
        upper_bound: float, 计算出的上界。
    """
    # 第一步：计算原始数据的中位数
    median = data_series.quantile(0.5)

    # 第二步：计算每个数据点与中位数之差的绝对值，然后求这些绝对值的中位数（即MAD）
    mad = (data_series - median).abs().quantile(0.5)

    # 第三步：利用MAD计算正常值的上下界
    upper_bound = median + n * mad
    lower_bound = median - n * mad

    # 第四步：将超出边界的数据缩放到边界值
    filtered_series = data_series.clip(lower=lower_bound, upper=upper_bound)

    return filtered_series, lower_bound, upper_bound
```

![](img/300b634af774614bfc3d876ef3d35212_27.png)

![](img/300b634af774614bfc3d876ef3d35212_28.png)

![](img/300b634af774614bfc3d876ef3d35212_29.png)

现在，我们可以调用这个函数来处理我们的示例数据，并可视化处理前后的对比。

![](img/300b634af774614bfc3d876ef3d35212_31.png)

```python
# 应用MAD去极值函数
filtered_data, low, high = mad_filter(data)

![](img/300b634af774614bfc3d876ef3d35212_32.png)

print(f"原始数据: {list(data)}")
print(f"处理后数据: {list(filtered_data)}")
print(f"下界: {low:.2f}, 上界: {high:.2f}")

![](img/300b634af774614bfc3d876ef3d35212_34.png)

# 绘制图像进行对比
plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
data.plot(title='原始数据')
plt.axhline(y=high, color='r', linestyle='--', label=f'上界 ({high:.2f})')
plt.axhline(y=low, color='g', linestyle='--', label=f'下界 ({low:.2f})')
plt.legend()

![](img/300b634af774614bfc3d876ef3d35212_35.png)

![](img/300b634af774614bfc3d876ef3d35212_36.png)

![](img/300b634af774614bfc3d876ef3d35212_37.png)

![](img/300b634af774614bfc3d876ef3d35212_38.png)

plt.subplot(1, 2, 2)
filtered_data.plot(title='MAD法去极值后数据', color='orange')
plt.tight_layout()
plt.show()
```

![](img/300b634af774614bfc3d876ef3d35212_40.png)

执行以上代码后，我们可以看到，原始数据中的极值（100）被拉回到了由上界定义的边界附近。图像中蓝色部分代表原始数据，橙色部分代表处理后的数据，红色和绿色虚线分别标识了计算出的上界和下界。

![](img/300b634af774614bfc3d876ef3d35212_41.png)

## 总结

![](img/300b634af774614bfc3d876ef3d35212_43.png)

![](img/300b634af774614bfc3d876ef3d35212_44.png)

![](img/300b634af774614bfc3d876ef3d35212_45.png)

本节课中我们一起学习了MAD去极值法。我们首先了解了MAD法的核心原理，即通过数据与中位数偏差的中位数来定义数据的离散程度，进而设定稳健的边界来识别极值。接着，我们一步步实现了该方法的Python代码，包括计算中位数、MAD值、上下界以及对数据进行裁剪。与百分位法相比，MAD法对极端值本身不敏感，因此在实际数据分布未知或存在严重偏斜时更为稳健。大家可以将此方法应用到自己的金融量化分析项目中，以清洗数据中的异常值。