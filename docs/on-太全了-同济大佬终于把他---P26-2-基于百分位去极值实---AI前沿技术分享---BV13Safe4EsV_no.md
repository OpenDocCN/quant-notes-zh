# 金融量化分析：P26：基于百分位去极值实例 📊

## 概述
在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术——基于百分位法去除极值。我们将通过一个具体的Python实例，演示如何将数据规范到指定的百分位范围内，从而减少异常值对分析结果的影响。

![](img/e539e307b3d1bbdfaaad078e7fd40d01_1.png)

---

![](img/e539e307b3d1bbdfaaad078e7fd40d01_3.png)

上一节我们介绍了数据清洗的重要性，本节中我们来看看如何使用百分位法处理数据中的极值。

我们的目标是：给定一组数据，计算其第一四分位数（Q1，25%分位点）和第三四分位数（Q3，75%分位点）。将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被规范到了一个由Q1和Q3定义的范围内。

![](img/e539e307b3d1bbdfaaad078e7fd40d01_5.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_1.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_7.png)

## 实现步骤

![](img/e539e307b3d1bbdfaaad078e7fd40d01_9.png)

### 1. 选择目标数据列
首先，我们需要指定要对哪一列数据进行操作。为了演示，我们随机选择一列。

![](img/e539e307b3d1bbdfaaad078e7fd40d01_3.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_11.png)

### 2. 编写核心处理函数
接下来，我们需要编写一个函数来完成基于百分位的极值处理。这个函数的核心逻辑是通用的，不仅可以处理25%和75%分位点，也可以处理其他任意指定的百分位。

![](img/e539e307b3d1bbdfaaad078e7fd40d01_13.png)

以下是该函数的实现思路：

![](img/e539e307b3d1bbdfaaad078e7fd40d01_15.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_5.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_17.png)

函数需要接收三个参数：
*   `series`: 一个Pandas Series对象，代表我们要处理的一列数据。
*   `min_percentile`: 希望保留的最小百分位，例如0.25。
*   `max_percentile`: 希望保留的最大百分位，例如0.75。

![](img/e539e307b3d1bbdfaaad078e7fd40d01_19.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_7.png)
![](img/e539e307b3d1bbdfaaad078e7fd40d01_9.png)

以下是函数的具体实现步骤：

**第一步：数据排序**
为了计算分位点，首先需要将数据按从小到大的顺序排列。

```python
sorted_series = series.sort_values()
```

![](img/e539e307b3d1bbdfaaad078e7fd40d01_13.png)
![](img/e539e307b3d1bbdfaaad078e7fd40d01_15.png)
![](img/e539e307b3d1bbdfaaad078e7fd40d01_17.png)

**第二步：计算分位点值**
使用Pandas的`quantile`函数，可以方便地计算出指定百分位对应的数值。

```python
Q = series.quantile([min_percentile, max_percentile])
min_value = Q.iloc[0]  # 获取最小值（如Q1）
max_value = Q.iloc[1]  # 获取最大值（如Q3）
```

![](img/e539e307b3d1bbdfaaad078e7fd40d01_19.png)

**第三步：规范数据范围**
使用NumPy的`clip`函数，将序列中所有小于`min_value`的值设置为`min_value`，将所有大于`max_value`的值设置为`max_value`。

```python
import numpy as np
clipped_series = np.clip(series, min_value, max_value)
```

![](img/e539e307b3d1bbdfaaad078e7fd40d01_21.png)

`np.clip`函数示例：对于序列 `[1, 3, 5, 7, 9, 11, 13]`，设置`min=3`, `max=11`，结果将变为 `[3, 3, 5, 7, 9, 11, 11]`。

![](img/e539e307b3d1bbdfaaad078e7fd40d01_21.png)

将以上步骤整合，完整的函数代码如下：

```python
def clip_by_percentile(series, min_percentile, max_percentile):
    # 计算分位点
    Q = series.quantile([min_percentile, max_percentile])
    min_val = Q.iloc[0]
    max_val = Q.iloc[1]
    # 裁剪数据
    clipped_series = np.clip(series, min_val, max_val)
    return clipped_series
```

![](img/e539e307b3d1bbdfaaad078e7fd40d01_23.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_23.png)
![](img/e539e307b3d1bbdfaaad078e7fd40d01_25.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_25.png)

### 3. 应用函数并可视化结果
函数编写完成后，我们可以将其应用到选定的数据列上。

![](img/e539e307b3d1bbdfaaad078e7fd40d01_27.png)

```python
# 假设我们的数据存储在DataFrame `data`中，目标列名为‘column_D’
processed_data = clip_by_percentile(data['column_D'], 0.25, 0.75)
```

![](img/e539e307b3d1bbdfaaad078e7fd40d01_27.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_29.png)

为了直观地对比处理前后的效果，我们可以将原始数据和处理后的数据绘制在同一张图上。

```python
import matplotlib.pyplot as plt

![](img/e539e307b3d1bbdfaaad078e7fd40d01_31.png)

plt.figure(figsize=(10, 6))
# 绘制原始数据（放在上层，用半透明显示差异）
plt.plot(data['column_D'].values, label='原始数据', alpha=0.7)
# 绘制处理后的数据
plt.plot(processed_data.values, label='去极值后数据')
plt.legend()
plt.title('基于百分位去极值效果对比')
plt.show()
```

![](img/e539e307b3d1bbdfaaad078e7fd40d01_33.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_29.png)
![](img/e539e307b3d1bbdfaaad078e7fd40d01_31.png)
![](img/e539e307b3d1bbdfaaad078e7fd40d01_33.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_35.png)

从生成的图表中，可以清晰地看到，处理后数据的上下边界被限制在了Q1和Q3的范围内，原本的极端值被“拉回”到了边界上。

![](img/e539e307b3d1bbdfaaad078e7fd40d01_35.png)
![](img/e539e307b3d1bbdfaaad078e7fd40d01_37.png)
![](img/e539e307b3d1bbdfaaad078e7fd40d01_39.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_37.png)

## 注意事项与实际应用

![](img/e539e307b3d1bbdfaaad078e7fd40d01_41.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_39.png)

以上我们完成了基于分位数去除极值的第一种方法演示。

![](img/e539e307b3d1bbdfaaad078e7fd40d01_41.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_43.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_43.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_45.png)

在实际的金融因子分析或数据挖掘中，有两点需要注意：

![](img/e539e307b3d1bbdfaaad078e7fd40d01_47.png)

1.  **分位点的选择**：本例中为了展示效果，使用了0.25和0.75这样较大的范围。在实际操作中，为了更严格地剔除异常值，常用的范围可能是**0.025和0.975**，甚至更窄。这意味着只将分布两端各2.5%的数据视为极值并进行处理。

    ![](img/e539e307b3d1bbdfaaad078e7fd40d01_45.png)
    ![](img/e539e307b3d1bbdfaaad078e7fd40d01_47.png)
    ![](img/e539e307b3d1bbdfaaad078e7fd40d01_49.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_49.png)

2.  **方法的灵活性**：本方法的核心优势在于其灵活性。通过修改`min_percentile`和`max_percentile`参数，可以轻松适应不同数据特性和分析需求，对极值进行不同严格程度的处理。

    ![](img/e539e307b3d1bbdfaaad078e7fd40d01_51.png)
    ![](img/e539e307b3d1bbdfaaad078e7fd40d01_53.png)
    ![](img/e539e307b3d1bbdfaaad078e7fd40d01_55.png)

![](img/e539e307b3d1bbdfaaad078e7fd40d01_51.png)

---

![](img/e539e307b3d1bbdfaaad078e7fd40d01_53.png)

## 总结
本节课中我们一起学习了基于百分位法去除数据极值的完整流程。我们掌握了如何编写一个通用的处理函数，该函数能够：
1.  接收数据序列和指定的百分位参数。
2.  计算对应的分位点数值（如Q1和Q3）。
3.  利用`np.clip`函数将数据规范到计算出的范围内。
4.  通过可视化对比处理前后的数据，直观验证处理效果。

![](img/e539e307b3d1bbdfaaad078e7fd40d01_55.png)

这是一种简单而强大的数据预处理技术，能有效提升后续建模分析的稳健性。理解其原理并掌握实现方法，是进行高质量金融量化分析的基础步骤之一。