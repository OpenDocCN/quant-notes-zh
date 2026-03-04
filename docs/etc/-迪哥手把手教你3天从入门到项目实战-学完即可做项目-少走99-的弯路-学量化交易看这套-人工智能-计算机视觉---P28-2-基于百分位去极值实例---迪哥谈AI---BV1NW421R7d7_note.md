# 金融量化AI实战：P28：2-基于百分位去极值实例 📊

在本节课中，我们将要学习一种在金融数据处理中常用的技术——基于百分位法去除极值。我们将通过编写一个具体的函数，将数据规范到指定的百分位范围内，从而消除异常值对分析结果的影响。

![](img/3c4359a4e20d511f53ea8c2560fd5f12_1.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_2.png)

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何通过编程实现基于百分位的去极值操作。

![](img/3c4359a4e20d511f53ea8c2560fd5f12_4.png)

## 操作目标 🎯

![](img/3c4359a4e20d511f53ea8c2560fd5f12_5.png)

我们需要完成一个操作：将数据规范到一定的范围中。具体方法是，计算数据的第一四分位数（Q1）和第三四分位数（Q3）。所有小于Q1的值都设置为Q1，所有大于Q3的值都设置为Q3。

![](img/3c4359a4e20d511f53ea8c2560fd5f12_7.png)

## 实现步骤 🛠️

![](img/3c4359a4e20d511f53ea8c2560fd5f12_8.png)

以下是实现基于百分位去极值功能的具体步骤。

![](img/3c4359a4e20d511f53ea8c2560fd5f12_10.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_11.png)

### 1. 选择目标列

![](img/3c4359a4e20d511f53ea8c2560fd5f12_12.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_13.png)

首先，需要指定对数据框中的哪一列执行操作。我们选择一个列作为示例。

```python
column_name = ‘your_column_name‘  # 替换为实际的列名
selected_series = data[column_name]
```

### 2. 编写去极值函数

接下来，编写一个函数来执行过滤操作。这个函数将接收一个数据序列（Series），以及希望保留的最小和最大百分位。

```python
import numpy as np

def percentile_winsorize(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位进行去极值处理（缩尾处理）。
    参数:
        series: pandas Series，待处理的数据序列。
        low_percentile: float，下限百分位（如0.25代表Q1）。
        high_percentile: float，上限百分位（如0.75代表Q3）。
    返回:
        winsorized_series: pandas Series，处理后的数据序列。
    """
    # 第一步：对数据进行排序
    sorted_series = series.sort_values()

    # 第二步：计算指定百分位对应的值
    # 使用np.percentile函数计算分位数
    q_low = np.percentile(sorted_series, low_percentile * 100)
    q_high = np.percentile(sorted_series, high_percentile * 100)

    # 第三步：使用np.clip函数将数据规范到[Q1, Q3]区间
    # np.clip(array, min, max) 将数组中小于min的值设为min，大于max的值设为max
    winsorized_series = np.clip(series, q_low, q_high)

    return winsorized_series
```

**核心概念解释**:
*   **`np.percentile(array, q)`**: 计算数组在百分位数 `q` 处的值。例如，`np.percentile(data, 25)` 计算第一四分位数（Q1）。
*   **`np.clip(array, a_min, a_max)`**: 将数组中的元素限制在区间 `[a_min, a_max]` 内。这是实现缩尾处理的关键函数。

![](img/3c4359a4e20d511f53ea8c2560fd5f12_15.png)

### 3. 应用函数并可视化结果

函数编写完成后，将其应用到选定的数据列上，并通过绘图对比处理前后的数据分布。

```python
import matplotlib.pyplot as plt

![](img/3c4359a4e20d511f53ea8c2560fd5f12_17.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_18.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_19.png)

# 应用去极值函数
winsorized_data = percentile_winsorize(selected_series, low_percentile=0.25, high_percentile=0.75)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_21.png)

# 可视化对比
plt.figure(figsize=(12, 6))

![](img/3c4359a4e20d511f53ea8c2560fd5f12_23.png)

# 绘制原始数据分布
plt.subplot(2, 1, 1)  # 创建2行1列的子图，当前为第1个
plt.plot(selected_series.values, ‘b.‘, alpha=0.5, label=‘Original Data‘)
plt.title(‘Original Data Distribution‘)
plt.legend()

![](img/3c4359a4e20d511f53ea8c2560fd5f12_24.png)

# 绘制去极值后的数据分布
plt.subplot(2, 1, 2)  # 切换到第2个子图
plt.plot(winsorized_data.values, ‘r.‘, alpha=0.5, label=‘Winsorized Data (25%-75%)‘)
plt.title(‘Data Distribution After Winsorizing‘)
plt.legend()

plt.tight_layout()  # 自动调整子图参数，使之填充整个图像区域
plt.show()
```

![](img/3c4359a4e20d511f53ea8c2560fd5f12_26.png)

## 注意事项与参数调整 📝

![](img/3c4359a4e20d511f53ea8c2560fd5f12_27.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_28.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_29.png)

在实际的金融因子分析中，直接使用25%和75%作为界限（即Q1和Q3）可能过于严格，会将大量正常数据视为极值。

![](img/3c4359a4e20d511f53ea8c2560fd5f12_30.png)

更常用的参数范围是更靠近两端的百分位，例如：
*   **下限（low_percentile）**: `0.025` (2.5%)
*   **上限（high_percentile）**: `0.975` (97.5%)

这意味著只将分布两端各2.5%的极端值进行缩尾处理，保留了更多数据的原始信息。你可以通过修改函数调用中的参数来调整敏感度：

![](img/3c4359a4e20d511f53ea8c2560fd5f12_32.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_33.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_34.png)

```python
# 使用更常用的2.5%和97.5%分位点进行去极值
winsorized_data_strict = percentile_winsorize(selected_series, low_percentile=0.025, high_percentile=0.975)
```

![](img/3c4359a4e20d511f53ea8c2560fd5f12_35.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_36.png)

![](img/3c4359a4e20d511f53ea8c2560fd5f12_37.png)

本节课中我们一起学习了基于百分位法去除数据极值的完整流程。我们掌握了其核心思想——将超出特定百分位范围的数据截断，并亲自动手实现了一个可复用的缩尾处理函数。这是金融数据预处理中至关重要的一步，能有效降低异常值对后续建模和分析的干扰。记住，根据实际数据的分布情况，灵活调整百分位阈值是关键。