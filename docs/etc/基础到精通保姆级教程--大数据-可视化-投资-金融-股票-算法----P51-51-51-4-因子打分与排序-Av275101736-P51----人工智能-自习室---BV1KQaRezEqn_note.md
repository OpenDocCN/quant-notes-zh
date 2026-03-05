# Python金融量化交易：P51：因子打分与排序 📊

![](img/56e1ed6c150ce364cadef3c341e384cd_0.png)

![](img/56e1ed6c150ce364cadef3c341e384cd_2.png)

在本节课中，我们将学习如何对选出的股票因子进行排序和打分。这是构建多因子模型的关键步骤，通过为每个因子赋予分数，我们可以综合评估股票的综合表现。

![](img/56e1ed6c150ce364cadef3c341e384cd_4.png)

上一节我们介绍了如何根据因子特性（越高越好或越低越好）将数据分为两个DataFrame。本节中，我们来看看如何遍历这些因子，进行排序并计算得分。

![](img/56e1ed6c150ce364cadef3c341e384cd_6.png)

## 遍历因子与排序 🔄

以下是遍历因子并进行排序的步骤。

首先，我们需要遍历每个因子。对于“越高越好”的因子DataFrame，我们获取其列名，然后遍历每一列（即每一个因子）。

![](img/56e1ed6c150ce364cadef3c341e384cd_8.png)

```python
import pandas as pd
import numpy as np

# 假设 `up_df` 是“越高越好”因子的DataFrame
for factor in up_df.columns:
    # 获取当前因子的数据
    current_factor_data = up_df[[factor]]
    # 对当前因子进行排序（降序，因为越高越好）
    up_df.sort_values(by=factor, ascending=False, inplace=True)
```

![](img/56e1ed6c150ce364cadef3c341e384cd_10.png)

代码说明：
*   `up_df.columns` 获取DataFrame的所有列名。
*   `sort_values(by=factor, ascending=False, inplace=True)` 对 `up_df` 按照指定因子列进行降序排序。`inplace=True` 参数确保排序结果直接修改原DataFrame，无需重新赋值。

![](img/56e1ed6c150ce364cadef3c341e384cd_12.png)

## 为排序结果打分 🏆

![](img/56e1ed6c150ce364cadef3c341e384cd_14.png)

![](img/56e1ed6c150ce364cadef3c341e384cd_16.png)

排序完成后，我们需要为排序结果打分。简单起见，我们直接按排名顺序赋予分数：第一名得最高分，最后一名得最低分。

![](img/56e1ed6c150ce364cadef3c341e384cd_18.png)

我们将使用NumPy的 `linspace` 函数来生成一个线性的分数序列。该函数可以方便地生成指定范围内等间隔的数值。

![](img/56e1ed6c150ce364cadef3c341e384cd_20.png)

![](img/56e1ed6c150ce364cadef3c341e384cd_22.png)

以下是 `numpy.linspace` 函数的基本用法：

![](img/56e1ed6c150ce364cadef3c341e384cd_24.png)

![](img/56e1ed6c150ce364cadef3c341e384cd_26.png)

```python
# 生成从1到300的300个等间隔数字（即1, 2, 3, ..., 300）
scores_asc = np.linspace(1, 300, 300)
print(scores_asc[:5], scores_asc[-5:]) # 查看开头和结尾

![](img/56e1ed6c150ce364cadef3c341e384cd_28.png)

![](img/56e1ed6c150ce364cadef3c341e384cd_30.png)

# 生成从300到1的300个等间隔数字（即300, 299, 298, ..., 1）
scores_desc = np.linspace(300, 1, 300)
print(scores_desc[:5], scores_desc[-5:]) # 查看开头和结尾
```

![](img/56e1ed6c150ce364cadef3c341e384cd_32.png)

现在，我们将其应用到打分过程中。对于“越高越好”的因子，排序后排名第一的股票应得最高分。

![](img/56e1ed6c150ce364cadef3c341e384cd_34.png)

![](img/56e1ed6c150ce364cadef3c341e384cd_36.png)

```python
# 对“越高越好”的因子进行打分
for factor in up_df.columns:
    up_df.sort_values(by=factor, ascending=False, inplace=True)
    # 生成分数：股票数量为n，第一名得n分，最后一名得1分
    n_stocks = len(up_df)
    scores = np.linspace(n_stocks, 1, n_stocks)
    # 将生成的分数赋值给当前因子列（替换原来的因子值）
    up_df[factor] = scores
```

![](img/56e1ed6c150ce364cadef3c341e384cd_38.png)

接下来，我们需要对“越低越好”的因子DataFrame进行类似操作，但打分逻辑相反：因子值最小的股票应得最高分。

```python
# 对“越低越好”的因子进行打分
for factor in down_df.columns:
    down_df.sort_values(by=factor, ascending=True, inplace=True) # 升序排序
    # 生成分数：股票数量为n，第一名（值最小）得n分，最后一名得1分
    n_stocks = len(down_df)
    scores = np.linspace(n_stocks, 1, n_stocks)
    # 将生成的分数赋值给当前因子列
    down_df[factor] = scores
```

## 合并数据与计算总分 ➕

![](img/56e1ed6c150ce364cadef3c341e384cd_40.png)

完成两个DataFrame的因子打分后，我们得到了两个包含因子得分的表格。为了计算每只股票的综合得分，我们需要将这两个表格合并。

![](img/56e1ed6c150ce364cadef3c341e384cd_42.png)

一个简单的方法是，将两个DataFrame中所有因子的分数直接按行相加。这要求两个DataFrame的股票索引顺序一致。在实际操作中，更稳妥的方法是先对齐索引再进行计算。

```python
# 假设两个DataFrame的股票代码（索引）已经对齐
# 计算总分：将所有因子的分数相加
total_score_df = up_df + down_df
# 计算每只股票的总分
total_score_df['total_score'] = total_score_df.sum(axis=1)
# 按总分进行排序，选出得分最高的股票
final_ranked_stocks = total_score_df.sort_values(by='total_score', ascending=False)
```

![](img/56e1ed6c150ce364cadef3c341e384cd_44.png)

![](img/56e1ed6c150ce364cadef3c341e384cd_46.png)

本节课中我们一起学习了因子打分与排序的完整流程。我们首先遍历每个因子并进行排序，然后利用 `numpy.linspace` 函数根据排序结果生成分数，最后合并所有因子的分数并计算总分，从而得到股票的综合排名。这是将多个因子信息合成为单一投资信号的重要步骤。