# Python金融量化实战课程：P48：完成选股方法 📊

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_0.png)

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_2.png)

在本节课中，我们将学习如何完成一个核心的选股方法。该方法通过读取多个股票因子数据，对每个因子进行打分，然后合并所有分数并进行排序，最终筛选出综合得分最高的前十只股票。整个过程将使用Pandas库进行数据处理。

---

上一节我们介绍了如何读取和准备因子数据，本节中我们来看看如何将这些数据整合并计算综合得分。

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_4.png)

首先，我们需要将两个包含不同因子得分的数据表拼接在一起。拼接操作使用Pandas的`concat`函数，将一个DataFrame连接到另一个DataFrame。

```python
# 假设df1和df2是两个已经准备好的DataFrame
df_combined = pd.concat([df1, df2], axis=1)
```

拼接完成后，我们得到了一个包含所有因子数据的大表。我们将其命名为`df_rank`。

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_6.png)

```python
df_rank = df_combined
```

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_8.png)

现在，`df_rank`表中还没有综合排名的信息。我们需要新建一列来存放计算出的综合得分。

以下是创建新列并初始化的步骤：

```python
# 新建一个名为‘score’的列，初始值设为0
# 数据的行数应与样本数量一致，例如300行
df_rank[‘score’] = np.zeros((300, 1))
```

初始化后，`score`列中的数据目前都是0。接下来，我们需要计算每个股票的综合总分。

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_10.png)

计算总分并排序是本节的核心。我们已经有了各个因子的得分，现在需要将它们相加，得到一个总和。

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_12.png)

以下是计算总分和排序的步骤：

```python
# 计算所有因子列的总和，假设因子列名为‘factor1’， ‘factor2’等
# 使用.sum(axis=1)对每一行进行横向求和
df_rank[‘score’] = df_rank[[‘factor1‘， ’factor2‘， ...]].sum(axis=1)

# 根据‘score’列进行降序排序，分数最高的排在最前面
df_rank_sorted = df_rank.sort_values(by=‘score’， ascending=False)
```

排序完成后，我们得到了按综合得分从高到低排列的股票列表。我们通常只关心排名靠前的股票。

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_14.png)

以下是提取最终结果的步骤：

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_16.png)

```python
# 从排序后的DataFrame中提取‘score’列，它是一个Series对象
score_series = df_rank_sorted[‘score’]

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_18.png)

# 获取得分前十的股票代码（即DataFrame的索引），并转换为列表
top_10_stocks = score_series.index[:10].tolist()
```

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_20.png)

这样，我们就完成了`get_stocks`函数的核心逻辑。执行一次这个函数，它就会返回基于当前所有因子数据计算出的前十名股票代码。

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_22.png)

---

![](img/64c1003bb0be0e5bda2e94d0b6ec2685_24.png)

本节课中我们一起学习了如何构建一个完整的选股方法。我们首先将多个因子数据表拼接起来，然后创建新的列来计算综合得分，接着对所有股票的得分进行求和与排序，最后提取出得分最高的前十只股票。这个方法为后续的量化交易策略提供了关键的股票筛选功能。