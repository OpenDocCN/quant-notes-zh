# Python金融量化：P29：基于百分位去极值实例

![](img/95e9395eb0806f1ec0bb11e81139b4ee_1.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_3.png)

## 概述
在本节课中，我们将要学习一种在金融数据分析中常用的数据预处理技术——基于百分位法去除极值。我们将通过编写一个函数，将数据中超出指定百分位范围（例如，小于25%分位数或大于75%分位数）的值，替换为对应的边界值，从而将数据规范到一个合理的范围内。

![](img/95e9395eb0806f1ec0bb11e81139b4ee_5.png)

## 核心步骤与代码实现

![](img/95e9395eb0806f1ec0bb11e81139b4ee_7.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_9.png)

上一节我们介绍了去极值的概念，本节中我们来看看如何通过编写函数来实现基于百分位的去极值操作。

![](img/95e9395eb0806f1ec0bb11e81139b4ee_11.png)

首先，我们需要明确操作的目标：对数据框（DataFrame）中的某一列数据进行处理。我们选择一个列名作为操作对象。

![](img/95e9395eb0806f1ec0bb11e81139b4ee_13.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_15.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_17.png)

以下是实现该功能的核心函数：

![](img/95e9395eb0806f1ec0bb11e81139b4ee_19.png)

```python
def filter_extremes_percentile(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位法去除极值。
    参数:
        series: 待处理的Pandas Series数据。
        low_percentile: 下分位数，默认0.25（Q1）。
        high_percentile: 上分位数，默认0.75（Q3）。
    返回:
        处理后的Series数据。
    """
    # 第一步：对数据进行排序
    sorted_series = series.sort_values()

    # 第二步：计算指定百分位的值
    q = sorted_series.quantile([low_percentile, high_percentile])
    low_bound = q.iloc[0]  # 下边界值（如Q1）
    high_bound = q.iloc[1] # 上边界值（如Q3）

    # 第三步：使用clip函数将数据规范到[low_bound, high_bound]区间
    # 小于low_bound的值变为low_bound，大于high_bound的值变为high_bound
    filtered_series = series.clip(lower=low_bound, upper=high_bound)

    return filtered_series
```

![](img/95e9395eb0806f1ec0bb11e81139b4ee_21.png)

## 函数应用与效果对比

函数编写完成后，我们可以将其应用到具体的数据列上。

![](img/95e9395eb0806f1ec0bb11e81139b4ee_23.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_25.png)

以下是应用函数并可视化对比处理前后效果的步骤：

![](img/95e9395eb0806f1ec0bb11e81139b4ee_27.png)

```python
# 1. 选择要处理的列，例如 ‘column_name’
target_column = ‘column_name’

![](img/95e9395eb0806f1ec0bb11e81139b4ee_29.png)

# 2. 应用去极值函数
original_data = data[target_column]
filtered_data = filter_extremes_percentile(original_data)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_31.png)

# 3. 绘制处理前后的数据分布图进行对比
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 5))

![](img/95e9395eb0806f1ec0bb11e81139b4ee_33.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_35.png)

# 绘制原始数据
plt.subplot(1, 2, 1)
original_data.plot(kind=‘hist’, title=‘原始数据分布’)
plt.axvline(x=original_data.quantile(0.25), color=‘r’, linestyle=‘--’, label=‘Q1’)
plt.axvline(x=original_data.quantile(0.75), color=‘g’, linestyle=‘--’, label=‘Q3’)
plt.legend()

![](img/95e9395eb0806f1ec0bb11e81139b4ee_37.png)

# 绘制处理后的数据
plt.subplot(1, 2, 2)
filtered_data.plot(kind=‘hist’, title=‘去极值后数据分布’)
plt.axvline(x=filtered_data.min(), color=‘r’, linestyle=‘--’, label=‘新下界’)
plt.axvline(x=filtered_data.max(), color=‘g’, linestyle=‘--’, label=‘新上界’)
plt.legend()

![](img/95e9395eb0806f1ec0bb11e81139b4ee_39.png)

plt.tight_layout()
plt.show()
```

![](img/95e9395eb0806f1ec0bb11e81139b4ee_41.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_43.png)

通过对比图表可以清晰地看到，原始数据中超出Q1和Q3范围的“极值”点，在处理后都被“拉回”到了边界上，数据的整体分布范围被有效规范。

![](img/95e9395eb0806f1ec0bb11e81139b4ee_45.png)

## 参数调整与实际应用

![](img/95e9395eb0806f1ec0bb11e81139b4ee_47.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_49.png)

在演示中，我们使用了25%和75%作为分位点，这会将较多数据视为极值进行处理，主要是为了展示效果更明显。

![](img/95e9395eb0806f1ec0bb11e81139b4ee_51.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_53.png)

在实际的金融因子分析中，通常会使用更严格的范围，例如：
*   **下分位数（low_percentile）**: `0.025` (2.5%)
*   **上分位数（high_percentile）**: `0.975` (97.5%)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_55.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_57.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_59.png)

这样可以只将分布两端各2.5%的极端值进行修正，在控制异常值影响的同时，更大程度地保留原始数据的信息。您可以通过修改函数调用时的参数来调整去极值的严格程度。

![](img/95e9395eb0806f1ec0bb11e81139b4ee_61.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_63.png)

```python
# 实际应用中更常用的参数设置
filtered_data_strict = filter_extremes_percentile(original_data, low_percentile=0.025, high_percentile=0.975)
```

![](img/95e9395eb0806f1ec0bb11e81139b4ee_65.png)

![](img/95e9395eb0806f1ec0bb11e81139b4ee_67.png)

## 总结
本节课中我们一起学习了基于百分位法去除数据极值的完整流程。我们首先定义了一个函数，其核心步骤包括**数据排序**、**计算分位点**以及使用 **`clip()`函数进行数据裁剪**。然后，我们将该函数应用于具体数据，并通过可视化对比直观展示了去极值的效果。最后，我们讨论了如何根据实际分析需求调整分位点参数。这种方法能有效平滑异常值对整体数据分析的影响，是金融量化研究中数据预处理的关键步骤之一。