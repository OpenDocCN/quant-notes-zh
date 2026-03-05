# 机器学习与量化交易：P51：因子打分与排序 📊

![](img/b7021fdc339ac4f80e8f817bb618f594_0.png)

在本节课中，我们将学习如何对选出的因子进行排序和打分。这是构建多因子模型的关键步骤，通过为每个股票在每个因子上的表现评分，我们可以综合评估股票的整体优劣。

![](img/b7021fdc339ac4f80e8f817bb618f594_2.png)

![](img/b7021fdc339ac4f80e8f817bb618f594_4.png)

上一节我们介绍了如何根据因子方向（越高越好或越低越好）筛选数据。本节中，我们来看看如何遍历这些数据，为每个因子下的股票排名并赋予相应的分数。

以下是实现因子打分与排序的核心步骤：

![](img/b7021fdc339ac4f80e8f817bb618f594_6.png)

1.  **遍历因子列**：我们需要分别处理“越高越好”和“越低越好”两个数据框，并遍历其中的每一个因子列。
2.  **对股票进行排序**：针对每个因子，根据其数值对股票进行排序。
3.  **为股票打分**：根据排序结果，为股票分配分数。通常，排名第一的股票获得最高分。
4.  **合并分数表**：将两个数据框的分数合并，为计算总分做准备。

---

### 遍历因子与数据排序

首先，我们需要遍历“越高越好”数据框中的每一个因子。每个因子的名称就是数据框的列名。

![](img/b7021fdc339ac4f80e8f817bb618f594_8.png)

```python
import pandas as pd
import numpy as np

# 假设 `up_df` 是“越高越好”因子的DataFrame， `down_df` 是“越低越好”因子的DataFrame
# 遍历“越高越好”的因子
for factor in up_df.columns:
    # 获取当前因子的数据列
    current_factor_data = up_df[[factor]]
    # 按照当前因子值进行降序排序（数值大的排前面）
    current_factor_data.sort_values(by=factor, ascending=False, inplace=True)
```

代码中的 `inplace=True` 参数非常重要，它表示直接在原数据上进行修改，而无需将排序结果重新赋值给变量。

![](img/b7021fdc339ac4f80e8f817bb618f594_10.png)

---

### 为排序后的股票打分

![](img/b7021fdc339ac4f80e8f817bb618f594_12.png)

![](img/b7021fdc339ac4f80e8f817bb618f594_14.png)

排序完成后，我们需要为股票打分。一个简单的方法是：如果有N只股票，则排名第1的得N分，排名第2的得N-1分，以此类推，排名最后的得1分。

![](img/b7021fdc339ac4f80e8f817bb618f594_16.png)

我们可以使用NumPy的 `linspace` 函数来快速生成这样一个分数序列。`linspace` 函数用于在指定的区间内生成等间隔的数值。

![](img/b7021fdc339ac4f80e8f817bb618f594_18.png)

![](img/b7021fdc339ac4f80e8f817bb618f594_20.png)

其基本公式为：
`np.linspace(start, stop, num)`
*   `start`：序列的起始值。
*   `stop`：序列的结束值。
*   `num`：要生成的样本数量。

![](img/b7021fdc339ac4f80e8f817bb618f594_22.png)

以下是 `linspace` 的用法示例：

![](img/b7021fdc339ac4f80e8f817bb618f594_24.png)

```python
# 生成从1到300的300个等间隔数字（即1,2,3,...,300）
ascending_scores = np.linspace(1, 300, 300)
print(ascending_scores[:10]) # 查看前10个值

![](img/b7021fdc339ac4f80e8f817bb618f594_26.png)

![](img/b7021fdc339ac4f80e8f817bb618f594_28.png)

# 生成从300到1的300个等间隔数字（即300,299,298,...,1）
descending_scores = np.linspace(300, 1, 300)
print(descending_scores[:10]) # 查看前10个值
```

![](img/b7021fdc339ac4f80e8f817bb618f594_30.png)

对于“越高越好”的因子，排序后排名靠前的股票应该获得高分，因此我们使用从高到低的分数序列（`descending_scores`）。对于“越低越好”的因子，排序后排名靠前的股票（即数值小的）也应该获得高分，同样使用从高到低的分数序列。

![](img/b7021fdc339ac4f80e8f817bb618f594_32.png)

现在，我们将打分步骤整合到循环中：

![](img/b7021fdc339ac4f80e8f817bb618f594_34.png)

![](img/b7021fdc339ac4f80e8f817bb618f594_36.png)

```python
# 为“越高越好”的因子打分
for factor in up_df.columns:
    up_df.sort_values(by=factor, ascending=False, inplace=True)
    # 生成从高到低的分数，并赋值给当前因子列
    up_df[factor] = np.linspace(len(up_df), 1, len(up_df))

# 为“越低越好”的因子打分
for factor in down_df.columns:
    down_df.sort_values(by=factor, ascending=True, inplace=True) # 注意这里是升序
    # 生成从高到低的分数，并赋值给当前因子列
    down_df[factor] = np.linspace(len(down_df), 1, len(down_df))
```

![](img/b7021fdc339ac4f80e8f817bb618f594_38.png)

注意，在给“越低越好”因子排序时，我们使用 `ascending=True`（升序），这样数值最小的股票会排在最前面，然后同样赋予其最高的分数。

---

### 合并分数表

![](img/b7021fdc339ac4f80e8f817bb618f594_40.png)

完成所有因子的打分后，我们得到了两个分数数据框：`up_df` 和 `down_df`。每个数据框的形状都是（股票数量 × 因子数量），其中的值不再是原始因子值，而是该股票在该因子上的得分。

![](img/b7021fdc339ac4f80e8f817bb618f594_42.png)

为了计算每只股票的总分，我们需要将这两个数据框合并。由于它们包含不同的因子，我们可以简单地将它们按列拼接起来。

```python
# 将两个分数DataFrame按列合并
all_scores_df = pd.concat([up_df, down_df], axis=1)
```

现在，`all_scores_df` 包含了所有股票在所有因子上的得分。在接下来的步骤中，我们可以对每只股票的所有因子得分进行加总或加权平均，得到最终的综合评分，并据此选择股票。

![](img/b7021fdc339ac4f80e8f817bb618f594_44.png)

---

![](img/b7021fdc339ac4f80e8f817bb618f594_46.png)

本节课中我们一起学习了因子评价流程中的打分与排序步骤。我们掌握了如何遍历因子、对股票进行排序、利用 `np.linspace` 函数进行打分，以及最后合并分数表的方法。这个过程将原始的因子数据转换成了统一的、可比较的分数，为后续构建多因子综合模型奠定了坚实的基础。下一节，我们将基于这个分数表计算股票的总分并进行最终筛选。