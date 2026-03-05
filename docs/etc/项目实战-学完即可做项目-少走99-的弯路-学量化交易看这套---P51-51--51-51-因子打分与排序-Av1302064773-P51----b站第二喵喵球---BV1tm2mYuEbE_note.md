# 量化交易实战课程：P51：因子打分与排序

![](img/b8f5bfd906269d876b3a819f9716f131_0.png)

![](img/b8f5bfd906269d876b3a819f9716f131_2.png)

在本节课中，我们将学习如何对筛选出的因子进行排序和打分。这是构建多因子模型的关键步骤，我们将分别处理“越高越好”和“越低越好”两类因子，并为每个股票在每个因子上分配一个得分。

![](img/b8f5bfd906269d876b3a819f9716f131_4.png)

上一节我们介绍了如何根据因子特性（越高越好或越低越好）将数据分成两个独立的DataFrame。本节中，我们来看看如何遍历这些因子，进行排序并计算得分。

![](img/b8f5bfd906269d876b3a819f9716f131_6.png)

以下是完成因子打分与排序的核心步骤。

## 遍历因子并排序

![](img/b8f5bfd906269d876b3a819f9716f131_8.png)

首先，我们需要遍历每个DataFrame中的因子列。对于“越高越好”的因子，我们按降序排列（因子值大的股票排名高）；对于“越低越好”的因子，我们按升序排列（因子值小的股票排名高）。

![](img/b8f5bfd906269d876b3a819f9716f131_10.png)

```python
# 假设我们已经有了两个DataFrame: df_up (越高越好) 和 df_down (越低越好)
import pandas as pd
import numpy as np

# 遍历“越高越好”的因子DataFrame
for factor_name in df_up.columns:
    # 对当前因子列进行降序排序（inplace=True表示直接修改原DataFrame）
    df_up.sort_values(by=factor_name, ascending=False, inplace=True)
```

![](img/b8f5bfd906269d876b3a819f9716f131_12.png)

## 为排序后的因子分配得分

![](img/b8f5bfd906269d876b3a819f9716f131_14.png)

排序完成后，我们需要为每个股票在当前因子上的表现打分。一个简单的方法是：排名第一的股票获得最高分（例如300分），排名最后的股票获得最低分（例如1分）。我们可以使用NumPy的`linspace`函数来生成一个等差数列作为得分。

![](img/b8f5bfd906269d876b3a819f9716f131_16.png)

![](img/b8f5bfd906269d876b3a819f9716f131_18.png)

`numpy.linspace`函数的公式如下：
**`numpy.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None, axis=0)`**
其中，`start`是序列起始值，`stop`是序列结束值，`num`是要生成的样本数。

![](img/b8f5bfd906269d876b3a819f9716f131_20.png)

![](img/b8f5bfd906269d876b3a819f9716f131_22.png)

以下是具体应用：

![](img/b8f5bfd906269d876b3a819f9716f131_24.png)

![](img/b8f5bfd906269d876b3a819f9716f131_26.png)

```python
# 计算股票总数
num_stocks = len(df_up)

![](img/b8f5bfd906269d876b3a819f9716f131_28.png)

![](img/b8f5bfd906269d876b3a819f9716f131_30.png)

# 为“越高越好”的因子分配得分：排名第一得最高分（num_stocks），最后一名得1分
# 因此得分序列为：num_stocks, num_stocks-1, ..., 1
scores_up = np.linspace(num_stocks, 1, num=num_stocks)

![](img/b8f5bfd906269d876b3a819f9716f131_32.png)

# 将计算出的得分赋值给当前因子列
df_up[factor_name] = scores_up
```

![](img/b8f5bfd906269d876b3a819f9716f131_34.png)

![](img/b8f5bfd906269d876b3a819f9716f131_36.png)

![](img/b8f5bfd906269d876b3a819f9716f131_38.png)

对于“越低越好”的因子，逻辑是相反的：因子值最小的股票应该获得最高分。因此，我们生成一个从最高分到最低分的序列。

![](img/b8f5bfd906269d876b3a819f9716f131_40.png)

```python
# 遍历“越低越好”的因子DataFrame
for factor_name in df_down.columns:
    # 对当前因子列进行升序排序（因子值小的在前）
    df_down.sort_values(by=factor_name, ascending=True, inplace=True)
    
    # 为“越低越好”的因子分配得分：排名第一（值最小）得最高分（num_stocks）
    # 得分序列同样为：num_stocks, num_stocks-1, ..., 1
    scores_down = np.linspace(num_stocks, 1, num=num_stocks)
    df_down[factor_name] = scores_down
```

## 合并数据并计算总分

完成所有因子的打分后，我们得到了两个新的DataFrame（`df_up`和`df_down`），其中每个单元格的值不再是原始因子值，而是该因子上的得分。为了计算每个股票的综合得分，我们需要将这两个DataFrame合并。

![](img/b8f5bfd906269d876b3a819f9716f131_42.png)

```python
# 将两个得分DataFrame按索引（股票代码）合并
# 假设两个DataFrame的索引都是股票代码，且形状一致
total_score_df = df_up + df_down

![](img/b8f5bfd906269d876b3a819f9716f131_44.png)

# 计算每个股票的总得分（对所有因子得分求和）
total_score_df[‘总分‘] = total_score_df.sum(axis=1)

# 最后，我们可以根据“总分”进行排序，选出综合得分最高的股票
final_ranking = total_score_df.sort_values(by=‘总分‘, ascending=False)
```

![](img/b8f5bfd906269d876b3a819f9716f131_46.png)

![](img/b8f5bfd906269d876b3a819f9716f131_48.png)

本节课中我们一起学习了因子打分与排序的完整流程。我们首先遍历两类因子并进行排序，然后利用`np.linspace`为排序后的股票分配得分，最后合并所有因子的得分并计算总分，从而得到股票的最终排名。这是将多个因子合成为一个可操作信号的关键步骤。