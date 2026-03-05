# Python金融量化：51：因子打分与排序

![](img/cd716c48a432ddeca5cfb0bbb8436471_0.png)

![](img/cd716c48a432ddeca5cfb0bbb8436471_2.png)

在本节课中，我们将学习如何对金融因子进行打分与排序。这是构建多因子模型的关键步骤，通过为每个股票的因子表现分配分数，我们可以综合评估股票的综合排名。

![](img/cd716c48a432ddeca5cfb0bbb8436471_4.png)

上一节我们介绍了如何筛选和分类因子，本节中我们来看看如何为这些因子计算得分。

## 因子数据遍历与排序

![](img/cd716c48a432ddeca5cfb0bbb8436471_6.png)

首先，我们需要遍历每个因子数据框，并对每个因子进行排序。我们有两个数据框：一个包含“越高越好”的因子，另一个包含“越低越好”的因子。

以下是遍历和排序的步骤：

1.  遍历每个因子的列名。
2.  对每个因子的数值进行排序。
3.  使用 `inplace=True` 参数将排序结果直接保存在原始数据框中。

![](img/cd716c48a432ddeca5cfb0bbb8436471_8.png)

```python
import pandas as pd
import numpy as np

# 假设 df_up 是“越高越好”因子的数据框，df_down 是“越低越好”因子的数据框
# 遍历“越高越好”的因子
for factor in df_up.columns:
    df_up.sort_values(by=factor, inplace=True)

![](img/cd716c48a432ddeca5cfb0bbb8436471_10.png)

# 遍历“越低越好”的因子
for factor in df_down.columns:
    df_down.sort_values(by=factor, inplace=True)
```

![](img/cd716c48a432ddeca5cfb0bbb8436471_12.png)

## 为排序后的因子分配分数

![](img/cd716c48a432ddeca5cfb0bbb8436471_14.png)

![](img/cd716c48a432ddeca5cfb0bbb8436471_16.png)

排序完成后，我们需要根据排名为每个股票在该因子上的表现打分。简单起见，我们采用线性打分法：排名第一的得最高分，排名最后的得最低分。

![](img/cd716c48a432ddeca5cfb0bbb8436471_18.png)

我们将使用 `numpy.linspace` 函数来生成分数序列。该函数的公式为：
`numpy.linspace(start, stop, num)`
它会生成一个从 `start` 到 `stop` 的等差数列，共有 `num` 个元素。

![](img/cd716c48a432ddeca5cfb0bbb8436471_20.png)

![](img/cd716c48a432ddeca5cfb0bbb8436471_22.png)

以下是打分的具体操作：

![](img/cd716c48a432ddeca5cfb0bbb8436471_24.png)

![](img/cd716c48a432ddeca5cfb0bbb8436471_26.png)

1.  对于“越高越好”的因子，排名越高（数值越大）的股票得分越高。因此分数序列应从低到高（例如 1 到 300）。
2.  对于“越低越好”的因子，排名越高（数值越小）的股票得分越高。因此分数序列应从高到低（例如 300 到 1）。

![](img/cd716c48a432ddeca5cfb0bbb8436471_28.png)

```python
# 获取股票总数，假设为300
num_stocks = 300

![](img/cd716c48a432ddeca5cfb0bbb8436471_30.png)

![](img/cd716c48a432ddeca5cfb0bbb8436471_32.png)

# 为“越高越好”的因子打分
for factor in df_up.columns:
    # 生成从1到300的分数
    scores = np.linspace(1, num_stocks, num_stocks)
    # 将分数赋值给排序后的因子列
    df_up[factor] = scores

![](img/cd716c48a432ddeca5cfb0bbb8436471_34.png)

![](img/cd716c48a432ddeca5cfb0bbb8436471_36.png)

# 为“越低越好”的因子打分
for factor in df_down.columns:
    # 生成从300到1的分数
    scores = np.linspace(num_stocks, 1, num_stocks)
    # 将分数赋值给排序后的因子列
    df_down[factor] = scores
```

![](img/cd716c48a432ddeca5cfb0bbb8436471_38.png)

## 合并数据框并计算总分

为所有因子打完分后，我们得到了两个包含因子得分的数据框。接下来，需要将它们合并，并计算每个股票的总得分。

以下是合并与计算总分的步骤：

![](img/cd716c48a432ddeca5cfb0bbb8436471_40.png)

1.  将两个数据框按股票索引进行合并。
2.  对所有因子得分进行求和，得到每个股票的综合总分。

![](img/cd716c48a432ddeca5cfb0bbb8436471_42.png)

```python
# 合并两个得分数据框
# 假设两个数据框的索引都是股票代码，且顺序一致（经过排序）
total_score_df = df_up + df_down

# 计算每个股票的总分
total_score_df['综合总分'] = total_score_df.sum(axis=1)

# 按综合总分进行最终排序，选出得分最高的股票
final_ranking = total_score_df.sort_values(by='综合总分', ascending=False)
```

![](img/cd716c48a432ddeca5cfb0bbb8436471_44.png)

![](img/cd716c48a432ddeca5cfb0bbb8436471_46.png)

本节课中我们一起学习了因子打分与排序的完整流程。我们首先遍历并对因子进行排序，然后根据因子特性（越高越好或越低越好）使用 `numpy.linspace` 分配分数，最后合并所有因子得分并计算综合总分以进行股票排名。这是构建量化选股策略的基础步骤。