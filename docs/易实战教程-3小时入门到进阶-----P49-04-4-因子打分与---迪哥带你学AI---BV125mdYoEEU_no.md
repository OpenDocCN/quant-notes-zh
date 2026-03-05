# Python金融分析与量化交易实战教程：P49：04-4-因子打分与排序 📊

![](img/489d81e83a43d837298b761a8a062cfa_0.png)

![](img/489d81e83a43d837298b761a8a062cfa_2.png)

在本节课程中，我们将学习如何对筛选出的股票因子进行排序和打分。这是构建多因子模型的关键步骤，我们将遍历两个因子数据框（“越高越好”和“越低越好”），为每个因子内的股票进行排名并赋予相应的分数。

![](img/489d81e83a43d837298b761a8a062cfa_4.png)

上一节我们介绍了如何根据因子特性将数据分为“越高越好”和“越低越好”两组。本节中，我们来看看如何为这些因子进行具体的打分操作。

## 遍历因子并排序

以下是遍历“越高越好”因子数据框并进行排序的步骤。

首先，我们需要遍历数据框中的每一个因子列。因子名称存储在数据框的列名中。

```python
import pandas as pd
import numpy as np

# 假设 up_df 是“越高越好”的因子数据框
for factor in up_df.columns:
    # 获取当前因子的数据
    current_factor_data = up_df[[factor]]
    # 对当前因子列进行排序，从大到小
    current_factor_data.sort_values(by=factor, ascending=False, inplace=True)
```

![](img/489d81e83a43d837298b761a8a062cfa_6.png)

代码说明：
*   `up_df.columns` 获取所有因子列的名称。
*   `sort_values(by=factor, ascending=False, inplace=True)` 对数据框按指定列 `factor` 进行降序排序。`inplace=True` 参数确保排序结果直接修改原数据框，无需重新赋值。

![](img/489d81e83a43d837298b761a8a062cfa_8.png)

## 为排序结果打分

![](img/489d81e83a43d837298b761a8a062cfa_10.png)

排序完成后，我们需要为排名赋予分数。简单起见，我们采用线性打分法：排名第一的股票得分最高，排名最后的股票得分最低。

![](img/489d81e83a43d837298b761a8a062cfa_12.png)

![](img/489d81e83a43d837298b761a8a062cfa_14.png)

我们将使用NumPy的 `linspace` 函数来生成一个等差分数序列。

![](img/489d81e83a43d837298b761a8a062cfa_16.png)

![](img/489d81e83a43d837298b761a8a062cfa_18.png)

```python
# 查看 linspace 函数用法
# np.linspace(start, stop, num)
# start: 序列起始值
# stop: 序列结束值
# num: 生成的样本数量

![](img/489d81e83a43d837298b761a8a062cfa_20.png)

print(np.linspace(1, 300, 300)) # 生成从1到300的300个等差数字
print(np.linspace(300, 1, 300)) # 生成从300到1的300个等差数字
```

![](img/489d81e83a43d837298b761a8a062cfa_22.png)

![](img/489d81e83a43d837298b761a8a062cfa_24.png)

对于“越高越好”的因子，我们希望因子值最大的股票获得最高分（300分）。因此，我们使用 `np.linspace(300, 1, num_stocks)` 生成一个从300递减到1的分数序列，并将其赋值给排序后的数据。

![](img/489d81e83a43d837298b761a8a062cfa_26.png)

![](img/489d81e83a43d837298b761a8a062cfa_28.png)

以下是完整的打分代码：

![](img/489d81e83a43d837298b761a8a062cfa_30.png)

```python
# 对“越高越好”的因子进行打分
for factor in up_df.columns:
    # 排序
    up_df.sort_values(by=factor, ascending=False, inplace=True)
    # 生成分数序列：股票数量为 len(up_df)
    scores = np.linspace(300, 1, len(up_df))
    # 将分数赋值给当前因子列（替换原来的因子值）
    up_df[factor] = scores

![](img/489d81e83a43d837298b761a8a062cfa_32.png)

![](img/489d81e83a43d837298b761a8a062cfa_34.png)

# 对“越低越好”的因子进行打分
for factor in down_df.columns:
    # 排序（注意：越低越好，所以升序排列）
    down_df.sort_values(by=factor, ascending=True, inplace=True)
    # 生成分数序列：因子值最小的股票应得最高分（300分）
    scores = np.linspace(300, 1, len(down_df))
    # 将分数赋值给当前因子列
    down_df[factor] = scores
```

代码说明：
*   对于“越高越好”的因子 (`up_df`)，我们按降序排列，并使用 `np.linspace(300, 1, ...)` 生成分数，确保排名第一（因子值最大）的股票得到300分。
*   对于“越低越好”的因子 (`down_df`)，我们按升序排列，同样使用 `np.linspace(300, 1, ...)` 生成分数，确保排名第一（因子值最小）的股票得到300分。
*   `len(up_df)` 和 `len(down_df)` 用于动态获取股票数量，使代码更通用。

## 合并分数并计算总分

![](img/489d81e83a43d837298b761a8a062cfa_36.png)

完成所有因子的独立打分后，我们得到了两个新的数据框 `up_df` 和 `down_df`，其中存储的不再是原始因子值，而是各因子下的股票得分。

![](img/489d81e83a43d837298b761a8a062cfa_38.png)

为了计算每只股票的综合得分，我们需要将这两个数据框合并。

```python
# 将两个打分后的数据框按行索引对齐合并
total_score_df = up_df.add(down_df, fill_value=0)
# 计算每只股票的总分（对所有因子得分求和）
total_score_series = total_score_df.sum(axis=1)
# 按总分从高到低排序，得到最终的股票排名
final_ranking = total_score_series.sort_values(ascending=False)
```

代码说明：
*   `add(fill_value=0)` 方法将两个数据框对应位置的分数相加。`fill_value=0` 确保如果某个因子在其中一个数据框中不存在，则其分数按0处理。
*   `sum(axis=1)` 对每只股票（每一行）的所有因子分数进行求和，得到该股票的总分。
*   `sort_values(ascending=False)` 对总分进行降序排序，总分最高的股票排名最靠前，这是我们最终希望选出的股票组合。

![](img/489d81e83a43d837298b761a8a062cfa_40.png)

![](img/489d81e83a43d837298b761a8a062cfa_42.png)

本节课中我们一起学习了因子打分与排序的全过程。我们首先遍历了“越高越好”和“越低越好”两组因子，分别进行排序。接着，我们利用 `np.linspace` 函数为排序后的股票生成线性分数。最后，我们将所有因子的分数合并，计算每只股票的总分并排序，从而得到了基于多因子模型的最终股票排名。这是量化选股策略中构建股票池的核心步骤。