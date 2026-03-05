# Python金融分析与量化交易实战：P52：完成选股方法 📊

![](img/be8e868740e74e90f5494f5e5ae729f7_0.png)

![](img/be8e868740e74e90f5494f5e5ae729f7_2.png)

在本节课中，我们将学习如何将多个股票因子评分表进行合并，并计算综合得分以完成最终的选股排名。我们将使用Pandas库进行数据拼接和排序操作，最终筛选出得分最高的前十只股票。

---

上一节我们介绍了如何为单个股票因子进行打分。本节中，我们来看看如何整合所有因子的评分，并基于综合得分进行选股。

![](img/be8e868740e74e90f5494f5e5ae729f7_4.png)

首先，我们需要将两个包含不同因子评分的DataFrame进行合并。

![](img/be8e868740e74e90f5494f5e5ae729f7_6.png)

![](img/be8e868740e74e90f5494f5e5ae729f7_8.png)

以下是拼接两个DataFrame的步骤：
1.  使用第一个DataFrame的`.join()`方法。
2.  将第二个DataFrame作为参数传入该方法。

![](img/be8e868740e74e90f5494f5e5ae729f7_10.png)

![](img/be8e868740e74e90f5494f5e5ae729f7_12.png)

```python
# 假设 df1 和 df2 是两个已存在的评分DataFrame
df_combined = df1.join(df2)
```

![](img/be8e868740e74e90f5494f5e5ae729f7_14.png)

现在，我们已经将两个DataFrame合并成一张大表，并命名为`df_combined`。但这张表中还没有综合排名的信息。

![](img/be8e868740e74e90f5494f5e5ae729f7_16.png)

---

![](img/be8e868740e74e90f5494f5e5ae729f7_18.png)

![](img/be8e868740e74e90f5494f5e5ae729f7_20.png)

接下来，我们需要在这张合并后的表中创建一列，用于存放最终的综合得分。

![](img/be8e868740e74e90f5494f5e5ae729f7_22.png)

我们首先新建一个名为“得分值”的列，并暂时用零填充它。

![](img/be8e868740e74e90f5494f5e5ae729f7_24.png)

```python
import numpy as np
# 获取DataFrame的行数，用于创建零值数组
num_rows = df_combined.shape[0]
df_combined[‘得分值’] = np.zeros((num_rows, 1))
```

现在，“得分值”这一列已经创建，但其中的数据全是零。我们需要计算每个股票在所有因子上的总分。

---

![](img/be8e868740e74e90f5494f5e5ae729f7_26.png)

计算总分的方法很简单：将每个股票在所有因子列上的得分相加。

![](img/be8e868740e74e90f5494f5e5ae729f7_28.png)

![](img/be8e868740e74e90f5494f5e5ae729f7_30.png)

以下是计算总分并排序的步骤：
1.  对DataFrame中所有的因子评分列进行横向求和。
2.  将求和结果赋值给“得分值”列。
3.  根据“得分值”列进行降序排序。

![](img/be8e868740e74e90f5494f5e5ae729f7_32.png)

```python
# 假设因子评分列名为 ‘factor1‘, ‘factor2‘, ...
factor_columns = [‘factor1‘, ‘factor2‘, ‘factor3‘, ‘factor4‘, ‘factor5‘, ‘factor6‘]
df_combined[‘得分值’] = df_combined[factor_columns].sum(axis=1)

![](img/be8e868740e74e90f5494f5e5ae729f7_34.png)

# 根据‘得分值‘进行降序排序
df_sorted = df_combined.sort_values(by=‘得分值‘, ascending=False)
```

![](img/be8e868740e74e90f5494f5e5ae729f7_36.png)

排序完成后，我们得到了一个按综合得分从高到低排列的新DataFrame `df_sorted`。

![](img/be8e868740e74e90f5494f5e5ae729f7_38.png)

![](img/be8e868740e74e90f5494f5e5ae729f7_40.png)

---

![](img/be8e868740e74e90f5494f5e5ae729f7_42.png)

我们最终的目标是选出得分最高的前十只股票，而不是所有数据。

![](img/be8e868740e74e90f5494f5e5ae729f7_44.png)

因此，我们只需要从排序后的结果中提取股票代码（即索引）列表。

![](img/be8e868740e74e90f5494f5e5ae729f7_46.png)

![](img/be8e868740e74e90f5494f5e5ae729f7_48.png)

以下是获取前十名股票代码的步骤：
1.  获取排序后DataFrame的索引。
2.  将索引转换为列表。
3.  使用列表切片取出前十个元素。

![](img/be8e868740e74e90f5494f5e5ae729f7_50.png)

```python
# 获取得分前十的股票代码列表
top_10_stocks = df_sorted.index[:10].tolist()
```

![](img/be8e868740e74e90f5494f5e5ae729f7_52.png)

![](img/be8e868740e74e90f5494f5e5ae729f7_54.png)

至此，我们就完成了`get_stocks`函数的核心操作。该函数的工作流程可以总结为：首先读取各因子评分数据，然后进行数据合并与综合打分，最后对总分排序并返回排名前十的股票代码。

![](img/be8e868740e74e90f5494f5e5ae729f7_56.png)

---

![](img/be8e868740e74e90f5494f5e5ae729f7_58.png)

本节课中我们一起学习了如何整合多个股票因子评分。我们通过Pandas的`.join()`方法合并数据，使用`.sum(axis=1)`计算每个股票的综合得分，并利用`.sort_values()`进行排序，最终筛选出表现最佳的十只股票。这是构建量化选股策略中非常关键的一步。