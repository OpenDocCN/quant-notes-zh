# Python金融分析量化交易：P29：基于百分位去极值实例 📊

![](img/b64afe7b973fa9177749061837c3a1ab_1.png)

在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术——基于百分位法去除极值。我们将通过编写一个函数，将数据规范到指定的百分位范围内，从而减少异常值对分析结果的影响。

![](img/b64afe7b973fa9177749061837c3a1ab_3.png)

---

![](img/b64afe7b973fa9177749061837c3a1ab_5.png)

上一节我们介绍了数据清洗的重要性，本节中我们来看看如何具体实现基于百分位的去极值操作。

![](img/b64afe7b973fa9177749061837c3a1ab_7.png)

![](img/b64afe7b973fa9177749061837c3a1ab_9.png)

我们的目标是：给定一个数据序列，计算其第一四分位数（Q1）和第三四分位数（Q3）。将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被规范到了一个由Q1和Q3定义的范围内。

![](img/b64afe7b973fa9177749061837c3a1ab_11.png)

以下是实现此功能的核心步骤：

![](img/b64afe7b973fa9177749061837c3a1ab_13.png)

1.  **选择数据列**：首先，我们需要从数据集中指定要进行操作的列。
2.  **编写处理函数**：创建一个函数，该函数接收一个数据序列（Series）以及希望保留的最小和最大百分位（如下四分位和上四分位）。
3.  **计算分位点**：在函数内部，对数据进行排序，并计算指定百分位对应的具体数值。
4.  **应用范围限制**：使用`numpy`的`clip`函数，将序列中所有小于下限的值设置为下限，所有大于上限的值设置为上限。
5.  **可视化对比**：最后，我们将原始数据和处理后的数据绘制成图，直观地观察去极值的效果。

![](img/b64afe7b973fa9177749061837c3a1ab_15.png)

![](img/b64afe7b973fa9177749061837c3a1ab_17.png)

---

![](img/b64afe7b973fa9177749061837c3a1ab_19.png)

## 代码实现与讲解

![](img/b64afe7b973fa9177749061837c3a1ab_21.png)

首先，我们导入必要的库并加载数据（假设数据已加载到变量`data`中）。

![](img/b64afe7b973fa9177749061837c3a1ab_23.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

接下来，我们选择要进行操作的列。这里以列`‘D’`为例。

![](img/b64afe7b973fa9177749061837c3a1ab_25.png)

![](img/b64afe7b973fa9177749061837c3a1ab_27.png)

```python
column_to_process = ‘D’
selected_series = data[column_to_process]
```

![](img/b64afe7b973fa9177749061837c3a1ab_29.png)

现在，我们来编写核心的去极值函数`percentile_clip`。

![](img/b64afe7b973fa9177749061837c3a1ab_31.png)

```python
def percentile_clip(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位对序列进行去极值处理。
    参数:
        series: pandas Series，待处理的数据序列。
        low_percentile: float，希望保留的最小百分位（如下四分位0.25）。
        high_percentile: float，希望保留的最大百分位（如上四分位0.75）。
    返回:
        clipped_series: pandas Series，处理后的数据序列。
    """
    # 步骤1: 对数据进行排序（从小到大）
    sorted_series = series.sort_values()
    
    # 步骤2: 计算指定百分位对应的具体数值
    # 使用quantile函数计算分位数
    q_low = sorted_series.quantile(low_percentile)
    q_high = sorted_series.quantile(high_percentile)
    
    # 步骤3: 使用np.clip将数据限制在[q_low, q_high]范围内
    # np.clip(array, min, max) 将数组中小于min的值设为min，大于max的值设为max
    clipped_series = np.clip(series, q_low, q_high)
    
    return clipped_series
```

**函数逻辑解析**：
*   `series.sort_values()`: 对输入序列进行排序，这是准确计算百分位数的前提。
*   `series.quantile(p)`: Pandas内置函数，直接计算序列在百分位`p`处的值。例如，`quantile(0.25)`得到第一四分位数Q1。
*   `np.clip(series, a_min, a_max)`: NumPy函数，是执行范围限制操作的关键。它返回一个新序列，其中所有小于`a_min`的元素被替换为`a_min`，所有大于`a_max`的元素被替换为`a_max`。

![](img/b64afe7b973fa9177749061837c3a1ab_33.png)

---

## 应用函数并可视化

编写好函数后，我们就可以将其应用到选定的数据列上，并观察效果。

![](img/b64afe7b973fa9177749061837c3a1ab_35.png)

![](img/b64afe7b973fa9177749061837c3a1ab_37.png)

```python
# 应用去极值函数，这里使用默认的25%和75%分位点
clipped_data = percentile_clip(selected_series)

![](img/b64afe7b973fa9177749061837c3a1ab_39.png)

# 可视化对比
plt.figure(figsize=(12, 6))

![](img/b64afe7b973fa9177749061837c3a1ab_41.png)

# 绘制处理后的数据（放在上层，便于观察变化）
plt.plot(clipped_data.values, label=‘Clipped Data (After)’, alpha=0.7, linewidth=2)

![](img/b64afe7b973fa9177749061837c3a1ab_43.png)

# 绘制原始数据
plt.plot(selected_series.values, label=‘Original Data (Before)’, alpha=0.5)

![](img/b64afe7b973fa9177749061837c3a1ab_45.png)

plt.title(‘Comparison: Original Data vs. Percentile-Clipped Data’)
plt.xlabel(‘Index’)
plt.ylabel(‘Value’)
plt.legend()
plt.grid(True, linestyle=‘--’, alpha=0.5)
plt.show()
```

![](img/b64afe7b973fa9177749061837c3a1ab_47.png)

**图像解读**：
在生成的图表中，您将看到两条线。颜色较浅、可能波动范围更大的线代表原始数据。颜色较深、波动被明显“削平”在某个水平线附近的线代表处理后的数据。那些原本低于Q1和高于Q3的极端值，在图中表现为被“拉回”到了Q1和Q3对应的水平线上。

![](img/b64afe7b973fa9177749061837c3a1ab_49.png)

---

![](img/b64afe7b973fa9177749061837c3a1ab_51.png)

## 实际应用注意事项

![](img/b64afe7b973fa9177749061837c3a1ab_53.png)

![](img/b64afe7b973fa9177749061837c3a1ab_55.png)

在实际的金融因子分析中，直接使用25%和75%作为分位点可能过于严格，会将过多数据视为异常。通常，我们会使用更两端的百分位，例如：

![](img/b64afe7b973fa9177749061837c3a1ab_57.png)

![](img/b64afe7b973fa9177749061837c3a1ab_59.png)

```python
# 更常用的参数设置：保留97.5%的数据，去除两端各2.5%的极值
clipped_data_common = percentile_clip(selected_series, low_percentile=0.025, high_percentile=0.975)
```

![](img/b64afe7b973fa9177749061837c3a1ab_61.png)

本节课的例子为了在图表上展示出更明显的效果，有意使用了范围较大的分位点（25%和75%）。您在实践中应根据具体数据和模型需求调整`low_percentile`和`high_percentile`参数。

![](img/b64afe7b973fa9177749061837c3a1ab_63.png)

![](img/b64afe7b973fa9177749061837c3a1ab_65.png)

---

![](img/b64afe7b973fa9177749061837c3a1ab_67.png)

![](img/b64afe7b973fa9177749061837c3a1ab_69.png)

本节课中我们一起学习了基于百分位法去除数据极值的完整流程。我们掌握了如何编写一个灵活的函数来计算任意分位点，并使用`np.clip`函数高效地实现数据范围限制。这种方法能有效平滑异常波动，为后续的金融因子分析和量化模型构建提供更稳健的数据基础。记住，关键在于根据实际情况选择合适的百分位阈值。