# 机器学习金融分析：P29：基于百分位去极值实例 📊

![](img/a6ae8d07f21010b4035d1f63550afec5_1.png)

![](img/a6ae8d07f21010b4035d1f63550afec5_3.png)

在本节课中，我们将学习如何使用Python，基于百分位法（例如四分位数）来去除数据集中的极端值。这是一种常见的数据预处理技术，旨在将数据规范到合理的范围内，以提高后续分析的稳健性。

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。

![](img/a6ae8d07f21010b4035d1f63550afec5_5.png)

![](img/a6ae8d07f21010b4035d1f63550afec5_7.png)

## 核心思路与步骤

![](img/a6ae8d07f21010b4035d1f63550afec5_9.png)

我们的目标是：给定一个数据列，计算其下四分位数（Q1，25%）和上四分位数（Q3，75%）。然后，将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被“修剪”并限制在[Q1, Q3]的范围内。

![](img/a6ae8d07f21010b4035d1f63550afec5_11.png)

![](img/a6ae8d07f21010b4035d1f63550afec5_13.png)

以下是实现此功能的具体步骤：

![](img/a6ae8d07f21010b4035d1f63550afec5_15.png)

1.  **选择目标列**：首先，我们需要从数据集中指定要进行处理的列。
2.  **定义处理函数**：编写一个函数，该函数接收一个数据序列（Series）以及自定义的下限和上限百分位（例如0.25和0.75）。
3.  **计算分位点**：在函数内部，对数据进行排序，并计算指定百分位对应的具体数值（即Q1和Q3）。
4.  **应用数值裁剪**：使用`np.clip`函数，将序列中所有小于Q1的值替换为Q1，所有大于Q3的值替换为Q3。
5.  **验证结果**：通过绘制处理前后的数据分布图，直观地对比去极值的效果。

![](img/a6ae8d07f21010b4035d1f63550afec5_17.png)

![](img/a6ae8d07f21010b4035d1f63550afec5_19.png)

## 代码实现详解

![](img/a6ae8d07f21010b4035d1f63550afec5_21.png)

接下来，我们通过代码来一步步实现上述逻辑。

![](img/a6ae8d07f21010b4035d1f63550afec5_23.png)

首先，我们导入必要的库并加载数据（假设数据已存在于`data`这个DataFrame中）。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

![](img/a6ae8d07f21010b4035d1f63550afec5_25.png)

![](img/a6ae8d07f21010b4035d1f63550afec5_27.png)

# 假设 data 是一个已加载的 pandas DataFrame
# 例如：data = pd.read_csv('your_data.csv')
```

![](img/a6ae8d07f21010b4035d1f63550afec5_29.png)

### 第一步：选择目标列

我们选择DataFrame中的某一列进行处理。这里以列名为`‘D’`的列为例。

![](img/a6ae8d07f21010b4035d1f63550afec5_31.png)

```python
column_to_process = 'D'
original_series = data[column_to_process]
```

![](img/a6ae8d07f21010b4035d1f63550afec5_33.png)

### 第二步：定义去极值函数

我们定义一个名为`remove_outliers_by_percentile`的函数。

![](img/a6ae8d07f21010b4035d1f63550afec5_35.png)

```python
def remove_outliers_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位去除序列中的极值。
    参数:
        series: pandas Series，待处理的数据序列。
        low_percentile: float，下限百分位（默认0.25，即Q1）。
        high_percentile: float，上限百分位（默认0.75，即Q3）。
    返回:
        pandas Series，处理后的数据序列。
    """
    # 对数据进行排序，为计算分位数做准备
    sorted_series = series.sort_values()

    # 计算指定百分位对应的具体数值
    # quantile函数接收一个列表，返回对应分位数的值
    q_values = sorted_series.quantile([low_percentile, high_percentile])

    low_bound = q_values.iloc[0]  # 下限值，例如Q1
    high_bound = q_values.iloc[1] # 上限值，例如Q3

    # 使用np.clip函数将数据裁剪到[low_bound, high_bound]区间
    # 语法：np.clip(原始序列, 最小值, 最大值)
    clipped_series = np.clip(series, low_bound, high_bound)

    return clipped_series
```

![](img/a6ae8d07f21010b4035d1f63550afec5_37.png)

**函数核心解释**：
*   `series.quantile([0.25, 0.75])`：Pandas内置函数，直接计算序列的25%和75%分位数。
*   `np.clip(a, a_min, a_max)`：NumPy函数，将数组`a`中的所有元素限制在`a_min`和`a_max`之间。小于`a_min`的变为`a_min`，大于`a_max`的变为`a_max`。

![](img/a6ae8d07f21010b4035d1f63550afec5_39.png)

### 第三步：应用函数并可视化结果

![](img/a6ae8d07f21010b4035d1f63550afec5_41.png)

现在，我们使用定义好的函数处理数据，并通过图表对比处理前后的差异。

![](img/a6ae8d07f21010b4035d1f63550afec5_43.png)

```python
# 应用去极值函数
processed_series = remove_outliers_by_percentile(original_series, 0.25, 0.75)

![](img/a6ae8d07f21010b4035d1f63550afec5_45.png)

# 创建图形，绘制处理前后的数据分布（箱线图对比明显）
plt.figure(figsize=(10, 6))

![](img/a6ae8d07f21010b4035d1f63550afec5_47.png)

![](img/a6ae8d07f21010b4035d1f63550afec5_49.png)

# 第一个子图：原始数据
plt.subplot(2, 1, 1) # (行，列，索引)
plt.boxplot(original_series.dropna(), vert=False) # dropna()忽略缺失值
plt.title('原始数据分布')
plt.xlabel('数值')

![](img/a6ae8d07f21010b4035d1f63550afec5_51.png)

# 第二个子图：处理后的数据
plt.subplot(2, 1, 2)
plt.boxplot(processed_series.dropna(), vert=False)
plt.title('基于百分位去极值后的数据分布')
plt.xlabel('数值')

![](img/a6ae8d07f21010b4035d1f63550afec5_53.png)

![](img/a6ae8d07f21010b4035d1f63550afec5_55.png)

plt.tight_layout() # 自动调整子图参数，使之填充整个图像区域
plt.show()
```

![](img/a6ae8d07f21010b4035d1f63550afec5_57.png)

![](img/a6ae8d07f21010b4035d1f63550afec5_59.png)

## 结果解读与注意事项

![](img/a6ae8d07f21010b4035d1f63550afec5_61.png)

通过对比上下两个箱线图，可以清晰地看到：
*   **原始数据**：可能存在远离箱体的“圆圈”或“星号”，这些就是被识别的极端值（异常值）。
*   **处理后数据**：箱体两侧的“须”会缩短，极端值点消失，因为所有数据都被限制在了Q1和Q3的范围内。

![](img/a6ae8d07f21010b4035d1f63550afec5_63.png)

![](img/a6ae8d07f21010b4035d1f63550afec5_65.png)

**重要提示**：
1.  **百分位选择**：本例中使用25%和75%四分位是为了演示效果明显。在实际金融因子分析中，为了保留更多信息，通常会选择更宽松的界限，例如**2.5%和97.5%**（即`low_percentile=0.025, high_percentile=0.975`）。具体阈值需根据业务知识和数据特性进行调整。
2.  **函数通用性**：我们定义的函数`remove_outliers_by_percentile`非常灵活，你可以通过修改`low_percentile`和`high_percentile`参数来应用不同的百分位标准。

![](img/a6ae8d07f21010b4035d1f63550afec5_67.png)

![](img/a6ae8d07f21010b4035d1f63550afec5_69.png)

本节课中我们一起学习了如何基于百分位法去除数据极值。我们掌握了从选择数据、定义处理函数（核心是`quantile`计算分位数和`np.clip`进行数值裁剪），到结果可视化的完整流程。这是数据预处理中非常实用且关键的一步，能为后续的量化模型构建提供更干净、更稳定的数据基础。