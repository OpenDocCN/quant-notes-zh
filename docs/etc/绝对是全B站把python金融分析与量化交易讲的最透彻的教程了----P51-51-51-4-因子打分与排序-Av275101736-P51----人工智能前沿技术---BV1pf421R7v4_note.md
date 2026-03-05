# Python金融量化+股票交易：P51：因子打分与排序

![](img/a2dc09933fe77ee623886ca41f8d2bf6_0.png)

![](img/a2dc09933fe77ee623886ca41f8d2bf6_2.png)

## 概述
在本节课中，我们将学习如何对股票因子进行排序和打分。这是构建多因子选股模型的关键步骤。我们将分别处理“越高越好”和“越低越好”两类因子，为每个股票在每个因子上分配一个得分，为后续的综合评分做准备。

![](img/a2dc09933fe77ee623886ca41f8d2bf6_4.png)

上一节我们介绍了如何筛选和分类因子，本节中我们来看看如何为这些因子进行排序和打分。

![](img/a2dc09933fe77ee623886ca41f8d2bf6_6.png)

## 因子排序与打分流程

### 1. 遍历因子并排序
首先，我们需要遍历“越高越好”和“越低越好”两个DataFrame中的每一个因子列。对于每个因子，我们根据其数值对股票进行排序。

以下是遍历“越高越好”因子并排序的步骤：

![](img/a2dc09933fe77ee623886ca41f8d2bf6_8.png)

```python
import numpy as np
import pandas as pd

![](img/a2dc09933fe77ee623886ca41f8d2bf6_10.png)

# 假设 `up_df` 是存储“越高越好”因子的DataFrame，形状为 300行 x N列
for factor in up_df.columns:
    # 对当前因子列进行排序，排序结果直接替换原DataFrame
    up_df.sort_values(by=factor, inplace=True)
```

代码解释：
*   `up_df.columns` 获取所有因子列的名称。
*   `sort_values(by=factor)` 按照名为 `factor` 的列进行排序。
*   `inplace=True` 参数使得排序操作直接修改原 `up_df`，无需重新赋值。

![](img/a2dc09933fe77ee623886ca41f8d2bf6_12.png)

![](img/a2dc09933fe77ee623886ca41f8d2bf6_14.png)

### 2. 为排序结果分配分数
排序完成后，我们需要为排序名次分配分数。通常，排名第一的股票获得最高分。我们将使用NumPy的 `linspace` 函数来生成一个等差分数序列。

![](img/a2dc09933fe77ee623886ca41f8d2bf6_16.png)

![](img/a2dc09933fe77ee623886ca41f8d2bf6_18.png)

`numpy.linspace` 函数用于创建等差数列，其核心公式为：
**`np.linspace(start, stop, num)`**
*   `start`：序列的起始值。
*   `stop`：序列的结束值。
*   `num`：要生成的样本数量。

![](img/a2dc09933fe77ee623886ca41f8d2bf6_20.png)

![](img/a2dc09933fe77ee623886ca41f8d2bf6_22.png)

![](img/a2dc09933fe77ee623886ca41f8d2bf6_24.png)

对于“越高越好”的因子，我们希望因子值最大的股票（排序后可能在最后一行）得分最高。因此，我们可以生成一个从1到300（假设有300支股票）的序列，并将其赋值给排序后的因子列。

![](img/a2dc09933fe77ee623886ca41f8d2bf6_26.png)

以下是生成分数并赋值的示例：

![](img/a2dc09933fe77ee623886ca41f8d2bf6_28.png)

![](img/a2dc09933fe77ee623886ca41f8d2bf6_30.png)

```python
# 假设股票总数为 stock_count
stock_count = len(up_df)

![](img/a2dc09933fe77ee623886ca41f8d2bf6_32.png)

![](img/a2dc09933fe77ee623886ca41f8d2bf6_34.png)

# 为“越高越好”的因子生成分数：第1名得1分，第300名得300分
scores = np.linspace(1, stock_count, stock_count)
# 将生成的分数序列赋值给当前排序后的因子列
up_df[factor] = scores
```

![](img/a2dc09933fe77ee623886ca41f8d2bf6_36.png)

![](img/a2dc09933fe77ee623886ca41f8d2bf6_38.png)

对于“越低越好”的因子，逻辑相反：因子值最小的股票应得最高分。因此，我们生成一个从300到1的递减序列。

以下是“越低越好”因子的打分代码：

```python
# 假设 `down_df` 是存储“越低越好”因子的DataFrame
for factor in down_df.columns:
    down_df.sort_values(by=factor, inplace=True)
    # 为“越低越好”的因子生成分数：第1名（值最小）得300分，第300名得1分
    scores = np.linspace(stock_count, 1, stock_count)
    down_df[factor] = scores
```

![](img/a2dc09933fe77ee623886ca41f8d2bf6_40.png)

### 3. 数据合并与后续步骤
完成所有因子的打分后，我们得到了两个新的DataFrame（`up_df` 和 `down_df`），其中原始的因子值已被替换为对应的排名分数。

![](img/a2dc09933fe77ee623886ca41f8d2bf6_42.png)

接下来，我们需要将这两个DataFrame合并，以便计算每支股票在所有因子上的综合得分。这可以通过`pandas`的合并操作（如 `concat`）来实现，我们将在下一节详细讲解。

## 总结
本节课中我们一起学习了因子打分与排序的核心操作。
1.  我们使用循环遍历每一个因子列。
2.  利用 `DataFrame.sort_values()` 方法对股票根据因子值进行排序。
3.  使用 `np.linspace()` 函数根据排序名次生成得分序列，并为“越高越好”和“越低越好”的因子设定了不同的得分方向。
4.  最终，我们将原始的因子值数据转换成了统一的得分数据，为下一步计算综合得分做好了准备。

![](img/a2dc09933fe77ee623886ca41f8d2bf6_44.png)

![](img/a2dc09933fe77ee623886ca41f8d2bf6_46.png)

这个过程标准化了不同量纲和方向的因子，使得它们可以相加和比较，是构建量化选股模型的基础。