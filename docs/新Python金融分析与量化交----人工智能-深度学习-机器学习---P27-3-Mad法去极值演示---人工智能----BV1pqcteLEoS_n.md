# Python金融分析与量化交易实战教程：P27：3-MAD法去极值演示

![](img/a03313fb35dc0636bbf54b15ccec483c_0.png)

![](img/a03313fb35dc0636bbf54b15ccec483c_2.png)

## 概述
在本节课中，我们将要学习数据预处理中的第二种去极值方法——MAD法。我们将详细解释其原理，并通过代码演示如何实现它。

## MAD法原理介绍
上一节我们介绍了百分位法去极值，本节中我们来看看另一种稳健的方法——MAD法。MAD是“Median Absolute Deviation”的缩写，即中位数绝对偏差。它的核心思想是利用数据的中位数和绝对偏差来设定合理的上下界，从而识别并处理极端值。

![](img/a03313fb35dc0636bbf54b15ccec483c_4.png)

MAD法的计算步骤如下：

1.  首先，计算原始数据序列的中位数（Median）。
2.  然后，计算序列中每个数据点与中位数之差的绝对值，形成一个新的序列。
3.  接着，计算这个新序列（即绝对偏差序列）的中位数，这个值就是MAD。
4.  最后，根据公式设定上下界：
    *   上界 = 中位数 + N * MAD
    *   下界 = 中位数 - N * MAD

其中，N是一个常数，通常取值为 **1.4826**。任何超出 [下界, 上界] 范围的数据点，都将被视为极值并进行处理（例如，用边界值替换）。

![](img/a03313fb35dc0636bbf54b15ccec483c_6.png)

![](img/a03313fb35dc0636bbf54b15ccec483c_8.png)

以下是MAD法的核心公式描述：
```
设原始序列为 X = [x1, x2, ..., xn]
中位数 Med = median(X)
绝对偏差序列 D = [|x1 - Med|, |x2 - Med|, ..., |xn - Med|]
MAD值 = median(D)
上界 Upper = Med + N * MAD
下界 Lower = Med - N * MAD
```

![](img/a03313fb35dc0636bbf54b15ccec483c_10.png)

## 代码实现
理解了原理之后，我们来看看如何用Python代码实现MAD去极值法。我们将编写一个函数，其逻辑与上述步骤完全一致。

![](img/a03313fb35dc0636bbf54b15ccec483c_12.png)

以下是`mad_filter`函数的实现代码：

![](img/a03313fb35dc0636bbf54b15ccec483c_14.png)

![](img/a03313fb35dc0636bbf54b15ccec483c_16.png)

```python
import pandas as pd
import numpy as np

![](img/a03313fb35dc0636bbf54b15ccec483c_18.png)

![](img/a03313fb35dc0636bbf54b15ccec483c_20.png)

def mad_filter(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series: pandas Series，待处理的数据序列。
        n: float，用于计算边界的倍数，默认值为1.4826。
    返回:
        series_capped: 处理后的序列。
        lower_bound: 计算出的下界。
        upper_bound: 计算出的上界。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算每个数据点与中位数之差的绝对值
    abs_deviation = (series - median).abs()

    # 3. 计算绝对偏差序列的中位数，即MAD值
    mad = abs_deviation.quantile(0.5)

    # 4. 计算上下界
    upper_bound = median + n * mad
    lower_bound = median - n * mad

    # 5. 对超出边界的数据进行截断处理
    series_capped = series.clip(lower=lower_bound, upper=upper_bound)

    return series_capped, lower_bound, upper_bound
```

![](img/a03313fb35dc0636bbf54b15ccec483c_22.png)

![](img/a03313fb35dc0636bbf54b15ccec483c_24.png)

## 函数应用与结果可视化
现在，让我们使用这个函数来处理一组模拟数据，并观察处理前后的效果。

![](img/a03313fb35dc0636bbf54b15ccec483c_26.png)

```python
# 生成包含一些极端值的示例数据
np.random.seed(42)
data = np.random.randn(100)  # 100个正态分布数据点
data[10] = 10  # 加入一个极大值
data[20] = -8  # 加入一个极小值
series = pd.Series(data)

# 应用MAD过滤器
filtered_series, lower, upper = mad_filter(series)

![](img/a03313fb35dc0636bbf54b15ccec483c_28.png)

# 打印边界值
print(f"下界 (Lower Bound): {lower:.4f}")
print(f"上界 (Upper Bound): {upper:.4f}")

![](img/a03313fb35dc0636bbf54b15ccec483c_30.png)

# 可视化结果
import matplotlib.pyplot as plt

![](img/a03313fb35dc0636bbf54b15ccec483c_32.png)

plt.figure(figsize=(10, 6))
plt.plot(series, 'o', label='原始数据', alpha=0.7)
plt.plot(filtered_series, 'x', label='MAD处理后的数据', alpha=0.7)
plt.axhline(y=upper, color='r', linestyle='--', label='上界')
plt.axhline(y=lower, color='g', linestyle='--', label='下界')
plt.legend()
plt.title('MAD法去极值效果演示')
plt.xlabel('数据索引')
plt.ylabel('数值')
plt.show()
```

![](img/a03313fb35dc0636bbf54b15ccec483c_34.png)

![](img/a03313fb35dc0636bbf54b15ccec483c_36.png)

运行以上代码，你将看到原始数据中的两个极端点（10和-8）被拉回到了由红色和绿色虚线表示的边界之内。这直观地展示了MAD法如何有效地识别并修正异常值。

![](img/a03313fb35dc0636bbf54b15ccec483c_38.png)

![](img/a03313fb35dc0636bbf54b15ccec483c_40.png)

## 总结
本节课中我们一起学习了MAD（中位数绝对偏差）去极值法。我们首先介绍了它的计算原理，即通过数据的中位数和绝对偏差的中位数来定义数据的正常范围。接着，我们一步步实现了`mad_filter`函数，并通过示例数据和图表验证了该方法的有效性。与百分位法相比，MAD法对极端值不敏感，因此更加稳健，是金融数据预处理中常用的工具之一。