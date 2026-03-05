# Python金融量化交易：P52：5-完成选股方法 📊

![](img/67a5308a282446a4dfd3259a502bea5d_0.png)

![](img/67a5308a282446a4dfd3259a502bea5d_2.png)

在本节课中，我们将要学习如何将多个股票因子评分表进行合并，并计算综合得分以完成最终的选股排名。我们将使用Pandas库进行数据拼接和排序操作，最终筛选出得分最高的前十只股票。

---

![](img/67a5308a282446a4dfd3259a502bea5d_4.png)

上一节我们介绍了如何为单个股票因子进行打分。本节中，我们来看看如何整合所有因子的评分，并基于综合得分进行选股。

![](img/67a5308a282446a4dfd3259a502bea5d_6.png)

首先，我们需要将两个包含不同因子评分的DataFrame进行拼接操作。拼接操作本质上是将一个DataFrame的数据合并到另一个DataFrame中。

![](img/67a5308a282446a4dfd3259a502bea5d_8.png)

以下是拼接两个DataFrame的代码：

![](img/67a5308a282446a4dfd3259a502bea5d_10.png)

```python
df_combined = df1.append(df2)
```

![](img/67a5308a282446a4dfd3259a502bea5d_12.png)

现在，我们已经将两个DataFrame拼接成了一个名为`df_combined`的大表。

![](img/67a5308a282446a4dfd3259a502bea5d_14.png)

![](img/67a5308a282446a4dfd3259a502bea5d_16.png)

---

![](img/67a5308a282446a4dfd3259a502bea5d_18.png)

但是，这个大表中还没有我们最终的综合排名信息。接下来，我们需要新建一列来存放每个股票的综合得分。

![](img/67a5308a282446a4dfd3259a502bea5d_20.png)

以下是新建得分列的代码：

![](img/67a5308a282446a4dfd3259a502bea5d_22.png)

```python
import numpy as np
df_combined['得分'] = np.zeros((df_combined.shape[0], 1))
```

![](img/67a5308a282446a4dfd3259a502bea5d_24.png)

我们使用`numpy.zeros`创建了一个初始值全为0的列，其维度与DataFrame的行数（即股票数量）相匹配。

![](img/67a5308a282446a4dfd3259a502bea5d_26.png)

---

上一节我们为每个因子计算了得分。本节中，我们来看看如何计算所有因子的总分。

计算总分其实就是将每个股票在所有因子上的得分进行求和。以下是计算总分的代码：

![](img/67a5308a282446a4dfd3259a502bea5d_28.png)

![](img/67a5308a282446a4dfd3259a502bea5d_30.png)

```python
df_combined['总分'] = df_combined[因子列列表].sum(axis=1)
```

![](img/67a5308a282446a4dfd3259a502bea5d_32.png)

这里，`因子列列表`代表所有因子评分列的名称列表。`axis=1`参数表示按行进行求和。

![](img/67a5308a282446a4dfd3259a502bea5d_34.png)

---

![](img/67a5308a282446a4dfd3259a502bea5d_36.png)

得到总分后，我们需要根据总分对股票进行降序排序，以找出得分最高的股票。

![](img/67a5308a282446a4dfd3259a502bea5d_38.png)

以下是进行排序的代码：

![](img/67a5308a282446a4dfd3259a502bea5d_40.png)

```python
df_sorted = df_combined.sort_values(by='总分', ascending=False)
```

![](img/67a5308a282446a4dfd3259a502bea5d_42.png)

参数`ascending=False`指定了按降序排列，即分数最高的排在最前面。

![](img/67a5308a282446a4dfd3259a502bea5d_44.png)

![](img/67a5308a282446a4dfd3259a502bea5d_46.png)

---

![](img/67a5308a282446a4dfd3259a502bea5d_48.png)

排序完成后，我们只需要关注排名结果。我们的目标是获取总分排名前十的股票代码。

![](img/67a5308a282446a4dfd3259a502bea5d_50.png)

以下是获取前十名股票代码的代码：

![](img/67a5308a282446a4dfd3259a502bea5d_52.png)

![](img/67a5308a282446a4dfd3259a502bea5d_54.png)

```python
top_10_stocks = df_sorted.head(10).index.tolist()
```

![](img/67a5308a282446a4dfd3259a502bea5d_56.png)

这行代码做了三件事：
1.  `df_sorted.head(10)` 选取排序后DataFrame的前10行。
2.  `.index` 获取这10行对应的索引（即股票代码）。
3.  `.tolist()` 将索引转换为Python列表，方便后续使用。

![](img/67a5308a282446a4dfd3259a502bea5d_58.png)

---

![](img/67a5308a282446a4dfd3259a502bea5d_60.png)

本节课中我们一起学习了完成选股方法的完整流程。我们首先将多个因子评分表拼接成一个大表，然后计算每个股票的综合得分，接着根据综合得分进行降序排序，最后提取出排名前十的股票代码。至此，我们构建了一个能够基于多因子模型进行初步选股的核心函数。