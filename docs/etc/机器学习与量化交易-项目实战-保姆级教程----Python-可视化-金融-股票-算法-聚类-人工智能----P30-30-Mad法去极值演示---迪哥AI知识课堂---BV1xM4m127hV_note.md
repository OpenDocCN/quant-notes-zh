# 机器学习与量化交易：P30：MAD法去极值演示

![](img/7537880f9a8563a7f107a380e18ad781_0.png)

![](img/7537880f9a8563a7f107a380e18ad781_2.png)

在本节课中，我们将学习第二种数据去极值方法——MAD法。我们将详细解释其核心概念、计算步骤，并通过代码演示其具体实现过程。

上一节我们介绍了百分位法去极值，本节中我们来看看另一种基于中位数绝对偏差的方法。

![](img/7537880f9a8563a7f107a380e18ad781_4.png)

## MAD法核心概念

MAD法，即中位数绝对偏差法，其核心思想是通过数据的中位数和绝对偏差来设定合理的上下界，从而识别并处理极端值。

![](img/7537880f9a8563a7f107a380e18ad781_6.png)

以下是MAD法的计算步骤：

![](img/7537880f9a8563a7f107a380e18ad781_8.png)

1.  **计算中位数**：首先计算原始数据序列的中位数。
    *   公式：`median = 序列.quantile(0.5)`
2.  **计算绝对偏差**：计算序列中每个数据点与中位数之差的绝对值，形成一个新的偏差序列。
    *   公式：`偏差序列 = abs(原始序列 - median)`
3.  **计算MAD值**：计算上述偏差序列的中位数，这个值就是MAD。
    *   公式：`MAD = 偏差序列.quantile(0.5)`
4.  **确定边界**：根据中位数、MAD值和一个缩放系数N来确定正常数据的上下边界。
    *   上界公式：`上界 = median + N * MAD`
    *   下界公式：`下界 = median - N * MAD`
    *   其中，缩放系数N通常取经验值 **1.4826**。
5.  **处理极值**：将超出上下界的数据点替换为边界值，完成去极值操作。

![](img/7537880f9a8563a7f107a380e18ad781_10.png)

![](img/7537880f9a8563a7f107a380e18ad781_12.png)

## 代码实现

![](img/7537880f9a8563a7f107a380e18ad781_14.png)

![](img/7537880f9a8563a7f107a380e18ad781_16.png)

理解了计算步骤后，我们通过代码来实现MAD去极值函数。

![](img/7537880f9a8563a7f107a380e18ad781_18.png)

![](img/7537880f9a8563a7f107a380e18ad781_20.png)

![](img/7537880f9a8563a7f107a380e18ad781_22.png)

以下是MAD去极值函数的Python实现：

![](img/7537880f9a8563a7f107a380e18ad781_24.png)

![](img/7537880f9a8563a7f107a380e18ad781_26.png)

```python
def filter_extreme_MAD(series, n):
    """
    使用MAD法对序列进行去极值处理。
    
    参数:
    series: pandas Series, 待处理的数据序列。
    n: float, 缩放系数，通常取1.4826。
    
    返回:
    series_capped: pandas Series, 处理后的数据序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算每个数据点与中位数的绝对偏差
    abs_deviation = (series - median).abs()
    
    # 3. 计算绝对偏差的中位数，即MAD值
    mad = abs_deviation.quantile(0.5)
    
    # 4. 根据公式计算上下界
    high = median + n * mad
    low = median - n * mad
    
    # 5. 将超出边界的数据替换为边界值
    series_capped = series.clip(lower=low, upper=high)
    
    return series_capped
```

![](img/7537880f9a8563a7f107a380e18ad781_28.png)

## 函数应用与可视化

![](img/7537880f9a8563a7f107a380e18ad781_30.png)

现在，我们可以使用这个函数来处理实际数据，并观察处理效果。

![](img/7537880f9a8563a7f107a380e18ad781_32.png)

以下是应用函数并绘制对比图的示例代码：

![](img/7537880f9a8563a7f107a380e18ad781_34.png)

![](img/7537880f9a8563a7f107a380e18ad781_36.png)

```python
# 假设 `data_series` 是您的原始数据序列
# 应用MAD去极值函数，n取1.4826
filtered_series_MAD = filter_extreme_MAD(data_series, n=1.4826)

![](img/7537880f9a8563a7f107a380e18ad781_38.png)

![](img/7537880f9a8563a7f107a380e18ad781_40.png)

# 可视化对比
import matplotlib.pyplot as plt

![](img/7537880f9a8563a7f107a380e18ad781_42.png)

![](img/7537880f9a8563a7f107a380e18ad781_44.png)

plt.figure(figsize=(10, 6))
plt.plot(data_series.values, label='原始数据', alpha=0.7)
plt.plot(filtered_series_MAD.values, label='MAD法处理后', alpha=0.9)
plt.legend()
plt.title('MAD法去极值效果对比')
plt.show()
```

![](img/7537880f9a8563a7f107a380e18ad781_46.png)

![](img/7537880f9a8563a7f107a380e18ad781_48.png)

执行上述代码后，蓝色线条代表处理后的数据。可以看到，原先过高或过低的极端值（极值点）被拉回到了由蓝色线条表示的合理边界之内。这就是MAD法的效果。

![](img/7537880f9a8563a7f107a380e18ad781_50.png)

![](img/7537880f9a8563a7f107a380e18ad781_52.png)

本节课中我们一起学习了第二种去极值方法——MAD法。我们掌握了其通过计算中位数和绝对偏差来动态确定数据正常范围的核心原理，并成功实现了对应的代码函数。与百分位法相比，MAD法对极端值不那么敏感，受极端值影响更小，是数据预处理中一个非常实用的工具。