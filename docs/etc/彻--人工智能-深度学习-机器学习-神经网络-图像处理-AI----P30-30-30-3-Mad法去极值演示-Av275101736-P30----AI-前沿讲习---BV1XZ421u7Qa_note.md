Python量化交易：30：MAD法去极值演示

![](img/4a297ced358bc15247055d789c443441_0.png)

![](img/4a297ced358bc15247055d789c443441_2.png)

在本节中，我们将学习第二种数据去极值方法——MAD法。我们将详细解释其计算原理，并通过代码演示如何实现。

上一节我们介绍了百分位法去极值，本节中我们来看看另一种基于中位数绝对偏差的方法。

### MAD法原理

MAD法的核心思想是利用数据的中位数和“中位数绝对偏差”来设定数据的合理范围，从而识别并处理异常值。

![](img/4a297ced358bc15247055d789c443441_4.png)

以下是MAD法的计算步骤：

1.  **计算中位数**：首先，计算原始数据序列的中位数。
    *   **公式**：`median = 序列.quantile(0.5)`

2.  **计算绝对偏差**：计算序列中每个数据点与中位数之差的绝对值，形成一个新的序列。
    *   **公式**：`abs_deviation = |序列 - median|`

![](img/4a297ced358bc15247055d789c443441_6.png)

3.  **计算MAD值**：计算上一步得到的绝对偏差序列的中位数，这个值就是“中位数绝对偏差”（MAD）。
    *   **公式**：`MAD = abs_deviation.quantile(0.5)`

![](img/4a297ced358bc15247055d789c443441_8.png)

4.  **确定上下边界**：使用中位数加上和减去 `N` 倍的MAD值，分别作为数据的上下边界。`N` 是一个常数，通常取 **1.4826**。
    *   **上界公式**：`upper_bound = median + N * MAD`
    *   **下界公式**：`lower_bound = median - N * MAD`

![](img/4a297ced358bc15247055d789c443441_10.png)

![](img/4a297ced358bc15247055d789c443441_12.png)

5.  **处理极值**：将原始数据中超过上界或低于下界的数据点，替换为相应的边界值。

### 代码实现

![](img/4a297ced358bc15247055d789c443441_14.png)

![](img/4a297ced358bc15247055d789c443441_16.png)

理解了原理后，我们通过代码来实现MAD去极值函数。

![](img/4a297ced358bc15247055d789c443441_18.png)

```python
import pandas as pd
import numpy as np

![](img/4a297ced358bc15247055d789c443441_20.png)

![](img/4a297ced358bc15247055d789c443441_22.png)

def filter_extreme_MAD(series, n):
    """
    使用MAD法对序列进行去极值处理。
    
    参数:
    series (pd.Series): 待处理的序列。
    n (float): 倍数，通常为1.4826。
    
    返回:
    pd.Series: 处理后的序列。
    """
    # 1. 计算中位数
    median = series.quantile(0.5)
    
    # 2. 计算绝对偏差序列
    abs_deviation = (series - median).abs()
    
    # 3. 计算MAD值（绝对偏差序列的中位数）
    mad = abs_deviation.quantile(0.5)
    
    # 4. 计算上下边界
    upper_bound = median + n * mad
    lower_bound = median - n * mad
    
    # 5. 对超出边界的数据进行截断处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)
    
    return filtered_series
```

![](img/4a297ced358bc15247055d789c443441_24.png)

### 函数使用演示

![](img/4a297ced358bc15247055d789c443441_26.png)

![](img/4a297ced358bc15247055d789c443441_28.png)

现在，我们使用这个函数来处理一组示例数据。

```python
# 假设我们有一组包含极值的数据
data = pd.Series([1, 2, 3, 4, 5, 6, 7, 100])  # 100是一个明显的极值

![](img/4a297ced358bc15247055d789c443441_30.png)

# 应用MAD去极值函数，n取常用值1.4826
filtered_data = filter_extreme_MAD(data, n=1.4826)

![](img/4a297ced358bc15247055d789c443441_32.png)

![](img/4a297ced358bc15247055d789c443441_34.png)

print("原始数据:")
print(data)
print("\nMAD法处理后的数据:")
print(filtered_data)
```

运行上述代码，你会发现极值 `100` 被替换为了一个基于数据分布计算出的合理上限值。

![](img/4a297ced358bc15247055d789c443441_36.png)

![](img/4a297ced358bc15247055d789c443441_38.png)

### 可视化对比

![](img/4a297ced358bc15247055d789c443441_40.png)

为了更直观地看到处理效果，我们可以将原始数据和处理后的数据绘制成图表。

![](img/4a297ced358bc15247055d789c443441_42.png)

![](img/4a297ced358bc15247055d789c443441_44.png)

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
data.plot(title='原始数据')
plt.axhline(y=filtered_data.max(), color='r', linestyle='--', label='MAD上界')
plt.axhline(y=filtered_data.min(), color='g', linestyle='--', label='MAD下界')
plt.legend()

![](img/4a297ced358bc15247055d789c443441_46.png)

![](img/4a297ced358bc15247055d789c443441_48.png)

plt.subplot(1, 2, 2)
filtered_data.plot(title='MAD法去极值后数据')
plt.tight_layout()
plt.show()
```

![](img/4a297ced358bc15247055d789c443441_50.png)

通过图表可以清晰地看到，MAD法成功地将远离主体数据的点拉回到了由中位数和MAD定义的合理范围内。

![](img/4a297ced358bc15247055d789c443441_52.png)

![](img/4a297ced358bc15247055d789c443441_54.png)

本节课中我们一起学习了MAD去极值法。我们了解了其通过计算数据的中位数和“中位数绝对偏差”来动态确定数据正常范围的核心思想，掌握了其计算步骤，并成功用代码实现了该算法。与百分位法相比，MAD法对极端值不敏感，因此受异常值影响更小，在数据分布未知或存在显著异常值时更为稳健。