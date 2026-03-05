# 量化交易教程：P51：因子打分与排序

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_0.png)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_2.png)

在本节课中，我们将学习如何对选定的因子进行排序和打分，这是构建多因子模型的关键步骤。我们将分别处理“越高越好”和“越低越好”两类因子，为每个股票在每个因子上分配一个得分，为后续的综合评分做准备。

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_4.png)

上一节我们介绍了如何筛选和分类因子，本节中我们来看看如何对这些因子进行具体的打分操作。

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_6.png)

## 遍历因子并排序

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_8.png)

首先，我们需要遍历两个DataFrame（分别对应“越高越好”和“越低越好”的因子），并对每个因子列进行排序。

以下是遍历“越高越好”因子DataFrame并进行排序的步骤：

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_10.png)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_12.png)

1.  获取DataFrame的列名列表，以遍历每一个因子。
2.  对每个因子列的数据进行排序。对于“越高越好”的因子，数值越大排名应越靠前。
3.  使用 `sort_values` 方法进行排序，并通过 `inplace=True` 参数直接在原数据上修改。

```python
# 假设 ‘up_df‘ 是包含“越高越好”因子的DataFrame
for factor in up_df.columns:
    # 按照当前因子列进行降序排序（数值大的在前）
    up_df.sort_values(by=factor, ascending=False, inplace=True)
```

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_14.png)

## 为排序结果分配得分

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_16.png)

排序完成后，我们需要根据排名为每个股票分配得分。一个简单的方法是：排名第一的得最高分，排名最后的得最低分。

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_18.png)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_20.png)

我们将使用NumPy的 `linspace` 函数来生成一个等差分数序列。该函数的基本公式为：
`numpy.linspace(start, stop, num)`
它会生成从 `start` 到 `stop` 的等间隔的 `num` 个数字。

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_22.png)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_24.png)

以下是使用 `linspace` 进行打分的逻辑：

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_26.png)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_28.png)

1.  对于“越高越好”的因子，排序后第一名应得最高分。因此，我们生成一个从最高分到最低分（例如300到1）的序列。
2.  对于“越低越好”的因子，排序后第一名（即数值最小的）也应得最高分。因此，我们生成一个从最高分到最低分（同样是300到1）的序列，但排序方向与前者相反。

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_30.png)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_32.png)

```python
import numpy as np
import pandas as pd

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_34.png)

# 假设股票总数为 stock_count
stock_count = len(up_df)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_36.png)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_38.png)

# 为“越高越好”的因子打分
for factor in up_df.columns:
    up_df.sort_values(by=factor, ascending=False, inplace=True)
    # 生成从stock_count到1的分数序列，分配给排序后的数据
    up_df[factor] = np.linspace(stock_count, 1, stock_count)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_40.png)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_42.png)

# 为“越低越好”的因子打分
for factor in down_df.columns:
    down_df.sort_values(by=factor, ascending=True, inplace=True) # 数值小的在前
    # 同样生成从stock_count到1的分数序列
    down_df[factor] = np.linspace(stock_count, 1, stock_count)
```

**代码说明**：通过 `np.linspace(stock_count, 1, stock_count)`，我们创建了一个长度为 `stock_count` 的数组，其值从 `stock_count`（最高分）线性递减到 `1`（最低分）。将这个数组赋值给排序后的因子列，就完成了打分。

## 合并数据并计算总分

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_44.png)

完成所有因子的独立打分后，我们得到了两个新的DataFrame（`up_df` 和 `down_df`），其中存储的不再是原始因子值，而是各因子上的得分。

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_46.png)

为了计算每个股票的综合得分，我们需要将这两个DataFrame合并。一个简单的方法是直接按索引对齐并相加。

```python
# 将两个得分DataFrame相加，得到每个股票的总分
total_score_df = up_df + down_df
# 计算每个股票的总得分（各因子得分之和）
total_score_df[‘total_score‘] = total_score_df.sum(axis=1)
```

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_48.png)

![](img/6bdd28575a2f4cb8af5c9f232d6f7d8b_50.png)

本节课中我们一起学习了因子评价中的打分与排序流程。我们首先对“越高越好”和“越低越好”两类因子分别进行排序，然后利用 `np.linspace` 函数根据排名分配得分，最后将两类因子的得分合并，计算出每个股票的初步综合得分。这是构建多因子选股模型的基础步骤。