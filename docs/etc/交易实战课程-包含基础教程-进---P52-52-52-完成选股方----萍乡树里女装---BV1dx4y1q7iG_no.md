# 量化交易教程：P52：完成选股方法 📊

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_0.png)

在本节课中，我们将学习如何将多个股票因子数据表进行拼接，并基于这些因子计算综合得分，最终完成一个选股排名方法，筛选出得分最高的前十只股票。

上一节我们介绍了如何获取和准备单个因子数据，本节中我们来看看如何整合多个因子并进行综合排名。

## 数据拼接操作

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_2.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_3.png)

首先，我们需要将两个包含不同因子数据的 `DataFrame` 进行拼接，合并成一张大表。

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_4.png)

以下是拼接操作的代码：

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_6.png)

```python
# 假设 df1 和 df2 是两个需要拼接的 DataFrame
df_combined = df1.join(df2)
```

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_7.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_8.png)

拼接完成后，我们得到了一个包含所有因子数据的新 `DataFrame`，可以将其命名为 `df_combined`。

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_9.png)

## 创建得分列并初始化

现在，我们有了包含所有因子的大表，但表中还没有综合排名的信息。接下来，我们需要新建一个列来存储每个股票的得分。

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_11.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_12.png)

以下是创建并初始化得分列的代码：

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_13.png)

```python
import numpy as np

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_15.png)

# 新建一个名为‘得分值’的列，并初始化为0
df_combined[‘得分值’] = 0

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_16.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_17.png)

# 为了后续计算，也可以先创建一个全为1的列作为基础
# 这里创建一个 300行 x 1列 的全1矩阵，假设有300个样本
base_scores = np.ones((300, 1))
# 将这一列数据赋值给‘得分值’（此处仅为示例，实际计算会更复杂）
df_combined[‘得分值’] = base_scores
```

## 计算综合总分并排序

每个股票的各个因子得分已经就位，接下来我们需要将这些得分相加，得到每个股票的综合总分，然后根据总分进行排序。

以下是计算总分并排序的步骤：

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_19.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_20.png)

1.  **求和**：将代表六个因子的六列数据相加。
2.  **排序**：对求和得到的总分列进行降序排列，以找到得分最高的股票。

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_21.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_22.png)

以下是实现代码：

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_23.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_24.png)

```python
# 假设因子数据列名为 ‘factor1‘, ‘factor2‘, ..., ‘factor6‘
# 计算总分
df_combined[‘总分’] = df_combined[[‘factor1‘, ‘factor2‘, ‘factor3‘, ‘factor4‘, ‘factor5‘, ‘factor6‘]].sum(axis=1)

# 根据‘总分’列进行降序排序
df_sorted = df_combined.sort_values(by=‘总分‘, ascending=False)
```

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_26.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_27.png)

排序完成后，`df_sorted` 这个 `DataFrame` 就是按照综合得分从高到低排列的股票列表。

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_28.png)

## 提取最终选股结果

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_30.png)

我们的目标是选出得分排名前十的股票。因此，在排序后，我们需要提取这些股票的名称（即 `DataFrame` 的索引）。

以下是提取前十名股票代码的代码：

```python
# 获取排序后 DataFrame 的索引（即股票代码），并转换为列表
top_10_stocks = df_sorted.index[:10].tolist()
```

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_32.png)

至此，`top_10_stocks` 这个列表就包含了我们根据多个因子综合选出的前十只股票。

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_33.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_34.png)

## 方法流程总结

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_35.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_36.png)

本节课中我们一起学习了完成选股方法的完整流程：

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_37.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_38.png)

1.  **数据拼接**：使用 `df1.join(df2)` 将多个因子表合并。
2.  **得分初始化**：在合并后的表中创建得分列。
3.  **总分计算与排序**：对多个因子列求和得到总分，并按总分降序排序。
4.  **结果提取**：从排序后的结果中提取排名前十的股票代码。

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_39.png)

![](img/a59f2bdbd22b3215f89ad3cffd9b6cce_40.png)

这个 `get_top_stocks` 函数的核心逻辑是：读取因子数据、为数据打分、拼接结果并最终排序，从而返回在特定时间点综合表现最好的前十只股票列表。