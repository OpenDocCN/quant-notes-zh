# Python金融量化分析：P28：基于百分位去极值实例 📊

![](img/7aa74b29db603bc7334d1199a5982b8f_1.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_3.png)

在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术——基于百分位法去除极值。我们将通过编写一个函数，将数据中超出指定百分位范围（例如，小于25%分位数或大于75%分位数）的值，分别替换为对应的分位数值，从而将数据规范到一个合理的区间内。

![](img/7aa74b29db603bc7334d1199a5982b8f_5.png)

---

![](img/7aa74b29db603bc7334d1199a5982b8f_7.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_9.png)

上一节我们介绍了数据清洗的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。

![](img/7aa74b29db603bc7334d1199a5982b8f_11.png)

首先，我们需要明确操作目标：对数据框（DataFrame）中的某一列数据，计算其第一四分位数（Q1，25%）和第三四分位数（Q3，75%）。然后，将所有小于Q1的值替换为Q1，将所有大于Q3的值替换为Q3。这样，数据就被“修剪”并规范到了Q1到Q3的范围内。

![](img/7aa74b29db603bc7334d1199a5982b8f_13.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_15.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_17.png)

以下是实现此功能的核心步骤：

![](img/7aa74b29db603bc7334d1199a5982b8f_19.png)

1.  **选择目标列**：从数据集中指定需要进行去极值处理的列。
2.  **编写处理函数**：创建一个函数，接收一个数据序列（Series）以及期望的最小和最大百分位（如下限`low_percent`和上限`up_percent`）。
3.  **计算分位点**：在函数内部，使用Pandas的`quantile`方法计算指定百分位对应的具体数值。
4.  **数据规范化**：利用NumPy的`clip`函数，将序列中所有小于下限分位数的值设置为下限分位数，将所有大于上限分位数的值设置为上限分位数。
5.  **应用与可视化**：将函数应用到选定的数据列上，并通过绘图对比处理前后的数据分布，以直观验证效果。

![](img/7aa74b29db603bc7334d1199a5982b8f_21.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_23.png)

接下来，我们通过代码来逐步实现上述逻辑。

首先，我们定义一个名为 `percentile_clip` 的函数。

![](img/7aa74b29db603bc7334d1199a5982b8f_25.png)

```python
import pandas as pd
import numpy as np

![](img/7aa74b29db603bc7334d1199a5982b8f_27.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_29.png)

def percentile_clip(series, low_percent=0.25, up_percent=0.75):
    """
    基于百分位法对数据进行去极值处理。
    参数:
        series: pandas Series, 待处理的数据列。
        low_percent: float, 下限百分位，默认0.25（Q1）。
        up_percent: float, 上限百分位，默认0.75（Q3）。
    返回:
        clipped_series: pandas Series, 处理后的数据。
    """
    # 步骤1: 计算指定百分位对应的值
    q = series.quantile([low_percent, up_percent])
    low_value = q.iloc[0]  # 获取下限值（如Q1）
    up_value = q.iloc[1]   # 获取上限值（如Q3）

    # 步骤2: 使用np.clip将数据规范到[low_value, up_value]区间
    clipped_series = np.clip(series, low_value, up_value)
    return clipped_series
```

**代码解释**:
*   `series.quantile([low_percent, up_percent])`：一次性计算多个分位数，返回一个Series。
*   `np.clip(series, a_min, a_max)`：这是核心函数。它将`series`中所有小于`a_min`的值设为`a_min`，所有大于`a_max`的值设为`a_max`，介于两者之间的值保持不变。

函数定义完成后，我们就可以将其应用到实际数据中。

![](img/7aa74b29db603bc7334d1199a5982b8f_31.png)

假设我们有一个名为`data`的DataFrame，其中包含一列名为`‘target_column‘`的数据。以下是应用函数并可视化结果的代码。

![](img/7aa74b29db603bc7334d1199a5982b8f_33.png)

```python
# 假设 data 是包含数据的DataFrame
column_name = ‘target_column‘  # 指定要处理的列名

![](img/7aa74b29db603bc7334d1199a5982b8f_35.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_37.png)

# 应用去极值函数
clipped_data = percentile_clip(data[column_name], low_percent=0.25, up_percent=0.75)

![](img/7aa74b29db603bc7334d1199a5982b8f_39.png)

# 可视化对比
import matplotlib.pyplot as plt

![](img/7aa74b29db603bc7334d1199a5982b8f_41.png)

plt.figure(figsize=(12, 5))

![](img/7aa74b29db603bc7334d1199a5982b8f_43.png)

# 绘制原始数据
plt.subplot(1, 2, 1)
data[column_name].plot(kind=‘hist‘, bins=50, alpha=0.7, color=‘blue‘, edgecolor=‘black‘)
plt.title(‘原始数据分布‘)
plt.xlabel(‘数值‘)
plt.ylabel(‘频数‘)

![](img/7aa74b29db603bc7334d1199a5982b8f_45.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_47.png)

# 绘制处理后的数据
plt.subplot(1, 2, 2)
clipped_data.plot(kind=‘hist‘, bins=50, alpha=0.7, color=‘green‘, edgecolor=‘black‘)
plt.title(‘去极值后数据分布‘)
plt.xlabel(‘数值‘)

![](img/7aa74b29db603bc7334d1199a5982b8f_49.png)

plt.tight_layout()
plt.show()
```

![](img/7aa74b29db603bc7334d1199a5982b8f_51.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_53.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_55.png)

通过对比左右两幅直方图，可以清晰地看到，处理后的数据（右图）两端的“尾巴”（即极值）被截断并集中到了Q1和Q3的位置，数据分布更加集中，消除了极端值的影响。

![](img/7aa74b29db603bc7334d1199a5982b8f_57.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_59.png)

---

![](img/7aa74b29db603bc7334d1199a5982b8f_61.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_63.png)

在实际的金融因子分析中，我们通常不会使用25%和75%这样宽泛的界限，因为这会将过多正常数据视为异常。更常见的做法是使用更严格的百分位，例如 **2.5%** 和 **97.5%**，或者根据因子的具体分布特性进行调整。本节课的例子为了展示更明显的效果，才选择了较大的范围。理解原理后，你只需修改`percentile_clip`函数中的`low_percent`和`up_percent`参数即可适应不同场景。

![](img/7aa74b29db603bc7334d1199a5982b8f_65.png)

![](img/7aa74b29db603bc7334d1199a5982b8f_67.png)

本节课中我们一起学习了基于百分位法去除数据极值的完整流程。我们掌握了如何编写一个通用的处理函数，利用`quantile`计算分位点，并使用`np.clip`进行数据裁剪。最后，通过可视化对比，直观地验证了该方法的有效性。这是金融数据预处理中非常实用且重要的一步，能为后续的量化分析模型提供更干净、更稳定的数据基础。