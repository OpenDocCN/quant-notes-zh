# Python金融分析与量化交易实战：P28：基于百分位去极值实例 📊

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_1.png)

## 概述
在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术——基于百分位法去除极值。我们将通过Python代码，手把手实现如何将数据规范到指定的百分位范围内，从而消除异常值对分析结果的影响。

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_3.png)

---

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_5.png)

上一节我们介绍了数据中可能存在极值的问题，本节中我们来看看如何使用百分位法来识别并处理这些极值。

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_7.png)

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_9.png)

我们的目标是：给定一组数据，计算出第一四分位数（Q1，25%）和第三四分位数（Q3，75%）。将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被规范到了一个由Q1和Q3定义的范围内。

以下是实现此功能的核心步骤：

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_11.png)

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_13.png)

1.  **选择数据列**：首先，我们需要从数据集中指定要处理的列。
2.  **编写处理函数**：创建一个函数，其核心逻辑是接收一列数据以及期望保留的最小和最大百分位（例如0.25和0.75）。
3.  **排序与计算分位点**：在函数内部，对数据进行排序，然后计算指定百分位对应的具体数值。
4.  **应用范围限制**：利用`numpy`的`clip`函数，将数据限制在计算出的最小值和最大值之间。
5.  **结果可视化**：最后，通过绘制处理前后的数据分布图，直观地对比去极值的效果。

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_15.png)

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_17.png)

---

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_19.png)

### 代码实现与分步解析

首先，我们导入必要的库并加载数据（这里假设`data`是一个Pandas DataFrame）。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 假设 data 是已经加载的Pandas DataFrame
# 例如：data = pd.read_csv(‘your_data.csv’)
```

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_21.png)

接下来，我们开始编写核心的去极值函数。

**第一步：定义去极值函数**
这个函数接收三个参数：一个Pandas Series（数据列）、最小百分位和最大百分位。

```python
def winsorize_by_percentile(series, min_percentile=0.25, max_percentile=0.75):
    """
    基于百分位进行去极值（缩尾）处理。
    参数:
        series: Pandas Series，待处理的数据列。
        min_percentile: 最小百分位，例如0.25代表Q1。
        max_percentile: 最大百分位，例如0.75代表Q3。
    返回:
        处理后的Pandas Series。
    """
    # 1. 对数据进行排序
    sorted_series = series.sort_values()
    
    # 2. 计算指定百分位对应的具体数值
    # quantile函数可以直接计算分位数
    q = sorted_series.quantile([min_percentile, max_percentile])
    min_value = q.iloc[0]  # 获取最小值（如Q1）
    max_value = q.iloc[1]  # 获取最大值（如Q3）
    
    # 3. 使用np.clip将数据限制在[min_value, max_value]范围内
    # 小于min_value的值变为min_value，大于max_value的值变为max_value
    clipped_series = np.clip(series, min_value, max_value)
    
    return clipped_series
```

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_23.png)

**代码解析**：
*   `series.sort_values()`: 将数据从小到大排序，这是准确计算百分位的前提。
*   `series.quantile([0.25, 0.75])`: Pandas内置函数，直接返回指定百分位列表对应的值。`q.iloc[0]`和`q.iloc[1]`分别对应Q1和Q3。
*   `np.clip(series, min_value, max_value)`: NumPy函数，是完成范围限制的关键。它接受原始数据、最小边界和最大边界，并返回处理后的数组。

**第二步：应用函数并可视化**
现在，我们选择一列数据应用这个函数，并比较处理前后的差异。

```python
# 选择要处理的列，这里以名为‘column_D’的列为例
column_name = ‘column_D‘
original_data = data[column_name]

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_25.png)

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_27.png)

# 应用去极值函数
winsorized_data = winsorize_by_percentile(original_data, 0.25, 0.75)

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_29.png)

# 可视化对比
plt.figure(figsize=(12, 5))

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_31.png)

# 绘制原始数据分布
plt.subplot(1, 2, 1)
original_data.plot(kind=‘hist‘, bins=50, alpha=0.7, color=‘blue‘, edgecolor=‘black‘)
plt.title(‘原始数据分布‘)
plt.xlabel(‘数值‘)
plt.ylabel(‘频次‘)

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_33.png)

# 绘制去极值后的数据分布
plt.subplot(1, 2, 2)
winsorized_data.plot(kind=‘hist‘, bins=50, alpha=0.7, color=‘green‘, edgecolor=‘black‘)
plt.title(‘去极值后数据分布‘)
plt.xlabel(‘数值‘)
plt.ylabel(‘频次‘)

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_35.png)

plt.tight_layout()
plt.show()
```

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_37.png)

**结果说明**：
通过对比左右两幅直方图，可以清晰地看到，去极值后的数据（右图）两端的“尾巴”被截断并集中到了Q1和Q3的位置。这证明了我们的函数成功地将极端值进行了处理。

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_39.png)

---

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_41.png)

### 重要补充与注意事项

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_43.png)

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_45.png)

在实际的金融因子分析中，有两点需要特别注意：

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_47.png)

1.  **百分位的选择**：本例为了展示效果，使用了`0.25`和`0.75`这样较大的范围。在实际应用中，为了更精确地剔除真正的异常值，通常会选择更极端的百分位，例如`0.025`和`0.975`（即保留中间95%的数据），或者`0.01`和`0.99`。你可以通过修改`winsorize_by_percentile`函数的`min_percentile`和`max_percentile`参数来轻松调整。

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_49.png)

2.  **函数的通用性**：我们编写的函数不限于Q1和Q3。通过传入不同的百分位参数，它可以实现任意百分位范围的去极值操作，非常灵活。

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_51.png)

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_53.png)

---

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_55.png)

![](img/2e5d69d870977b3fc8d0343ccdb48c4b_57.png)

## 总结
本节课中我们一起学习了基于百分位法去除数据极值的完整流程。我们掌握了其核心思想：**将数据限制在由低分位点和高分位点定义的区间内**。通过动手实现，我们学会了：
1.  如何使用Pandas的`sort_values`和`quantile`函数进行数据排序和分位数计算。
2.  如何利用NumPy的`clip`函数高效地进行数据范围裁剪。
3.  如何通过可视化直观验证数据处理的效果。
这种方法能有效平滑异常值，使后续的模型分析更加稳健，是金融数据预处理中不可或缺的一步。