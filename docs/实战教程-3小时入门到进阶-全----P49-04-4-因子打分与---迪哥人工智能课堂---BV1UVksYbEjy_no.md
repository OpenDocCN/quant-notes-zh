# Python金融分析与量化交易实战教程：P49：04-4-因子打分与排序 📊

![](img/f1bdd074f52f1a1e60c269a29eb16255_0.png)

![](img/f1bdd074f52f1a1e60c269a29eb16255_2.png)

在本节课中，我们将学习如何对选出的股票因子进行排序和打分。这是构建多因子模型的关键步骤，我们将分别处理“越高越好”和“越低越好”两类因子，并为它们分配合理的分数。

![](img/f1bdd074f52f1a1e60c269a29eb16255_4.png)

上一节我们介绍了如何筛选和分类因子，本节中我们来看看如何为这些因子进行排序和打分。

## 遍历因子数据框

首先，我们需要遍历我们创建的两个数据框（`up` 和 `down`），它们分别包含了“越高越好”和“越低越好”的因子。以下是遍历数据框并对每个因子进行排序和打分的步骤：

1.  **获取因子列名**：我们需要获取数据框的列名，以便遍历每一个因子。
2.  **对因子值进行排序**：对每个因子的数值进行排序，为后续打分做准备。
3.  **为排序后的因子分配分数**：根据排序结果，为每个股票在该因子上的表现分配一个分数。

## 实现排序与打分

以下是具体的代码实现过程。我们将使用 `pandas` 进行数据处理，使用 `numpy` 生成分数序列。

```python
import pandas as pd
import numpy as np

![](img/f1bdd074f52f1a1e60c269a29eb16255_6.png)

# 假设我们已经有了两个DataFrame: df_up (越高越好) 和 df_down (越低越好)
# df_up 和 df_down 的形状都是 (300, 3)，代表300支股票，3个因子

# 首先处理“越高越好”的因子
for factor in df_up.columns.tolist():  # 遍历每一个因子列
    # 1. 按当前因子值进行降序排序（值越大排名越靠前）
    df_up.sort_values(by=factor, ascending=False, inplace=True)
    # 2. 生成分数：第一名300分，第二名299分，...，最后一名1分
    scores = np.linspace(300, 1, num=len(df_up))
    # 3. 将生成的分数赋值给该因子列（替换原来的因子值）
    df_up[factor] = scores

![](img/f1bdd074f52f1a1e60c269a29eb16255_8.png)

![](img/f1bdd074f52f1a1e60c269a29eb16255_10.png)

# 接着处理“越低越好”的因子
for factor in df_down.columns.tolist():
    # 1. 按当前因子值进行升序排序（值越小排名越靠前）
    df_down.sort_values(by=factor, ascending=True, inplace=True)
    # 2. 生成分数：第一名300分，第二名299分，...，最后一名1分
    scores = np.linspace(300, 1, num=len(df_down))
    # 3. 将生成的分数赋值给该因子列
    df_down[factor] = scores
```

![](img/f1bdd074f52f1a1e60c269a29eb16255_12.png)

![](img/f1bdd074f52f1a1e60c269a29eb16255_14.png)

**代码解释**：
*   `df.columns.tolist()`: 将数据框的列名转换为列表，便于遍历。
*   `df.sort_values(by=column, inplace=True)`: 按指定列排序，`inplace=True` 表示直接在原数据框上修改，无需重新赋值。
*   `np.linspace(start, stop, num)`: 这是生成等差数列的函数。对于“越高越好”的因子，我们希望第一名（最大值）得最高分，所以从300开始到1结束。对于“越低越好”的因子，排序后第一名是最小值，同样应得最高分，因此分数序列也是从300到1。

![](img/f1bdd074f52f1a1e60c269a29eb16255_16.png)

## 关于 `np.linspace` 函数

![](img/f1bdd074f52f1a1e60c269a29eb16255_18.png)

![](img/f1bdd074f52f1a1e60c269a29eb16255_20.png)

`np.linspace` 函数是生成打分序列的核心工具。它的基本公式是：

![](img/f1bdd074f52f1a1e60c269a29eb16255_22.png)

**`np.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None)`**

![](img/f1bdd074f52f1a1e60c269a29eb16255_24.png)

以下是关键参数说明：
*   `start`: 序列的起始值。
*   `stop`: 序列的结束值。
*   `num`: 要生成的样本数量。

![](img/f1bdd074f52f1a1e60c269a29eb16255_26.png)

![](img/f1bdd074f52f1a1e60c269a29eb16255_28.png)

例如：
*   `np.linspace(1, 300, 300)` 会生成一个从1到300的、包含300个数的等差数列：`[1, 2, 3, ..., 300]`。
*   `np.linspace(300, 1, 300)` 会生成一个从300到1的等差数列：`[300, 299, 298, ..., 1]`。

![](img/f1bdd074f52f1a1e60c269a29eb16255_30.png)

我们正是利用这个特性，为排序后的股票分配从高到低的分数。

![](img/f1bdd074f52f1a1e60c269a29eb16255_32.png)

## 合并数据框并计算总分

![](img/f1bdd074f52f1a1e60c269a29eb16255_34.png)

完成所有因子的打分后，我们得到了两个新的数据框 `df_up` 和 `df_down`，其中原始的因子值已被替换为分数。接下来，我们需要将它们合并，并计算每支股票的总分。

以下是合并与计算总分的步骤：

1.  **合并数据框**：将 `df_up` 和 `df_down` 按股票索引合并。
2.  **计算总分**：对每个股票的所有因子分数进行求和，得到该股票的综合得分。

![](img/f1bdd074f52f1a1e60c269a29eb16255_36.png)

```python
# 将两个打分后的数据框合并
# 假设它们的索引都是股票代码，并且因子列名不重复
df_total_score = pd.concat([df_up, df_down], axis=1)

![](img/f1bdd074f52f1a1e60c269a29eb16255_38.png)

# 计算每支股票的总分（对所有因子分数求和）
df_total_score['总分'] = df_total_score.sum(axis=1)

# 按总分进行降序排序，得到最终的股票排名
df_final_ranking = df_total_score.sort_values(by='总分', ascending=False)
```

**代码解释**：
*   `pd.concat([df1, df2], axis=1)`: 沿列方向（`axis=1`）合并数据框，要求行索引对齐。
*   `df.sum(axis=1)`: 对每一行（即每支股票）的所有列（即所有因子分数）进行求和，得到总分。
*   `df.sort_values(by=‘总分’)`: 根据总分列对股票进行最终排名。

![](img/f1bdd074f52f1a1e60c269a29eb16255_40.png)

![](img/f1bdd074f52f1a1e60c269a29eb16255_42.png)

本节课中我们一起学习了量化交易中因子打分与排序的核心流程。我们首先遍历了“越高越好”和“越低越好”两类因子，分别进行排序并利用 `np.linspace` 函数分配分数。然后，我们将所有因子的分数合并，并计算每支股票的总分以得到综合排名。这个过程是多因子选股模型的基础，打分的合理性能直接影响最终选股的效果。