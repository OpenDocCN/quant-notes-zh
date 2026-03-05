# 量化交易教程：P29：基于百分位去极值实例 📊

在本节课中，我们将学习如何使用分位数（百分位）方法来识别和处理数据集中的极端值（极值）。这是一种常见的数据预处理技术，旨在将数据规范到一个合理的范围内，以提高后续分析的稳定性。

![](img/fb95ee3bc01be24f7f1b0611a0240729_1.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_2.png)

上一节我们介绍了数据清洗的重要性，本节中我们来看看如何具体实现基于百分位的去极值操作。

![](img/fb95ee3bc01be24f7f1b0611a0240729_4.png)

## 核心思路 💡

![](img/fb95ee3bc01be24f7f1b0611a0240729_5.png)

我们的目标是：对于选定的一列数据，计算其第一四分位数（Q1，25%分位点）和第三四分位数（Q3，75%分位点）。将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被“修剪”并限制在[Q1, Q3]的区间内。

![](img/fb95ee3bc01be24f7f1b0611a0240729_7.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_8.png)

## 代码实现步骤 🛠️

以下是实现该功能的具体步骤。

![](img/fb95ee3bc01be24f7f1b0611a0240729_10.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_11.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_12.png)

### 1. 选择数据列

![](img/fb95ee3bc01be24f7f1b0611a0240729_13.png)

首先，需要指定要对哪一列数据进行操作。这里我们以`DataFrame`中名为`column_name`的列为例。

![](img/fb95ee3bc01be24f7f1b0611a0240729_15.png)

```python
column_name = ‘你的列名’  # 请替换为实际的列名
data_series = data[column_name]
```

### 2. 定义去极值函数

![](img/fb95ee3bc01be24f7f1b0611a0240729_17.png)

接下来，我们编写一个函数来完成基于百分位的去极值操作。该函数接收一个`Series`数据序列，以及最小和最大百分位参数。

![](img/fb95ee3bc01be24f7f1b0611a0240729_19.png)

```python
import numpy as np

![](img/fb95ee3bc01be24f7f1b0611a0240729_21.png)

def winsorize_by_percentile(series, low_percent=0.25, high_percent=0.75):
    “””
    基于百分位进行去极值（缩尾）处理。
    参数:
        series: pandas Series，待处理的数据序列。
        low_percent: float，下限百分位（如0.25代表Q1）。
        high_percent: float，上限百分位（如0.75代表Q3）。
    返回:
        pandas Series，处理后的数据序列。
    “””
    # 对数据进行排序
    sorted_series = series.sort_values()
    
    # 计算指定百分位对应的数值
    # np.percentile 用于计算分位数
    q_low = np.percentile(sorted_series, low_percent * 100)
    q_high = np.percentile(sorted_series, high_percent * 100)
    
    # 使用np.clip将数据限制在[q_low, q_high]范围内
    # 小于q_low的值变为q_low，大于q_high的值变为q_high
    winsorized_series = np.clip(series, q_low, q_high)
    
    return winsorized_series
```

**函数解析：**
*   `series.sort_values()`: 将数据从小到大排序，这是计算准确分位数的前提。
*   `np.percentile(array, q)`: 计算数组在`q`百分位处的值。例如，`np.percentile(data, 25)`计算25%分位数（Q1）。
*   `np.clip(array, a_min, a_max)`: 将数组中的所有元素限制在区间`[a_min, a_max]`内。这是实现“缩尾”操作的核心函数。

![](img/fb95ee3bc01be24f7f1b0611a0240729_23.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_24.png)

### 3. 应用函数并可视化结果

![](img/fb95ee3bc01be24f7f1b0611a0240729_26.png)

定义好函数后，我们可以将其应用到选定的数据列上，并通过绘图对比处理前后的数据分布。

```python
import matplotlib.pyplot as plt

# 应用去极值函数
winsorized_data = winsorize_by_percentile(data_series, low_percent=0.25, high_percent=0.75)

![](img/fb95ee3bc01be24f7f1b0611a0240729_28.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_29.png)

# 创建图形，绘制处理前后的数据分布对比
plt.figure(figsize=(12, 6))

![](img/fb95ee3bc01be24f7f1b0611a0240729_30.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_32.png)

# 绘制原始数据
plt.subplot(2, 1, 1)  # 2行1列，第1个图
plt.plot(data_series.values, label=‘Original Data’)
plt.title(‘Original Data Distribution’)
plt.legend()

# 绘制去极值后的数据
plt.subplot(2, 1, 2)  # 2行1列，第2个图
plt.plot(winsorized_data.values, label=‘Winsorized Data (25%-75%)’, color=‘orange’)
plt.title(‘Data Distribution After Winsorizing’)
plt.legend()

![](img/fb95ee3bc01be24f7f1b0611a0240729_34.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_35.png)

plt.tight_layout()  # 自动调整子图参数，使之填充整个图像区域
plt.show()
```

通过上下对比两个子图，可以清晰地看到，经过处理后，原始数据中过高和过低的极端值被“拉回”到由Q1和Q3定义的边界附近（图中可能体现为一条平直线），而中间部分的数据则保持不变。

## 重要说明 📝

![](img/fb95ee3bc01be24f7f1b0611a0240729_37.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_38.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_39.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_40.png)

在实际的量化研究或数据分析中，通常不会使用25%和75%这样宽松的分位数，因为这会将大量正常数据误判为极值。

![](img/fb95ee3bc01be24f7f1b0611a0240729_41.png)

以下是更常用的参数设置：

*   **更常见的参数**：下限`low_percent=0.025`（2.5%），上限`high_percent=0.975`（97.5%）。这会将两端各2.5%的数据视为极值并进行处理。
*   **参数选择依据**：阈值的选取需要根据具体数据特性和研究目的进行调整。本节课为了在图表上展示更明显的效果，特意使用了较大的范围（25%-75%）。

![](img/fb95ee3bc01be24f7f1b0611a0240729_43.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_44.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_45.png)

## 总结 🎯

![](img/fb95ee3bc01be24f7f1b0611a0240729_46.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_47.png)

![](img/fb95ee3bc01be24f7f1b0611a0240729_48.png)

本节课中我们一起学习了基于百分位去极值的方法。我们掌握了其核心思想，即利用分位数确定数据的正常范围边界，并使用`np.clip`函数将超出边界的值替换为边界值。通过编写`winsorize_by_percentile`函数，我们实现了该过程的自动化，并通过可视化对比验证了处理效果。这种方法简单有效，是数据预处理中处理极端值的常用工具之一。记住，在实际应用时，需要根据情况选择合适的百分位阈值。