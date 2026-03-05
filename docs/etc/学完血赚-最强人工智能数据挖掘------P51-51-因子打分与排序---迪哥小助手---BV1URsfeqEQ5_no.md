# 人工智能数据挖掘实战：P51：因子打分与排序 📊

![](img/a79b3230489fbf91dec464d9e1bc9d28_0.png)

在本节课中，我们将学习如何对计算出的因子进行排序和打分。这是构建多因子模型的关键步骤，我们将分别处理“越高越好”和“越低越好”两类因子，并为每只股票在每个因子上分配一个得分。

![](img/a79b3230489fbf91dec464d9e1bc9d28_2.png)

上一节我们介绍了如何计算并筛选出有效的因子，本节中我们来看看如何对这些因子进行量化的评分。

![](img/a79b3230489fbf91dec464d9e1bc9d28_3.png)

## 遍历因子与排序 🔄

![](img/a79b3230489fbf91dec464d9e1bc9d28_5.png)

我们得到了两个DataFrame，分别包含“越高越好”和“越低越好”的因子数据。首先需要遍历每个因子列，并对其进行排序。

以下是遍历“越高越好”因子并排序的步骤：

1.  获取DataFrame的列名列表，以便遍历每个因子。
2.  对每个因子列的数据进行排序。排序时，我们使用 `sort_values` 方法，并设置 `inplace=True` 参数，这样排序结果会直接更新到原始DataFrame中，而无需重新赋值。

![](img/a79b3230489fbf91dec464d9e1bc9d28_7.png)

```python
import pandas as pd
import numpy as np

# 假设 df_up 是“越高越好”因子的DataFrame
for factor in df_up.columns.tolist():
    df_up.sort_values(by=factor, inplace=True)
```

![](img/a79b3230489fbf91dec464d9e1bc9d28_9.png)

## 为排序结果分配得分 🥇

![](img/a79b3230489fbf91dec464d9e1bc9d28_11.png)

![](img/a79b3230489fbf91dec464d9e1bc9d28_12.png)

排序完成后，我们需要根据排名为每只股票分配得分。简单起见，我们采用线性打分法：对于有300只股票的数据，排名第一的得300分，排名第二的得299分，以此类推。

我们将使用NumPy的 `linspace` 函数来生成这个得分序列。`linspace` 用于在指定的区间内生成均匀间隔的数字。

![](img/a79b3230489fbf91dec464d9e1bc9d28_14.png)

![](img/a79b3230489fbf91dec464d9e1bc9d28_15.png)

以下是 `linspace` 函数的基本用法：

![](img/a79b3230489fbf91dec464d9e1bc9d28_16.png)

![](img/a79b3230489fbf91dec464d9e1bc9d28_17.png)

![](img/a79b3230489fbf91dec464d9e1bc9d28_18.png)

```python
# 语法：np.linspace(start, stop, num)
# 生成从1到300的300个均匀间隔的数字（即1,2,3,...,300）
score_series_asc = np.linspace(1, 300, 300)
# 生成从300到1的300个均匀间隔的数字（即300,299,298,...,1）
score_series_desc = np.linspace(300, 1, 300)
```

![](img/a79b3230489fbf91dec464d9e1bc9d28_19.png)

对于“越高越好”的因子，排序后因子值是从小到大的，但得分应该是排名越靠前（因子值越大）得分越高。因此，我们需要使用从高到低的得分序列（`score_series_desc`）。

![](img/a79b3230489fbf91dec464d9e1bc9d28_21.png)

![](img/a79b3230489fbf91dec464d9e1bc9d28_22.png)

以下是完整的打分代码：

![](img/a79b3230489fbf91dec464d9e1bc9d28_24.png)

![](img/a79b3230489fbf91dec464d9e1bc9d28_26.png)

```python
# 对“越高越好”的因子进行打分
for factor in df_up.columns.tolist():
    df_up.sort_values(by=factor, inplace=True)
    # 生成得分序列：排名第一得最高分（300分），最后一名得1分
    scores = np.linspace(300, 1, len(df_up))
    # 将当前因子列的值替换为计算出的得分
    df_up[factor] = scores

![](img/a79b3230489fbf91dec464d9e1bc9d28_28.png)

# 对“越低越好”的因子进行打分
for factor in df_down.columns.tolist():
    df_down.sort_values(by=factor, inplace=True)
    # 对于“越低越好”的因子，排序后最小的排第一，也应得最高分（300分）
    scores = np.linspace(300, 1, len(df_down))
    df_down[factor] = scores
```

![](img/a79b3230489fbf91dec464d9e1bc9d28_29.png)

## 合并数据与计算总分 ➕

完成所有因子的打分后，我们得到了两个新的DataFrame，其中存储的不再是原始因子值，而是各股票在每个因子上的得分。

接下来，我们需要将这两个DataFrame合并，并计算每只股票在所有因子上的总分，以进行最终的综合评价。

![](img/a79b3230489fbf91dec464d9e1bc9d28_31.png)

以下是合并与计算总分的步骤：

1.  将 `df_up` 和 `df_down` 按行索引（股票代码）进行合并。
2.  对合并后的DataFrame按行求和，得到每只股票的总得分。

```python
# 合并两个得分DataFrame
df_score_total = pd.concat([df_up, df_down], axis=1)
# 计算每只股票的总得分
total_score = df_score_total.sum(axis=1)
```

![](img/a79b3230489fbf91dec464d9e1bc9d28_33.png)

![](img/a79b3230489fbf91dec464d9e1bc9d28_34.png)

![](img/a79b3230489fbf91dec464d9e1bc9d28_35.png)

本节课中我们一起学习了因子分析中的打分与排序方法。我们掌握了如何遍历因子、根据排序分配线性得分，以及如何合并不同类别的因子得分并计算总分。这是将多个单一因子综合成统一评价指标的重要过程，为后续的选股策略奠定了基础。