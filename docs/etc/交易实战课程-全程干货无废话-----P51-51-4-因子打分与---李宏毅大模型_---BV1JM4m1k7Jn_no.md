# Python金融分析与量化交易实战：P51：51.4-因子打分与排序 📊

![](img/fc8b61b2fe62a2e202aba939f368eae6_0.png)

![](img/fc8b61b2fe62a2e202aba939f368eae6_2.png)

在本节课程中，我们将学习如何对选出的股票因子进行排序和打分。这是构建多因子模型的关键步骤，我们将分别处理“越高越好”和“越低越好”两类因子，并为它们分配合理的分数。

![](img/fc8b61b2fe62a2e202aba939f368eae6_4.png)

上一节我们完成了因子的筛选与分组，本节中我们来看看如何为这些因子进行量化打分。

![](img/fc8b61b2fe62a2e202aba939f368eae6_6.png)

## 遍历因子与数据排序

首先，我们需要遍历两个DataFrame（分别包含“越高越好”和“越低越好”的因子），并对每个因子列的数据进行排序。

以下是核心操作步骤：

![](img/fc8b61b2fe62a2e202aba939f368eae6_8.png)

1.  **导入必要的库**：我们需要使用`pandas`进行数据处理，`numpy`进行数值计算。
    ```python
    import pandas as pd
    import numpy as np
    ```

2.  **遍历因子列**：获取每个DataFrame的列名（即因子名称），然后遍历每一列。
    ```python
    # 假设 `df_up` 是“越高越好”因子的DataFrame
    for factor in df_up.columns:
        # 对当前因子列进行排序
        df_up.sort_values(by=factor, inplace=True)
    ```
    *   `inplace=True`参数确保排序结果直接修改原DataFrame，无需重新赋值。

![](img/fc8b61b2fe62a2e202aba939f368eae6_10.png)

## 为排序后的因子分配分数

![](img/fc8b61b2fe62a2e202aba939f368eae6_12.png)

排序完成后，我们需要根据排名为每只股票在该因子上的表现打分。简单起见，我们采用线性打分法：排名第一的得最高分，排名最后的得最低分。

![](img/fc8b61b2fe62a2e202aba939f368eae6_14.png)

![](img/fc8b61b2fe62a2e202aba939f368eae6_16.png)

![](img/fc8b61b2fe62a2e202aba939f368eae6_18.png)

我们将使用`numpy.linspace`函数来生成一个等差分数序列。这个函数非常实用：

![](img/fc8b61b2fe62a2e202aba939f368eae6_20.png)

![](img/fc8b61b2fe62a2e202aba939f368eae6_22.png)

```python
# numpy.linspace 函数基本用法
# 参数: start(起始值), stop(结束值), num(生成的样本数)
scores = np.linspace(start, stop, num)
```

![](img/fc8b61b2fe62a2e202aba939f368eae6_24.png)

*   对于**“越高越好”**的因子，因子值最大的股票应得最高分。因此，我们从低分到高分生成序列（例如，1分到300分）。
*   对于**“越低越好”**的因子，因子值最小的股票应得最高分。因此，我们需要从高分到低分生成序列（例如，300分到1分）。

![](img/fc8b61b2fe62a2e202aba939f368eae6_26.png)

![](img/fc8b61b2fe62a2e202aba939f368eae6_28.png)

以下是具体的打分代码实现：

![](img/fc8b61b2fe62a2e202aba939f368eae6_30.png)

![](img/fc8b61b2fe62a2e202aba939f368eae6_32.png)

```python
# 1. 为“越高越好”的因子打分
for factor in df_up.columns:
    df_up.sort_values(by=factor, inplace=True)
    # 生成从1到股票总数（例如300）的分数序列
    df_up[factor] = np.linspace(1, len(df_up), len(df_up))

![](img/fc8b61b2fe62a2e202aba939f368eae6_34.png)

![](img/fc8b61b2fe62a2e202aba939f368eae6_36.png)

# 2. 为“越低越好”的因子打分
for factor in df_down.columns:
    df_down.sort_values(by=factor, inplace=True)
    # 生成从股票总数（例如300）到1的分数序列
    df_down[factor] = np.linspace(len(df_down), 1, len(df_down))
```

![](img/fc8b61b2fe62a2e202aba939f368eae6_38.png)

通过以上操作，`df_up`和`df_down`中的原始因子值已被替换为对应的得分。得分越高，代表该股票在该因子上的表现越优秀。

## 合并数据表与计算总分

现在，我们得到了两个包含各因子得分的DataFrame。为了计算每只股票的综合得分，我们需要将这两个表合并。

![](img/fc8b61b2fe62a2e202aba939f368eae6_40.png)

```python
# 将两个DataFrame按索引（股票代码）合并
total_score_df = df_up.add(df_down, fill_value=0)
# 计算每个股票的总分（对所有因子得分求和）
total_score_df[‘总分‘] = total_score_df.sum(axis=1)
```

![](img/fc8b61b2fe62a2e202aba939f368eae6_42.png)

*   `.add()`方法将两个DataFrame对应位置的数值相加，`fill_value=0`确保如果某个因子只存在于一个表中，则其缺失值按0处理。
*   `.sum(axis=1)`对每一行（即每只股票）的所有因子得分进行求和，得到该股票的总分。

---

![](img/fc8b61b2fe62a2e202aba939f368eae6_44.png)

![](img/fc8b61b2fe62a2e202aba939f368eae6_46.png)

本节课中我们一起学习了因子打分与排序的核心流程。我们首先使用`sort_values`对因子数据进行排序，然后利用`numpy.linspace`根据排序结果为每只股票分配分数，并区分了“越高越好”和“越低越好”两类因子的打分逻辑。最后，通过合并数据表并求和，我们得到了每只股票的综合得分，为下一步的股票筛选和投资组合构建奠定了基础。