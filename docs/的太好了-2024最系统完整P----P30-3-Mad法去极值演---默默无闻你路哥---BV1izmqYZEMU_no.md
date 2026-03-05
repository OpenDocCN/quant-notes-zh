# Python金融分析与量化交易实战教程：P30：3-MAD法去极值演示 📊

![](img/e44064fb4743ce5a5044db4e6a04e38e_0.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_2.png)

在本节课中，我们将要学习第二种数据去极值的方法——MAD法。我们将详细解释其原理，并通过代码演示如何实现。

## 概述

![](img/e44064fb4743ce5a5044db4e6a04e38e_4.png)

上一节我们介绍了百分位法去极值，本节中我们来看看另一种稳健的方法：MAD法。MAD法通过计算数据的中位数绝对偏差来识别异常值，对极端值不敏感，因此在金融数据分析中非常有用。

## MAD法原理

MAD法的核心思想是：如果一个数据点距离数据中位数的距离，超过了某个基于“中位数绝对偏差”计算的阈值，则被视为异常值。

![](img/e44064fb4743ce5a5044db4e6a04e38e_6.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_8.png)

以下是计算步骤：

![](img/e44064fb4743ce5a5044db4e6a04e38e_10.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_12.png)

1.  首先，计算原始数据序列的中位数。
    *   **公式**：`median = median(series)`
2.  接着，计算每个数据点与中位数差值的绝对值，形成一个新的序列。
    *   **公式**：`abs_diff = |series - median|`
3.  然后，计算这个绝对值序列的中位数，这个值就是“中位数绝对偏差”（MAD）。
    *   **公式**：`MAD = median(abs_diff)`
4.  最后，设定上下界。通常使用一个缩放系数 `N`（常取 1.4826）与 MAD 相乘，再与中位数相加或相减，得到正常值的范围。
    *   **上界公式**：`upper_bound = median + N * MAD`
    *   **下界公式**：`lower_bound = median - N * MAD`
5.  任何超出 `[lower_bound, upper_bound]` 范围的数据点，都将被视为极值并进行处理（如缩尾或截尾）。

![](img/e44064fb4743ce5a5044db4e6a04e38e_14.png)

## 代码实现

![](img/e44064fb4743ce5a5044db4e6a04e38e_16.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_18.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_20.png)

理解了原理后，我们通过代码来实现MAD去极值函数。以下是具体的实现步骤：

![](img/e44064fb4743ce5a5044db4e6a04e38e_22.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_24.png)

```python
import pandas as pd
import numpy as np

![](img/e44064fb4743ce5a5044db4e6a04e38e_26.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_28.png)

def filter_extreme_MAD(series, n):
    """
    使用MAD法对序列进行去极值处理。
    
    参数:
    series (pd.Series): 待处理的序列。
    n (float): 缩放系数，通常为1.4826。
    
    返回:
    pd.Series: 处理后的序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算每个数据点与中位数差值的绝对值
    abs_diff = (series - median).abs()
    
    # 3. 计算绝对值序列的中位数，即MAD值
    MAD = abs_diff.quantile(0.5)
    
    # 4. 计算上下界
    upper_bound = median + n * MAD
    lower_bound = median - n * MAD
    
    # 5. 对超出范围的值进行缩尾处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)
    
    return filtered_series
```

![](img/e44064fb4743ce5a5044db4e6a04e38e_30.png)

## 应用演示

![](img/e44064fb4743ce5a5044db4e6a04e38e_32.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_34.png)

现在，我们使用这个函数来处理一组示例数据，并可视化处理前后的对比。

```python
# 假设我们有一组包含极值的金融数据
np.random.seed(42)
data = pd.Series(np.random.randn(100) * 10)  # 生成100个正态分布数据
data.iloc[0] = 100  # 人为添加一个极大值
data.iloc[1] = -80  # 人为添加一个极小值

![](img/e44064fb4743ce5a5044db4e6a04e38e_36.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_38.png)

# 应用MAD法去极值，使用常用缩放系数1.4826
filtered_data = filter_extreme_MAD(data, n=1.4826)

![](img/e44064fb4743ce5a5044db4e6a04e38e_40.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_42.png)

# 可视化对比
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
plt.plot(data, label='原始数据', alpha=0.7)
plt.plot(filtered_data, label='MAD法处理后', alpha=0.9)
plt.axhline(y=filtered_data.max(), color='r', linestyle='--', alpha=0.5, label='处理上界')
plt.axhline(y=filtered_data.min(), color='r', linestyle='--', alpha=0.5, label='处理下界')
plt.legend()
plt.title('MAD法去极值效果对比')
plt.show()
```

![](img/e44064fb4743ce5a5044db4e6a04e38e_44.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_46.png)

运行以上代码，可以看到原始数据中的两个极端值（100和-80）被成功地“拉回”到由MAD法计算出的正常值范围内。蓝色线代表原始数据，橙色线代表处理后的数据，红色虚线标出了处理后的上下边界。

![](img/e44064fb4743ce5a5044db4e6a04e38e_48.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_50.png)

## 总结

![](img/e44064fb4743ce5a5044db4e6a04e38e_52.png)

![](img/e44064fb4743ce5a5044db4e6a04e38e_54.png)

本节课中我们一起学习了MAD法去极值。我们首先了解了MAD法的计算原理，它通过中位数和**中位数绝对偏差**来定义数据的正常范围，比基于均值和标准差的方法更稳健。接着，我们一步步实现了 `filter_extreme_MAD` 函数，并使用它处理了包含异常值的数据，通过图表直观地展示了其处理效果。MAD法是金融数据预处理中一个非常实用的工具。