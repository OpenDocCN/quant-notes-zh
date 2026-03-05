# Python金融分析与量化交易实战课程：P26：MAD法去极值演示

![](img/106b59e0aa97a479a7dfc9680e4d74a0_0.png)

![](img/106b59e0aa97a479a7dfc9680e4d74a0_2.png)

在本节课中，我们将学习第二种数据去极值的方法——MAD法。我们将详细解释其原理，并通过Python代码演示如何实现该方法。

上一节我们介绍了基于百分位法的去极值操作，本节中我们来看看另一种基于中位数绝对偏差的方法。

## MAD法原理

![](img/106b59e0aa97a479a7dfc9680e4d74a0_4.png)

MAD法，即中位数绝对偏差法，其核心思想是通过数据的中位数和偏差来设定合理的上下界，从而识别并处理极端值。

首先，给定一个数据序列，例如 `[1, 2, 3, 4, 5, 6, 7]`。

第一步，计算该序列的中位数。假设中位数 `median = 4`。

![](img/106b59e0aa97a479a7dfc9680e4d74a0_6.png)

![](img/106b59e0aa97a479a7dfc9680e4d74a0_8.png)

第二步，计算序列中每个数值与中位数之差的绝对值，形成一个新的偏差序列。计算过程如下：
*   `|1 - 4| = 3`
*   `|2 - 4| = 2`
*   `|3 - 4| = 1`
*   `|4 - 4| = 0`
*   `|5 - 4| = 1`
*   `|6 - 4| = 2`
*   `|7 - 4| = 3`
得到偏差序列 `[3, 2, 1, 0, 1, 2, 3]`。

![](img/106b59e0aa97a479a7dfc9680e4d74a0_10.png)

第三步，对这个偏差序列再次求中位数，得到的值即为 **MAD**。

![](img/106b59e0aa97a479a7dfc9680e4d74a0_12.png)

第四步，利用中位数、MAD和一个缩放系数 `N` 来计算数据的上下界。公式如下：
*   **上界** = `median + N * MAD`
*   **下界** = `median - N * MAD`

![](img/106b59e0aa97a479a7dfc9680e4d74a0_14.png)

其中，缩放系数 `N` 通常取一个固定值 **1.4826**。最后，将所有超出 `[下界， 上界]` 范围的数据替换为边界值，其操作逻辑与百分位法一致。

![](img/106b59e0aa97a479a7dfc9680e4d74a0_16.png)

![](img/106b59e0aa97a479a7dfc9680e4d74a0_18.png)

## Python代码实现

![](img/106b59e0aa97a479a7dfc9680e4d74a0_20.png)

![](img/106b59e0aa97a479a7dfc9680e4d74a0_22.png)

以下是按照上述原理实现的MAD去极值函数。

![](img/106b59e0aa97a479a7dfc9680e4d74a0_24.png)

![](img/106b59e0aa97a479a7dfc9680e4d74a0_26.png)

```python
import pandas as pd
import numpy as np

def filter_extreme_MAD(series, n):
    """
    使用MAD法对序列进行去极值处理。
    
    参数:
    series (pd.Series): 待处理的数据序列。
    n (float): 缩放系数，通常为1.4826。
    
    返回:
    pd.Series: 处理后的数据序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算每个数据与中位数差值的绝对值序列
    abs_diff = (series - median).abs()
    
    # 3. 计算该绝对值序列的中位数，即MAD值
    mad = abs_diff.quantile(0.5)
    
    # 4. 计算上下界
    high = median + n * mad
    low = median - n * mad
    
    # 5. 将超出范围的值替换为边界值
    filtered_series = series.copy()
    filtered_series[filtered_series > high] = high
    filtered_series[filtered_series < low] = low
    
    return filtered_series
```

![](img/106b59e0aa97a479a7dfc9680e4d74a0_28.png)

## 函数应用与结果可视化

定义好函数后，我们可以将其应用于实际数据，并可视化处理前后的对比。

![](img/106b59e0aa97a479a7dfc9680e4d74a0_30.png)

![](img/106b59e0aa97a479a7dfc9680e4d74a0_32.png)

```python
# 假设 `data` 是包含待处理数据的Pandas Series
# 例如: data = pd.Series([...你的数据...])

![](img/106b59e0aa97a479a7dfc9680e4d74a0_34.png)

# 应用MAD去极值函数，n取1.4826
filtered_data_MAD = filter_extreme_MAD(data, n=1.4826)

# 可视化结果
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
data.plot(label='原始数据', alpha=0.7)
filtered_data_MAD.plot(label='MAD法处理后', alpha=0.7)
plt.legend()
plt.title('MAD法去极值效果对比')
plt.show()
```

![](img/106b59e0aa97a479a7dfc9680e4d74a0_36.png)

![](img/106b59e0aa97a479a7dfc9680e4d74a0_38.png)

执行以上代码后，蓝色曲线代表原始数据，橙色曲线代表经过MAD法处理后的数据。可以观察到，超出计算边界的极端值已被拉回至边界处。

![](img/106b59e0aa97a479a7dfc9680e4d74a0_40.png)

![](img/106b59e0aa97a479a7dfc9680e4d74a0_42.png)

本节课中我们一起学习了第二种去极值方法——MAD法。我们理解了其通过中位数和绝对偏差来动态确定数据正常范围的核心思想，并掌握了用Python代码实现该过程的具体步骤。MAD法对异常值不敏感，在数据存在极端离群点时，通常比基于均值和标准差的方法更稳健。