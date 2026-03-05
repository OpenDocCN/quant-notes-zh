# Python金融分析与量化交易实战教程：3：MAD法去极值演示 📊

![](img/213c34196a05f3f2b551c5e16714a233_0.png)

![](img/213c34196a05f3f2b551c5e16714a233_2.png)

在本节课中，我们将要学习数据预处理中的第二种去极值方法——MAD法。我们将详细解释其原理，并通过代码演示如何实现它。

上一节我们介绍了百分位法去极值，本节中我们来看看另一种基于中位数绝对偏差的方法。

![](img/213c34196a05f3f2b551c5e16714a233_4.png)

## MAD法原理

MAD法，全称为中位数绝对偏差法。其核心思想是利用数据的中位数和绝对偏差来设定合理的上下界，从而识别并处理极端值。

以下是MAD法的计算步骤：

![](img/213c34196a05f3f2b551c5e16714a233_6.png)

![](img/213c34196a05f3f2b551c5e16714a233_8.png)

1.  首先，计算原始数据序列的中位数。
    *   公式：`median = median(series)`
2.  然后，计算每个数据点与中位数之差的绝对值，形成一个新的序列。
    *   公式：`abs_diff = |series - median|`
3.  接着，计算这个绝对值序列的中位数，这个值就是MAD。
    *   公式：`MAD = median(abs_diff)`
4.  最后，根据中位数和MAD值设定上下界。通常，上界为中位数加上N倍的MAD值，下界为中位数减去N倍的MAD值。N通常取一个固定值1.4826。
    *   公式：
        *   `upper_bound = median + N * MAD`
        *   `lower_bound = median - N * MAD`
5.  对于原始数据，任何大于上界或小于下界的值，都将被视为极值并进行处理（例如，替换为边界值）。

![](img/213c34196a05f3f2b551c5e16714a233_10.png)

![](img/213c34196a05f3f2b551c5e16714a233_12.png)

## 代码实现

![](img/213c34196a05f3f2b551c5e16714a233_14.png)

理解了原理后，我们来看看如何用Python代码实现MAD法去极值。

![](img/213c34196a05f3f2b551c5e16714a233_16.png)

![](img/213c34196a05f3f2b551c5e16714a233_18.png)

以下是实现MAD去极值功能的函数代码：

![](img/213c34196a05f3f2b551c5e16714a233_20.png)

![](img/213c34196a05f3f2b551c5e16714a233_22.png)

```python
def filter_MAD(series, N=1.4826):
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算每个数据点与中位数之差的绝对值
    abs_diff = (series - median).abs()

    # 3. 计算绝对值序列的中位数，即MAD值
    MAD = abs_diff.quantile(0.5)

    # 4. 计算上下界
    upper_bound = median + N * MAD
    lower_bound = median - N * MAD

    # 5. 对超出边界的数据进行截断处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)

    return filtered_series
```

![](img/213c34196a05f3f2b551c5e16714a233_24.png)

![](img/213c34196a05f3f2b551c5e16714a233_26.png)

## 函数应用与结果可视化

现在，我们可以使用这个函数来处理实际数据，并观察处理效果。

```python
# 假设 `data` 是包含待处理数据的Pandas Series
filtered_data_MAD = filter_MAD(data)

![](img/213c34196a05f3f2b551c5e16714a233_28.png)

![](img/213c34196a05f3f2b551c5e16714a233_30.png)

# 可视化对比原始数据与处理后的数据
import matplotlib.pyplot as plt

![](img/213c34196a05f3f2b551c5e16714a233_32.png)

plt.figure(figsize=(10, 6))
plt.plot(data, label='原始数据', alpha=0.7)
plt.plot(filtered_data_MAD, label='MAD法处理后数据')
plt.legend()
plt.title('MAD法去极值效果对比')
plt.show()
```

![](img/213c34196a05f3f2b551c5e16714a233_34.png)

![](img/213c34196a05f3f2b551c5e16714a233_36.png)

执行代码后，生成的图表将清晰展示MAD法如何将原始数据中的极端值（通常表现为远离主体数据的尖峰或低谷）调整到合理的边界范围内。

![](img/213c34196a05f3f2b551c5e16714a233_38.png)

![](img/213c34196a05f3f2b551c5e16714a233_40.png)

本节课中我们一起学习了MAD去极值法。我们了解了其基于中位数和绝对偏差的计算逻辑，掌握了用Python实现该方法的完整步骤，并通过可视化对比验证了其处理效果。MAD法对异常值不敏感，比基于标准差的方法更稳健，是金融数据分析中常用的数据清洗工具之一。