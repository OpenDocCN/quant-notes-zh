# Python金融时间序列分析与量化交易实战教程：P27：26.MAD法去极值演示 📊

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_0.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_2.png)

在本节课中，我们将学习第二种数据去极值的方法——**MAD法**。上一节我们介绍了基于百分位数的去极值方法，本节中我们来看看如何利用中位数绝对偏差来识别和处理异常值。

## 概述 📋

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_4.png)

MAD法是一种基于数据中位数和绝对偏差的稳健去极值方法。它的核心思想是通过计算数据点与中位数偏差的中位数，来确定一个合理的范围，并将超出此范围的值视为极值进行处理。

## MAD法原理与计算步骤 🔍

MAD法的公式看似复杂，但计算过程是清晰的。以下是其核心计算步骤：

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_6.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_8.png)

1.  **计算中位数**：首先，计算原始数据序列的中位数。
    *   公式：`median = 序列.quantile(0.5)`

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_10.png)

2.  **计算绝对偏差**：然后，计算序列中每个数据点与中位数的绝对差值。
    *   公式：`abs_deviation = abs(序列 - median)`

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_12.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_14.png)

3.  **计算MAD值**：接着，计算这些绝对差值序列的中位数，这个值就是**中位数绝对偏差**。
    *   公式：`MAD = abs_deviation.quantile(0.5)`

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_16.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_18.png)

4.  **确定上下界**：最后，利用中位数和MAD值来确定正常数据的范围。通常，我们会乘以一个系数 `n`（常取 `1.4826`）来调整范围。
    *   上界公式：`upper_bound = median + n * MAD`
    *   下界公式：`lower_bound = median - n * MAD`

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_20.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_22.png)

对于超出 `[lower_bound, upper_bound]` 范围的数据点，我们将其视为极值并进行处理（例如，缩放到边界值）。

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_24.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_26.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_28.png)

## 代码实现 💻

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_30.png)

理解了原理后，我们可以将其转化为Python代码。以下是实现MAD去极值功能的函数：

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_32.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_34.png)

```python
def filter_extreme_MAD(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series: 待处理的数据序列 (pandas Series)
        n: 调整范围的系数，默认为1.4826
    返回:
        处理后的数据序列
    """
    # 1. 计算中位数
    median = series.quantile(0.5)
    
    # 2. 计算绝对偏差并求其中位数 (MAD值)
    abs_deviation = (series - median).abs()
    MAD = abs_deviation.quantile(0.5)
    
    # 3. 计算上下界
    upper_bound = median + n * MAD
    lower_bound = median - n * MAD
    
    # 4. 对超出范围的值进行截断处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)
    
    return filtered_series
```

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_36.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_38.png)

## 函数使用与效果对比 📈

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_40.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_42.png)

现在，让我们使用这个函数来处理一组示例数据，并观察其效果。

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_44.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_46.png)

```python
# 假设 `data_series` 是您的原始数据序列
filtered_data_MAD = filter_extreme_MAD(data_series, n=1.4826)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_48.png)

# 可视化对比原始数据与处理后的数据
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
plt.plot(data_series.index, data_series.values, label='原始数据', alpha=0.7)
plt.plot(filtered_data_MAD.index, filtered_data_MAD.values, label='MAD法处理后', linewidth=2)
plt.legend()
plt.title('MAD法去极值效果对比')
plt.show()
```

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_50.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_52.png)

执行上述代码后，您将看到处理后的序列（例如图中的蓝色线）中那些过高或过低的极端值已被拉回到由MAD法确定的合理边界内，而大部分正常数据则保持不变。

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_54.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_56.png)

## 总结 🎯

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_58.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_60.png)

本节课中我们一起学习了**MAD（中位数绝对偏差）去极值法**。我们了解到，这种方法通过两次计算中位数（一次是数据本身，一次是绝对偏差）来构建一个对异常值不敏感的稳健数据范围。与基于百分位数的方法相比，MAD法受极端值的影响更小，因此在数据存在显著异常值时可能更为可靠。

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_62.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_64.png)

以下是两种方法的核心对比：
*   **百分位法**：直接根据数据分布的分位数设定阈值，简单直观。
*   **MAD法**：基于数据的中位数和离散程度（MAD）动态计算阈值，稳健性更强。

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_66.png)

![](img/c8f7ca942a10c5b79d81aa6dbd7cb02d_68.png)

在实际金融数据分析中，您可以根据数据的具体分布特征和业务需求，选择合适的去极值方法，或结合使用多种方法以获取更可靠的结果。