# 量化交易教程：P30：MAD法去极值演示

![](img/e18cd592be91e1fd88428e6c91222388_0.png)

![](img/e18cd592be91e1fd88428e6c91222388_2.png)

在本节课中，我们将要学习第二种数据去极值的方法——MAD法。我们将详细解释其核心概念、计算步骤，并通过代码演示其具体实现。

## 概述

上一节我们介绍了基于百分位法的去极值方法。本节中我们来看看另一种方法，即MAD法。MAD法通过计算数据的中位数绝对偏差来设定数据的合理范围，从而识别并处理极端值。

![](img/e18cd592be91e1fd88428e6c91222388_4.png)

## MAD法核心概念

MAD法的核心思想是：首先计算数据序列的中位数，然后计算每个数据点与中位数之差的绝对值，再求这些绝对值的中位数，这个值被称为中位数绝对偏差。最后，通过中位数加减若干倍的MAD值来设定数据的上下边界，超出此边界的数据点被视为极值并进行处理。

![](img/e18cd592be91e1fd88428e6c91222388_6.png)

![](img/e18cd592be91e1fd88428e6c91222388_7.png)

其核心公式可以概括为：
1.  计算中位数：`median = Median(原始数据序列)`
2.  计算绝对偏差序列：`abs_deviation = |原始数据序列 - median|`
3.  计算MAD值：`MAD = Median(abs_deviation)`
4.  设定边界：
    *   上界：`upper_bound = median + N * MAD`
    *   下界：`lower_bound = median - N * MAD`

![](img/e18cd592be91e1fd88428e6c91222388_9.png)

其中，`N`是一个常数，通常取值为1.4826。

![](img/e18cd592be91e1fd88428e6c91222388_11.png)

## 代码实现步骤

![](img/e18cd592be91e1fd88428e6c91222388_13.png)

![](img/e18cd592be91e1fd88428e6c91222388_14.png)

以下是使用Python实现MAD法去极值的具体步骤。

首先，我们需要导入必要的库并准备示例数据。

![](img/e18cd592be91e1fd88428e6c91222388_16.png)

![](img/e18cd592be91e1fd88428e6c91222388_17.png)

![](img/e18cd592be91e1fd88428e6c91222388_18.png)

![](img/e18cd592be91e1fd88428e6c91222388_19.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

![](img/e18cd592be91e1fd88428e6c91222388_20.png)

# 生成示例数据
np.random.seed(42)
data = pd.Series(np.random.randn(100) * 10 + 50)  # 均值为50，标准差为10的正态分布数据
# 人为添加几个极端值
data.iloc[10] = 150
data.iloc[50] = 5
```

![](img/e18cd592be91e1fd88428e6c91222388_22.png)

接下来，我们定义一个函数来实现MAD法去极值。

![](img/e18cd592be91e1fd88428e6c91222388_24.png)

![](img/e18cd592be91e1fd88428e6c91222388_25.png)

```python
def mad_filter(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series: 待处理的Pandas Series序列。
        n: 用于计算边界的倍数，默认为1.4826。
    返回:
        filtered_series: 处理后的序列。
        lower_bound: 计算出的下界。
        upper_bound: 计算出的上界。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算每个数据点与中位数之差的绝对值
    abs_deviation = (series - median).abs()
    
    # 3. 计算绝对值序列的中位数，即MAD值
    mad = abs_deviation.quantile(0.5)
    
    # 4. 计算上下边界
    upper_bound = median + n * mad
    lower_bound = median - n * mad
    
    # 5. 对超出边界的数据进行截断处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)
    
    return filtered_series, lower_bound, upper_bound
```

![](img/e18cd592be91e1fd88428e6c91222388_27.png)

![](img/e18cd592be91e1fd88428e6c91222388_28.png)

现在，我们可以调用这个函数来处理我们的数据，并可视化结果。

```python
# 应用MAD法去极值
filtered_data, lower, upper = mad_filter(data)

![](img/e18cd592be91e1fd88428e6c91222388_30.png)

![](img/e18cd592be91e1fd88428e6c91222388_31.png)

![](img/e18cd592be91e1fd88428e6c91222388_32.png)

# 打印边界信息
print(f'下界 (Lower Bound): {lower:.2f}')
print(f'上界 (Upper Bound): {upper:.2f}')

![](img/e18cd592be91e1fd88428e6c91222388_33.png)

# 可视化原始数据与处理后的数据
plt.figure(figsize=(10, 6))
plt.plot(data, label='原始数据', alpha=0.7)
plt.plot(filtered_data, label='MAD法处理后数据', linestyle='--')
plt.axhline(y=upper, color='r', linestyle=':', label=f'上界 ({upper:.2f})')
plt.axhline(y=lower, color='g', linestyle=':', label=f'下界 ({lower:.2f})')
plt.legend()
plt.title('MAD法去极值效果演示')
plt.xlabel('数据索引')
plt.ylabel('数值')
plt.show()
```

![](img/e18cd592be91e1fd88428e6c91222388_35.png)

![](img/e18cd592be91e1fd88428e6c91222388_36.png)

## 总结

![](img/e18cd592be91e1fd88428e6c91222388_38.png)

![](img/e18cd592be91e1fd88428e6c91222388_39.png)

![](img/e18cd592be91e1fd88428e6c91222388_40.png)

本节课中我们一起学习了MAD法去极值。我们首先理解了MAD法的原理，即通过中位数和绝对偏差中位数来稳健地定义数据的正常范围。然后，我们一步步实现了该方法的代码，包括计算中位数、MAD值以及上下边界，并对极值进行了截断处理。最后，通过可视化对比了处理前后的数据。MAD法对异常值不敏感，是金融数据分析中处理极值的一种常用且有效的方法。