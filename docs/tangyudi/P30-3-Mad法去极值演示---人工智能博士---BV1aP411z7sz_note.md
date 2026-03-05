# 数据处理：3：MAD法去极值演示 🧮

![](img/3c9e6a01e1e4d89be837950dc9db394f_0.png)

![](img/3c9e6a01e1e4d89be837950dc9db394f_2.png)

在本节课中，我们将要学习第二种数据去极值方法——MAD法。这种方法通过计算数据的中位数绝对偏差来设定合理的上下界，从而识别并处理异常值。

上一节我们介绍了百分位法去极值，本节中我们来看看另一种基于中位数的方法。

![](img/3c9e6a01e1e4d89be837950dc9db394f_4.png)

## MAD法原理 📊

MAD法的核心思想是使用数据的中位数和“中位数绝对偏差”来确定数据的正常范围。其计算过程分为两步。

首先，计算原始数据序列的中位数。接着，计算序列中每个数据点与该中位数差值的绝对值，从而得到一个新的序列。最后，计算这个新序列的中位数，这个值就是“中位数绝对偏差”（MAD）。

![](img/3c9e6a01e1e4d89be837950dc9db394f_6.png)

![](img/3c9e6a01e1e4d89be837950dc9db394f_8.png)

以下是MAD值的计算公式：
`MAD = median( |Xi - median(X)| )`
其中，`Xi`代表序列中的每一个数据点，`median(X)`代表原始序列的中位数。

![](img/3c9e6a01e1e4d89be837950dc9db394f_10.png)

有了MAD值后，我们就可以设定数据的正常范围边界。通常，上界和下界由以下公式确定：
`上界 = median(X) + N * MAD`
`下界 = median(X) - N * MAD`
公式中的`N`是一个常数，通常取值为1.486。任何落在这个范围之外的数据点，都可以被视为极值并进行处理。

![](img/3c9e6a01e1e4d89be837950dc9db394f_12.png)

## 代码实现 💻

![](img/3c9e6a01e1e4d89be837950dc9db394f_14.png)

理解了原理后，我们来看看如何用代码实现MAD法去极值。我们将按照上述步骤，逐步计算中位数、MAD值以及最终的上下界。

![](img/3c9e6a01e1e4d89be837950dc9db394f_16.png)

![](img/3c9e6a01e1e4d89be837950dc9db394f_18.png)

以下是实现MAD去极值功能的Python函数：

![](img/3c9e6a01e1e4d89be837950dc9db394f_20.png)

![](img/3c9e6a01e1e4d89be837950dc9db394f_22.png)

```python
import pandas as pd
import numpy as np

![](img/3c9e6a01e1e4d89be837950dc9db394f_24.png)

def filter_extreme_MAD(series, n):
    """
    使用MAD法过滤极值。
    :param series: 输入的数据序列（pandas Series）
    :param n: 用于计算边界的倍数，通常为1.486
    :return: 处理后的数据序列
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算每个数据点与中位数差值的绝对值，然后求该绝对值序列的中位数（即MAD）
    mad = (series - median).abs().quantile(0.5)
    
    # 3. 计算上界和下界
    upper_bound = median + n * mad
    lower_bound = median - n * mad
    
    # 4. 将超出边界的数据替换为边界值
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)
    
    return filtered_series
```

现在，我们可以使用这个函数来处理数据。假设我们有一个名为`data`的数据序列，并取`N=1.486`。

```python
# 假设 data 是一个 pandas Series
filtered_data = filter_extreme_MAD(data, n=1.486)

![](img/3c9e6a01e1e4d89be837950dc9db394f_26.png)

# 可视化对比原始数据与处理后的数据
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
plt.plot(data.index, data.values, 'o', label='原始数据', alpha=0.6)
plt.plot(filtered_data.index, filtered_data.values, 'x', label='MAD处理后的数据', alpha=0.8)
plt.axhline(y=upper_bound, color='r', linestyle='--', label='上界')
plt.axhline(y=lower_bound, color='g', linestyle='--', label='下界')
plt.legend()
plt.title('MAD法去极值效果对比')
plt.show()
```

![](img/3c9e6a01e1e4d89be837950dc9db394f_28.png)

![](img/3c9e6a01e1e4d89be837950dc9db394f_30.png)

执行代码后，蓝色点代表原始数据，处理后的数据以及红色的上界和绿色的下界会清晰地展示在图中。超出边界的数据点已被修正到边界值。

## 总结 ✨

![](img/3c9e6a01e1e4d89be837950dc9db394f_32.png)

![](img/3c9e6a01e1e4d89be837950dc9db394f_34.png)

本节课中我们一起学习了MAD法去极值。我们首先了解了该方法的原理，即通过中位数和MAD值来定义数据的合理范围。然后，我们一步步实现了对应的代码，并进行了可视化演示。

![](img/3c9e6a01e1e4d89be837950dc9db394f_36.png)

![](img/3c9e6a01e1e4d89be837950dc9db394f_38.png)

与百分位法相比，MAD法对极端值不敏感，因为它基于中位数而非均值，这使得它在处理包含异常值的数据时更加稳健。掌握这种方法，能为数据清洗和预处理提供又一个有效的工具。