# 金融数据分析：3：MAD法去极值演示 📊

![](img/5ce39690270b4f3c213de06b10803dda_0.png)

![](img/5ce39690270b4f3c213de06b10803dda_2.png)

在本节课中，我们将要学习第二种数据去极值的方法——MAD法。上一节我们介绍了百分位法，本节中我们来看看如何利用中位数绝对偏差来识别和处理异常值。

## 概述

![](img/5ce39690270b4f3c213de06b10803dda_4.png)

MAD法是一种基于数据中位数和绝对偏差的稳健去极值方法。它的核心思想是通过计算数据点与中位数偏差的中位数，来确定一个合理的范围，并将超出此范围的值视为极值进行处理。

## MAD法原理

MAD法的计算过程分为几个步骤。

![](img/5ce39690270b4f3c213de06b10803dda_6.png)

![](img/5ce39690270b4f3c213de06b10803dda_8.png)

首先，对于一个给定的数据序列，我们计算它的中位数。公式表示为：
`median = 序列.quantile(0.5)`

![](img/5ce39690270b4f3c213de06b10803dda_10.png)

接着，我们计算序列中每个数据点与该中位数的绝对偏差，从而形成一个新的序列。这个新序列的每个元素是：
`| 原始数据点 - 中位数 |`

![](img/5ce39690270b4f3c213de06b10803dda_12.png)

然后，我们对这个由绝对偏差构成的新序列再次求中位数，这个值就是中位数绝对偏差（MAD）。

![](img/5ce39690270b4f3c213de06b10803dda_14.png)

![](img/5ce39690270b4f3c213de06b10803dda_16.png)

最后，我们利用中位数和MAD值来设定数据的正常范围。通常，上界和下界通过以下公式确定：
`上界 = 中位数 + N * MAD`
`下界 = 中位数 - N * MAD`
其中，N是一个常数，通常取值为1.4826。任何超出[下界， 上界]范围的数据点将被视为极值并进行处理（例如缩尾或截尾）。

![](img/5ce39690270b4f3c213de06b10803dda_18.png)

![](img/5ce39690270b4f3c213de06b10803dda_20.png)

![](img/5ce39690270b4f3c213de06b10803dda_22.png)

## 代码实现

![](img/5ce39690270b4f3c213de06b10803dda_24.png)

以下是使用Python实现MAD去极值函数的步骤。

![](img/5ce39690270b4f3c213de06b10803dda_26.png)

![](img/5ce39690270b4f3c213de06b10803dda_28.png)

首先，我们定义一个名为 `filter_extreme_MAD` 的函数，它接收一个数据序列 `series` 和一个倍数参数 `n`。

```python
def filter_extreme_MAD(series, n):
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算每个数据点与中位数的绝对偏差，并求该偏差序列的中位数（即MAD值）
    mad = (series - median).abs().quantile(0.5)
    
    # 3. 利用中位数和MAD计算上界与下界
    max_limit = median + n * mad
    min_limit = median - n * mad
    
    # 4. 对超出范围的值进行缩尾处理
    return series.clip(lower=min_limit, upper=max_limit)
```

定义好函数后，我们可以将其应用于实际数据。

![](img/5ce39690270b4f3c213de06b10803dda_30.png)

![](img/5ce39690270b4f3c213de06b10803dda_32.png)

```python
# 应用MAD法去极值，n取常用值1.4826
filtered_data_MAD = filter_extreme_MAD(original_series, n=1.4826)

![](img/5ce39690270b4f3c213de06b10803dda_34.png)

![](img/5ce39690270b4f3c213de06b10803dda_36.png)

# 可视化对比原始数据与处理后的数据
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
plt.plot(original_series, label=‘原始数据’, alpha=0.7)
plt.plot(filtered_data_MAD, label=‘MAD法处理后数据’)
plt.legend()
plt.show()
```

![](img/5ce39690270b4f3c213de06b10803dda_38.png)

执行以上代码后，蓝色线条代表处理后的数据，超出正常范围（由MAD法确定）的极值点已被调整至边界处。

![](img/5ce39690270b4f3c213de06b10803dda_40.png)

![](img/5ce39690270b4f3c213de06b10803dda_42.png)

## 总结

![](img/5ce39690270b4f3c213de06b10803dda_44.png)

![](img/5ce39690270b4f3c213de06b10803dda_46.png)

本节课中我们一起学习了MAD去极值法。我们了解了其通过两次计算中位数来确定数据正常范围的原理，并完成了从公式推导到Python代码实现的完整过程。与百分位法相比，MAD法对极端值不敏感，因此更为稳健，是金融数据分析中常用的数据预处理手段之一。