# Python金融分析与量化交易实战课程：P47：47.4-因子打分与排序 📊

![](img/454da1e5035da64ae5a51935f58cc8eb_0.png)

![](img/454da1e5035da64ae5a51935f58cc8eb_2.png)

在本节课中，我们将学习如何对选出的股票因子进行打分与排序。这是构建多因子模型的关键步骤，我们将分别处理“越高越好”和“越低越好”两类因子，并为它们分配合理的分数，为后续的综合评分做准备。

![](img/454da1e5035da64ae5a51935f58cc8eb_4.png)

上一节我们介绍了如何筛选和分类因子，本节中我们来看看如何为这些因子进行具体的打分。

## 遍历因子并排序

首先，我们需要遍历之前创建的两个DataFrame（分别代表“越高越好”和“越低越好”的因子），并对每个因子列进行排序。

以下是遍历和排序的核心步骤：

1.  **获取因子名称**：通过DataFrame的列名（`columns`属性）来获取所有需要处理的因子名称。
2.  **遍历每个因子**：使用循环结构，依次处理每个因子列。
3.  **执行排序**：使用Pandas的`sort_values`方法，按照当前因子的数值进行排序。通过设置参数`inplace=True`，排序结果将直接更新到原始DataFrame中，无需重新赋值。

```python
# 假设 df_up 是“越高越好”因子的DataFrame
for factor in df_up.columns:
    # 按照当前因子列进行排序，结果直接更新到df_up中
    df_up.sort_values(by=factor, inplace=True)
```

![](img/454da1e5035da64ae5a51935f58cc8eb_6.png)

## 为排序结果分配分数

![](img/454da1e5035da64ae5a51935f58cc8eb_8.png)

排序完成后，我们需要为排序名次分配具体的分数。一个简单直接的方法是：排名第一的股票获得最高分（例如300分，假设共有300支股票），排名最后的股票获得最低分（1分）。

![](img/454da1e5035da64ae5a51935f58cc8eb_10.png)

![](img/454da1e5035da64ae5a51935f58cc8eb_12.png)

我们将使用NumPy的`linspace`函数来快速生成这样一个等差数列作为分数。

![](img/454da1e5035da64ae5a51935f58cc8eb_14.png)

`linspace`函数的基本用法如下：
```python
import numpy as np
# 生成从 start 到 stop（包含）的 num 个等间隔数字
scores = np.linspace(start, stop, num)
```

![](img/454da1e5035da64ae5a51935f58cc8eb_16.png)

![](img/454da1e5035da64ae5a51935f58cc8eb_18.png)

以下是应用`linspace`进行打分的逻辑：

![](img/454da1e5035da64ae5a51935f58cc8eb_20.png)

*   **对于“越高越好”的因子**：因子值越大，排名越靠前，应获得越高分数。因此，分数序列应为从高到低（例如300, 299, ..., 1）。
*   **对于“越低越好”的因子**：因子值越小，排名越靠前，也应获得越高分数。因此，分数序列同样为从高到低。

![](img/454da1e5035da64ae5a51935f58cc8eb_22.png)

![](img/454da1e5035da64ae5a51935f58cc8eb_24.png)

```python
import numpy as np
import pandas as pd

![](img/454da1e5035da64ae5a51935f58cc8eb_26.png)

![](img/454da1e5035da64ae5a51935f58cc8eb_28.png)

# 假设股票总数为 stock_count
stock_count = len(df_up)

![](img/454da1e5035da64ae5a51935f58cc8eb_30.png)

![](img/454da1e5035da64ae5a51935f58cc8eb_32.png)

# 为“越高越好”的因子打分
for factor in df_up.columns:
    df_up.sort_values(by=factor, inplace=True)
    # 生成从高到低的分数序列：stock_count, stock_count-1, ..., 1
    df_up[factor] = np.linspace(stock_count, 1, stock_count)

![](img/454da1e5035da64ae5a51935f58cc8eb_34.png)

# 为“越低越好”的因子打分
for factor in df_down.columns:
    df_down.sort_values(by=factor, inplace=True)
    # 生成从高到低的分数序列：stock_count, stock_count-1, ..., 1
    df_down[factor] = np.linspace(stock_count, 1, stock_count)
```

## 合并数据并计算总分

完成两个DataFrame的因子打分后，我们得到了两个新的DataFrame，其中每个单元格的值不再是原始因子值，而是该因子下的得分。

![](img/454da1e5035da64ae5a51935f58cc8eb_36.png)

为了计算每支股票的综合得分，我们需要将这两个DataFrame合并。一个简单的方法是使用Pandas的`concat`函数，按列合并。

![](img/454da1e5035da64ae5a51935f58cc8eb_38.png)

```python
# 将两个DataFrame按列合并
total_score_df = pd.concat([df_up, df_down], axis=1)

# 计算每支股票的总分（假设所有因子权重相等，简单加和）
total_score_df['综合得分'] = total_score_df.sum(axis=1)

# 按照综合得分进行最终排序，选出得分最高的股票
final_ranking = total_score_df.sort_values(by='综合得分', ascending=False)
```

![](img/454da1e5035da64ae5a51935f58cc8eb_40.png)

![](img/454da1e5035da64ae5a51935f58cc8eb_42.png)

本节课中我们一起学习了因子打分与排序的完整流程。我们首先遍历并排序了“越高越好”和“越低越好”两类因子，然后利用`np.linspace`为排序结果分配了分数，最后通过合并数据与求和计算出了每支股票的综合得分，并得到了最终的股票排名。这为基于多因子的选股策略奠定了坚实的基础。