# Python金融分析与量化交易实战教程：P46：4-因子打分与排序

![](img/e8baff8529ace49a791f07ff61aad4e3_0.png)

![](img/e8baff8529ace49a791f07ff61aad4e3_0.png)

![](img/e8baff8529ace49a791f07ff61aad4e3_2.png)

![](img/e8baff8529ace49a791f07ff61aad4e3_4.png)

在本节课程中，我们将学习如何对筛选出的因子进行打分与排序。这是构建多因子模型的关键步骤，我们将分别处理“越高越好”和“越低越好”两类因子，为每只股票在每个因子上分配一个得分，为后续的综合评分做准备。

上一节我们完成了因子的初步筛选，得到了两个DataFrame。本节中我们来看看如何遍历这些因子数据，并进行排序与打分。

## 遍历因子与排序

首先，我们需要遍历两个DataFrame（`up` 和 `down`）中的每一个因子列。以下是具体步骤：

1.  **获取因子列名**：将DataFrame的列名转换为列表，以便遍历每一列（即每一个因子）。
2.  **对因子值进行排序**：对每个因子的数值进行排序。对于“越高越好”的因子，我们按降序排列；对于“越低越好”的因子，我们按升序排列。这里使用Pandas的 `sort_values` 方法，并通过 `inplace=True` 参数直接在原数据上修改排序结果。

以下是实现遍历与排序的代码框架：

```python
import pandas as pd
import numpy as np

# 假设 df_up 和 df_down 是已经准备好的DataFrame
# 遍历“越高越好”的因子DataFrame
for factor in df_up.columns.tolist():
    # 按当前因子列降序排序（数值大的排前面）
    df_up.sort_values(by=factor, ascending=False, inplace=True)
    # 接下来将在此处添加打分代码

![](img/e8baff8529ace49a791f07ff61aad4e3_6.png)

# 遍历“越低越好”的因子DataFrame
for factor in df_down.columns.tolist():
    # 按当前因子列升序排序（数值小的排前面）
    df_down.sort_values(by=factor, ascending=True, inplace=True)
    # 接下来将在此处添加打分代码
```

## 为排序后的因子分配得分

![](img/e8baff8529ace49a791f07ff61aad4e3_8.png)

排序完成后，我们需要为每只股票在每个因子上的排名分配一个具体的分数。一个简单直接的方法是：排名第一的得最高分，排名最后的得最低分。

![](img/e8baff8529ace49a791f07ff61aad4e3_10.png)

我们将使用NumPy的 `linspace` 函数来生成一个等差分数序列。这个函数非常适用于此类场景。

![](img/e8baff8529ace49a791f07ff61aad4e3_12.png)

![](img/e8baff8529ace49a791f07ff61aad4e3_14.png)

### 理解 `numpy.linspace` 函数

![](img/e8baff8529ace49a791f07ff61aad4e3_16.png)

`numpy.linspace` 函数用于在指定的区间内生成均匀间隔的数字。其核心参数如下：
*   `start`：序列的起始值。
*   `stop`：序列的结束值。
*   `num`：要生成的样本数量。

![](img/e8baff8529ace49a791f07ff61aad4e3_18.png)

![](img/e8baff8529ace49a791f07ff61aad4e3_20.png)

其基本公式为：
**`numpy.linspace(start, stop, num)`**

![](img/e8baff8529ace49a791f07ff61aad4e3_22.png)

让我们通过示例来理解它的用法：

![](img/e8baff8529ace49a791f07ff61aad4e3_24.png)

```python
import numpy as np
# 生成从1到300的300个等差数字（升序）
scores_asc = np.linspace(1, 300, 300)
print(scores_asc[:10]) # 输出前10个值，例如 [1., 2., 3., ...]

![](img/e8baff8529ace49a791f07ff61aad4e3_26.png)

# 生成从300到1的300个等差数字（降序）
scores_desc = np.linspace(300, 1, 300)
print(scores_desc[:10]) # 输出前10个值，例如 [300., 299., 298., ...]
```

![](img/e8baff8529ace49a791f07ff61aad4e3_28.png)

对于“越高越好”的因子，排序后排名第一（因子值最大）的股票应得最高分。因此，我们使用降序分数序列 `np.linspace(300, 1, 股票数量)`。
对于“越低越好”的因子，排序后排名第一（因子值最小）的股票也应得最高分。因此，我们同样使用降序分数序列 `np.linspace(300, 1, 股票数量)`。

![](img/e8baff8529ace49a791f07ff61aad4e3_30.png)

![](img/e8baff8529ace49a791f07ff61aad4e3_32.png)

### 实现打分逻辑

![](img/e8baff8529ace49a791f07ff61aad4e3_34.png)

现在，我们将打分逻辑整合到之前的遍历代码中。注意，我们使用 `df.shape[0]` 来动态获取股票数量，使代码更通用。

以下是完整的打分代码：

```python
# 为“越高越好”的因子打分
for factor in df_up.columns.tolist():
    df_up.sort_values(by=factor, ascending=False, inplace=True)
    # 生成降序分数序列，第一名300分，最后一名1分
    scores = np.linspace(df_up.shape[0], 1, df_up.shape[0])
    # 将生成的分数赋值给当前因子列
    df_up[factor] = scores

# 为“越低越好”的因子打分
for factor in df_down.columns.tolist():
    df_down.sort_values(by=factor, ascending=True, inplace=True)
    # 生成降序分数序列，第一名300分，最后一名1分
    scores = np.linspace(df_down.shape[0], 1, df_down.shape[0])
    # 将生成的分数赋值给当前因子列
    df_down[factor] = scores
```

![](img/e8baff8529ace49a791f07ff61aad4e3_36.png)

执行完上述代码后，`df_up` 和 `df_down` 中的数据不再是原始的因子值，而是转换后的因子得分（分数范围取决于股票数量）。

![](img/e8baff8529ace49a791f07ff61aad4e3_38.png)

## 合并数据并计算总分

两个DataFrame分别包含了不同类别因子的得分。为了计算每只股票的综合得分，我们需要先将它们合并。

一个简单的方法是使用Pandas的加法操作，前提是两个DataFrame具有相同的索引（股票代码）。合并后，对每只股票的所有因子得分求和，即可得到该股票的总分。

![](img/e8baff8529ace49a791f07ff61aad4e3_40.png)

```python
# 将两个得分DataFrame相加，得到综合得分DataFrame
total_score_df = df_up + df_down
# 计算每只股票的总分（对所有因子得分求和）
total_score_df['综合总分'] = total_score_df.sum(axis=1)
# 可以按总分进行排序，找到得分最高的股票
total_score_df.sort_values(by='综合总分', ascending=False, inplace=True)
print(total_score_df[['综合总分']].head()) # 查看总分最高的前几只股票
```

![](img/e8baff8529ace49a791f07ff61aad4e3_42.png)

本节课中我们一起学习了因子打分与排序的全过程。我们首先遍历并排序了“越高越好”和“越低越好”两类因子，然后利用 `numpy.linspace` 函数为排序后的股票分配得分，最后将两类因子的得分合并并计算了每只股票的综合总分。这为我们后续基于多因子得分进行选股奠定了坚实的基础。下一节，我们将探讨如何根据这些总分构建最终的投资组合。