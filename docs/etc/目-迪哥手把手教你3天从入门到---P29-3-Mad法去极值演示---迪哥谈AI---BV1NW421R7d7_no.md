# 金融量化分析：P29：3-MAD法去极值演示 📊

![](img/854d9c8f406ea043002cb39bcd7abf50_0.png)

![](img/854d9c8f406ea043002cb39bcd7abf50_2.png)

在本节课中，我们将要学习第二种数据去极值的方法——MAD法。我们将详细解释其原理，并通过代码演示如何实现它。

上一节我们介绍了百分位法去极值，本节中我们来看看另一种基于中位数绝对偏差的方法。

## MAD法原理

![](img/854d9c8f406ea043002cb39bcd7abf50_4.png)

MAD法的核心思想是通过数据的中位数和“中位数绝对偏差”来设定合理的上下界，从而识别并处理极端值。其计算过程分为两步。

以下是MAD值的计算步骤：
1.  首先，计算原始数据序列的中位数。
2.  然后，计算序列中每个数据点与中位数之差的绝对值，形成一个新的序列。
3.  最后，对这个新的绝对值序列再次求中位数，得到的结果即为MAD值。

![](img/854d9c8f406ea043002cb39bcd7abf50_6.png)

![](img/854d9c8f406ea043002cb39bcd7abf50_7.png)

得到MAD值后，我们可以设定数据的正常范围。通常使用以下公式来确定上下界：
*   **上界** = 中位数 + N × MAD
*   **下界** = 中位数 - N × MAD

![](img/854d9c8f406ea043002cb39bcd7abf50_8.png)

![](img/854d9c8f406ea043002cb39bcd7abf50_10.png)

其中，**N** 是一个常数，通常取值为 **1.4826**。任何超出此上下界的数据点将被视为极值并进行处理（例如缩尾至边界值）。

![](img/854d9c8f406ea043002cb39bcd7abf50_12.png)

## 代码实现

理解了原理后，我们来看看如何用代码实现MAD去极值法。我们将按照上述步骤编写一个函数。

![](img/854d9c8f406ea043002cb39bcd7abf50_14.png)

![](img/854d9c8f406ea043002cb39bcd7abf50_15.png)

![](img/854d9c8f406ea043002cb39bcd7abf50_16.png)

![](img/854d9c8f406ea043002cb39bcd7abf50_17.png)

以下是`mad_filter`函数的实现代码：

![](img/854d9c8f406ea043002cb39bcd7abf50_18.png)

```python
def mad_filter(s, n=1.4826):
    # 1. 计算原始序列的中位数
    median = s.quantile(0.5)
    
    # 2. 计算每个数据点与中位数差值的绝对值
    abs_diff = (s - median).abs()
    
    # 3. 计算绝对值序列的中位数，即MAD值
    mad = abs_diff.quantile(0.5)
    
    # 4. 计算上下界
    high = median + n * mad
    low = median - n * mad
    
    # 5. 对超出边界的数据进行缩尾处理
    result = s.clip(lower=low, upper=high)
    
    return result
```

![](img/854d9c8f406ea043002cb39bcd7abf50_20.png)

现在，我们可以调用这个函数来处理数据。

以下是函数调用与结果可视化的示例：

![](img/854d9c8f406ea043002cb39bcd7abf50_22.png)

```python
# 假设 s 是待处理的数据序列
filtered_data = mad_filter(s, n=1.4826)

![](img/854d9c8f406ea043002cb39bcd7abf50_23.png)

![](img/854d9c8f406ea043002cb39bcd7abf50_24.png)

# 可视化原始数据与处理后的数据
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
s.plot(label='原始数据')
filtered_data.plot(label='MAD法处理后数据')
plt.legend()
plt.show()
```

![](img/854d9c8f406ea043002cb39bcd7abf50_26.png)

执行代码后，图表将显示：原始数据中超出蓝色边界（即上下界）的部分，在结果中已被调整至边界值。

![](img/854d9c8f406ea043002cb39bcd7abf50_28.png)

![](img/854d9c8f406ea043002cb39bcd7abf50_29.png)

![](img/854d9c8f406ea043002cb39bcd7abf50_30.png)

本节课中我们一起学习了MAD去极值法。这种方法通过中位数和MAD值来动态确定数据的合理范围，对异常值不敏感，鲁棒性较强。它与百分位法一样，是金融数据预处理中常用的工具。