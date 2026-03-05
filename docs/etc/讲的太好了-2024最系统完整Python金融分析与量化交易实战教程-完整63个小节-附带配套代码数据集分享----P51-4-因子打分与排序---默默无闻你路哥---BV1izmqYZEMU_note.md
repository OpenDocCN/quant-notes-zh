# Python金融分析与量化交易实战：P51：4-因子打分与排序

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_0.png)

在本节课中，我们将学习如何对选出的股票因子进行打分与排序。这是构建多因子模型的关键步骤，我们将分别处理“越高越好”和“越低越好”两类因子，为每只股票的每个因子赋予一个分数，为后续的综合评分做准备。

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_2.png)

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_3.png)

上一节我们介绍了如何筛选出两类因子数据框。本节中我们来看看如何遍历这些数据框，并对每个因子进行排序和打分。

## 遍历因子与排序

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_5.png)

首先，我们需要遍历数据框中的每一个因子列。我们的数据框结构是300行（股票）乘以3列（因子）。以下是具体步骤：

1.  **获取因子列名**：我们将数据框的列名转换为列表，以便遍历每一个因子。
2.  **对因子值进行排序**：对于“越高越好”的因子，数值越大排名应越靠前；对于“越低越好”的因子则相反。我们使用Pandas的`sort_values`方法进行排序。

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_7.png)

```python
import pandas as pd
import numpy as np

# 假设 fundamental_up 是“越高越好”因子的DataFrame
# 假设 fundamental_down 是“越低越好”因子的DataFrame

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_9.png)

# 遍历“越高越好”的因子
for factor in fundamental_up.columns.tolist():
    # 按当前因子列进行降序排序（数值大的在前）
    fundamental_up.sort_values(by=factor, ascending=False, inplace=True)
```

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_11.png)

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_12.png)

代码中的`inplace=True`参数非常重要，它确保排序结果直接修改原始数据框，而无需重新赋值。

## 为排序结果打分

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_14.png)

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_15.png)

排序完成后，我们需要为排序位置赋予分数。为了简化，我们采用线性打分法：排名第一的得最高分，排名最后的得最低分。由于共有300只股票，最高分设为300，最低分设为1。

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_16.png)

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_17.png)

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_18.png)

我们将使用NumPy的`linspace`函数来生成一个等差数列作为分数序列。

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_19.png)

```python
# 查看 linspace 函数的用法
# np.linspace(start, stop, num)
# start: 序列起始值
# stop: 序列结束值
# num: 生成的样本数

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_21.png)

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_22.png)

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_24.png)

print(np.linspace(1, 300, 300)) # 输出从1到300的300个数
print(np.linspace(300, 1, 300)) # 输出从300到1的300个数
```

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_26.png)

对于“越高越好”的因子，排序后第一名（数值最大）应得300分，最后一名得1分，因此使用`np.linspace(300, 1, 股票数量)`。
对于“越低越好”的因子，排序后第一名（数值最小）应得300分，最后一名得1分，因此使用`np.linspace(1, 300, 股票数量)`。

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_28.png)

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_29.png)

以下是完整的打分代码：

```python
# 对“越高越好”因子进行打分
for factor in fundamental_up.columns.tolist():
    fundamental_up.sort_values(by=factor, ascending=False, inplace=True)
    # 生成打分序列：第一名300分，最后一名1分
    scores = np.linspace(300, 1, len(fundamental_up))
    # 将当前因子列的值替换为对应的分数
    fundamental_up[factor] = scores

# 对“越低越好”因子进行打分
for factor in fundamental_down.columns.tolist():
    fundamental_down.sort_values(by=factor, ascending=True, inplace=True) # 升序排序，最小的在前
    # 生成打分序列：第一名（数值最小）300分，最后一名1分
    scores = np.linspace(300, 1, len(fundamental_down))
    # 将当前因子列的值替换为对应的分数
    fundamental_down[factor] = scores
```

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_31.png)

## 合并数据框与计算总分

完成两类因子的独立打分后，我们得到了两个新的DataFrame，其中存储的不再是原始因子值，而是标准化后的分数。为了计算每只股票的综合得分，我们需要将这两个DataFrame合并。

```python
# 将两个打分后的DataFrame按索引（股票代码）合并
total_score_df = fundamental_up + fundamental_down
# 计算每只股票的总分（各因子分数之和）
total_score_df['总分'] = total_score_df.sum(axis=1)
# 按总分进行降序排序，选出综合评分最高的股票
final_ranking = total_score_df.sort_values(by='总分', ascending=False)
```

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_33.png)

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_34.png)

![](img/e8f05a08d2fdd5fce937fc20fdbaefbc_35.png)

本节课中我们一起学习了因子打分与排序的完整流程。我们首先遍历因子并排序，然后利用`np.linspace`生成分数序列进行赋值，最后合并分数并计算总分。这个过程将不同量纲和方向的因子统一转化为可加和的分数，为构建最终的投资组合打下了基础。下一节，我们将基于这个综合评分来筛选最终的股票组合。