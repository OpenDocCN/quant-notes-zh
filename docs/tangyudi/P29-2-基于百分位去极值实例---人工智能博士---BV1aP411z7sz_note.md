# 数据处理：P29：基于百分位去极值实例 📊

![](img/68010a80172fc1f2215f0535db075163_1.png)

在本节课中，我们将学习如何使用分位数（例如Q1和Q3）来识别和处理数据集中的极端值。我们将通过一个具体的例子，编写一个函数，将超出指定百分位范围的数据“截断”到边界值，从而实现数据的规范化处理。

![](img/68010a80172fc1f2215f0535db075163_3.png)

上一节我们介绍了数据清洗的基本概念，本节中我们来看看如何具体实现基于百分位的去极值操作。

![](img/68010a80172fc1f2215f0535db075163_5.png)

## 操作目标与思路 🎯

![](img/68010a80172fc1f2215f0535db075163_7.png)

我们的目标是：给定一个数据集，计算其第一四分位数（Q1，25%）和第三四分位数（Q3，75%）。将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被规范到了一个由Q1和Q3确定的范围内。

![](img/68010a80172fc1f2215f0535db075163_9.png)

以下是实现此目标的核心步骤：
1.  选择需要处理的特定数据列。
2.  编写一个函数，该函数能够接收一列数据以及指定的下限和上限百分位。
3.  在函数内部，计算对应的百分位数值。
4.  使用这些计算出的边界值来“截断”原始数据。
5.  最后，通过可视化对比处理前后的数据分布。

## 代码实现步骤 💻

![](img/68010a80172fc1f2215f0535db075163_11.png)

接下来，我们将分步实现上述思路。

![](img/68010a80172fc1f2215f0535db075163_13.png)

### 第一步：选择数据列

![](img/68010a80172fc1f2215f0535db075163_15.png)

首先，我们需要指定要对数据框中的哪一列进行操作。这只是一个示例，因此我们可以任意选择一列。

```python
column_name = ‘your_column_name‘  # 替换为实际列名
data_series = data[column_name]
```

### 第二步：编写去极值函数

我们将编写一个名为 `clip_by_percentile` 的函数。这个函数的核心逻辑是接收一个数据序列（Series）以及最小和最大百分位参数，然后返回处理后的序列。

以下是该函数的详细代码：

```python
import pandas as pd
import numpy as np

def clip_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位截断极值的函数。

    参数:
    series (pd.Series): 待处理的数据序列。
    low_percentile (float): 下限百分位，例如0.25代表Q1。
    high_percentile (float): 上限百分位，例如0.75代表Q3。

    返回:
    pd.Series: 处理后的数据序列。
    """
    # 1. 计算指定百分位对应的值
    # 使用 np.percentile 或 series.quantile 计算
    low_value = np.percentile(series, low_percentile * 100)
    high_value = np.percentile(series, high_percentile * 100)

    # 2. 使用 np.clip 函数进行截断
    # np.clip(数组, 最小值, 最大值) 会将数组中小于最小值的元素替换为最小值，大于最大值的替换为最大值。
    clipped_series = np.clip(series, low_value, high_value)

    # 将结果转换回Pandas Series，保持索引一致
    return pd.Series(clipped_series, index=series.index)
```

**函数逻辑解析：**
*   **计算边界值**：`np.percentile(series, 25)` 计算序列中25%位置的值（即Q1），`np.percentile(series, 75)` 计算Q3。
*   **截断操作**：`np.clip()` 函数是完成核心操作的关键。它接受三个参数：原始数据、最小允许值和最大允许值。函数会遍历原始数据，将所有低于最小值的数提升到最小值，将所有高于最大值的数降低到最大值，处于中间的值则保持不变。

![](img/68010a80172fc1f2215f0535db075163_17.png)

### 第三步：应用函数并查看结果

编写好函数后，我们可以将其应用到选定的数据列上。

```python
# 应用去极值函数，使用默认的25%和75%分位
clipped_data = clip_by_percentile(data_series)

![](img/68010a80172fc1f2215f0535db075163_19.png)

![](img/68010a80172fc1f2215f0535db075163_21.png)

# 查看处理前后的基本统计信息对比
print(“原始数据描述：”)
print(data_series.describe())
print(“\n处理后的数据描述：”)
print(clipped_data.describe())
```

![](img/68010a80172fc1f2215f0535db075163_23.png)

### 第四步：结果可视化

为了直观地感受去极值的效果，我们可以将处理前后的数据分布绘制出来进行对比。

![](img/68010a80172fc1f2215f0535db075163_25.png)

```python
import matplotlib.pyplot as plt

![](img/68010a80172fc1f2215f0535db075163_27.png)

# 创建画布和子图
fig, axes = plt.subplots(2, 1, figsize=(10, 8))

![](img/68010a80172fc1f2215f0535db075163_29.png)

# 绘制原始数据
axes[0].plot(data_series.values, ‘o‘, alpha=0.5, label=‘Original Data‘)
axes[0].axhline(y=np.percentile(data_series, 25), color=‘r‘, linestyle=‘--‘, label=‘Q1 (25%)‘)
axes[0].axhline(y=np.percentile(data_series, 75), color=‘g‘, linestyle=‘--‘, label=‘Q3 (75%)‘)
axes[0].set_title(‘原始数据分布‘)
axes[0].legend()
axes[0].grid(True)

# 绘制处理后的数据
axes[1].plot(clipped_data.values, ‘o‘, alpha=0.5, color=‘orange‘, label=‘Clipped Data‘)
axes[1].axhline(y=np.percentile(data_series, 25), color=‘r‘, linestyle=‘--‘, label=‘Q1 (25%)‘)
axes[1].axhline(y=np.percentile(data_series, 75), color=‘g‘, linestyle=‘--‘, label=‘Q3 (75%)‘)
axes[1].set_title(‘去极值后数据分布‘)
axes[1].legend()
axes[1].grid(True)

![](img/68010a80172fc1f2215f0535db075163_31.png)

plt.tight_layout()
plt.show()
```

![](img/68010a80172fc1f2215f0535db075163_33.png)

在生成的图表中，您将看到：
1.  第一张图显示原始数据点，并用虚线标出了Q1和Q3的位置。
2.  第二张图显示处理后的数据。可以观察到，所有原来低于红色虚线（Q1）的点现在都“抬升”到了红线上；所有原来高于绿色虚线（Q3）的点都“降低”到了绿线上。中间的数据点则保持不变。

![](img/68010a80172fc1f2215f0535db075163_35.png)

## 实际应用注意事项 ⚠️

![](img/68010a80172fc1f2215f0535db075163_37.png)

在理论示例中，我们使用了25%和75%作为边界，这会将相当一部分数据视为“极值”并进行调整，主要是为了演示效果更明显。

![](img/68010a80172fc1f2215f0535db075163_39.png)

![](img/68010a80172fc1f2215f0535db075163_41.png)

在实际数据分析或因子处理中，通常会使用更严格的边界，例如：
*   **下限**：`low_percentile=0.025` (2.5%)
*   **上限**：`high_percentile=0.975` (97.5%)

![](img/68010a80172fc1f2215f0535db075163_43.png)

这样只处理分布两端各2.5%的极端数据，对主体数据的影响更小，也更符合多数场景下去除异常值的需求。您可以通过向 `clip_by_percentile` 函数传递不同的参数来轻松调整这个范围。

![](img/68010a80172fc1f2215f0535db075163_45.png)

## 总结 📝

![](img/68010a80172fc1f2215f0535db075163_47.png)

![](img/68010a80172fc1f2215f0535db075163_49.png)

本节课中我们一起学习了基于百分位进行去极值处理的完整流程：
1.  **明确目标**：将数据限制在由指定分位数（如Q1和Q3）确定的范围内。
2.  **核心工具**：利用 `np.percentile` 计算边界值，利用 `np.clip` 函数高效地进行数据截断。
3.  **代码实现**：我们封装了一个可复用的函数 `clip_by_percentile`，并通过可视化对比验证了其效果。
4.  **灵活调整**：该方法允许通过修改百分位参数来适应不同的业务场景和严格程度。

![](img/68010a80172fc1f2215f0535db075163_51.png)

这种方法简单有效，是数据预处理中处理异常值的常用技术之一。