# Python金融分析量化交易：P51：4-因子打分与排序 📊

![](img/fd814c91c8f3319dbe7214a2cc205d6f_0.png)

![](img/fd814c91c8f3319dbe7214a2cc205d6f_2.png)

在本节课中，我们将学习如何对筛选出的股票因子进行打分与排序。这是构建多因子模型的关键步骤，通过为每个因子赋予合理的分数，我们可以综合评估每只股票的优劣。

![](img/fd814c91c8f3319dbe7214a2cc205d6f_4.png)

上一节我们介绍了如何根据因子特性（越高越好或越低越好）将数据分成两个独立的DataFrame。本节中，我们来看看如何遍历这些因子数据，为每只股票在每个因子上的表现进行排名和打分。

以下是实现因子打分与排序的核心步骤：

1.  **遍历因子列**：我们需要分别处理“越高越好”和“越低越好”两个DataFrame，遍历其中的每一个因子列。
2.  **对因子值进行排序**：对每个因子列的数据进行排序，明确股票的排名顺序。
3.  **为排名分配分数**：根据排序后的名次，为每只股票在该因子上的表现分配一个分数。通常，排名第一的股票获得最高分。
4.  **合并分数表**：将两个DataFrame的得分结果合并，为计算总分做准备。

---

### 步骤一：遍历因子与排序

首先，我们需要导入必要的库并开始遍历“越高越好”的因子DataFrame。

![](img/fd814c91c8f3319dbe7214a2cc205d6f_6.png)

```python
import pandas as pd
import numpy as np

# 假设 df_up 是“越高越好”因子的DataFrame，df_down 是“越低越好”因子的DataFrame
# 遍历“越高越好”的因子
for factor in df_up.columns.tolist():  # 获取所有列名并转换为列表以便遍历
    # 对当前因子列进行排序，inplace=True表示直接修改原DataFrame
    df_up.sort_values(by=factor, inplace=True)
```

![](img/fd814c91c8f3319dbe7214a2cc205d6f_8.png)

这段代码会按照每个因子列的值，对`df_up`中的股票进行升序排列（从小到大）。对于“越高越好”的因子，值小的股票排名靠前，但这并不是我们想要的最终评分顺序，我们将在下一步进行分数分配来修正。

![](img/fd814c91c8f3319dbe7214a2cc205d6f_10.png)

![](img/fd814c91c8f3319dbe7214a2cc205d6f_12.png)

---

![](img/fd814c91c8f3319dbe7214a2cc205d6f_14.png)

### 步骤二：为排名分配分数

![](img/fd814c91c8f3319dbe7214a2cc205d6f_16.png)

![](img/fd814c91c8f3319dbe7214a2cc205d6f_18.png)

排序完成后，我们需要根据排名分配分数。我们将使用NumPy的`linspace`函数来生成一个等差分数序列。

![](img/fd814c91c8f3319dbe7214a2cc205d6f_20.png)

`numpy.linspace`函数的公式为：
`np.linspace(start, stop, num)`
它会生成从`start`到`stop`（包含两端）的等间隔的`num`个数字。

![](img/fd814c91c8f3319dbe7214a2cc205d6f_22.png)

![](img/fd814c91c8f3319dbe7214a2cc205d6f_24.png)

对于“越高越好”的因子，我们希望排名第一（因子值最大）的股票获得最高分。由于我们之前是按升序排列的，排名最后的股票因子值最大。因此，我们可以生成一个从高到低的分数序列。

![](img/fd814c91c8f3319dbe7214a2cc205d6f_26.png)

```python
    # 为“越高越好”的因子分配分数
    # 股票数量
    num_stocks = len(df_up)
    # 生成一个从最高分到最低分的序列（例如，300, 299, ..., 1）
    scores = np.linspace(num_stocks, 1, num_stocks)
    # 将分数赋值给当前因子列
    df_up[factor] = scores
```

![](img/fd814c91c8f3319dbe7214a2cc205d6f_28.png)

![](img/fd814c91c8f3319dbe7214a2cc205d6f_30.png)

现在，我们对“越低越好”的因子DataFrame执行类似操作。区别在于，对于“越低越好”的因子，排序后排名第一的股票（因子值最小）应该获得最高分。

![](img/fd814c91c8f3319dbe7214a2cc205d6f_32.png)

```python
# 遍历“越低越好”的因子
for factor in df_down.columns.tolist():
    df_down.sort_values(by=factor, inplace=True)
    num_stocks = len(df_down)
    # 生成一个从最高分到最低分的序列。因为已经是升序排列，最小的排第一，所以直接赋高分。
    scores = np.linspace(num_stocks, 1, num_stocks)
    df_down[factor] = scores
```

![](img/fd814c91c8f3319dbe7214a2cc205d6f_34.png)

通过以上操作，`df_up`和`df_down`中的数值不再是原始的因子值，而是根据其在该因子上的排名转换后的得分。

---

### 步骤三：合并得分表

![](img/fd814c91c8f3319dbe7214a2cc205d6f_36.png)

两个DataFrame的因子都完成打分后，我们需要将它们合并，以便后续计算每只股票在所有因子上的总分。

![](img/fd814c91c8f3319dbe7214a2cc205d6f_38.png)

```python
# 合并两个得分DataFrame
# 假设两个DataFrame的索引（股票代码）是一致的
total_score_df = df_up + df_down
# 计算每个股票的总分
total_score_df['总分'] = total_score_df.sum(axis=1)
```

这里我们使用简单的加法进行合并，前提是两个DataFrame具有相同的形状和索引顺序。合并后，我们计算了每一行（即每只股票）所有因子得分的总和，得到该股票的最终综合得分。

---

![](img/fd814c91c8f3319dbe7214a2cc205d6f_40.png)

![](img/fd814c91c8f3319dbe7214a2cc205d6f_42.png)

本节课中我们一起学习了因子打分与排序的完整流程。我们首先遍历不同特性的因子并进行排序，然后利用`np.linspace`函数根据排名生成得分，最后将各因子的得分表合并并计算总分。这个过程将原始的因子数据转换成了统一的、可加总的分数，为下一步的股票筛选和策略构建奠定了基础。下一节，我们将基于这个总分来筛选出最终的优质股票组合。