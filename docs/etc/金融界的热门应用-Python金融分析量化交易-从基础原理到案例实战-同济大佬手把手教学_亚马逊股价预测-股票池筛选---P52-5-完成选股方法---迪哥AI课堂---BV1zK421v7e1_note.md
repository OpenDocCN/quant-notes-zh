# Python金融分析量化交易：P52：5-完成选股方法 📊

![](img/75a1d7c3865d821985f711d9aec7a2fd_0.png)

![](img/75a1d7c3865d821985f711d9aec7a2fd_2.png)

在本节课中，我们将学习如何完成一个核心的选股方法。我们将把多个股票因子数据表进行拼接，计算每只股票的综合得分，并根据得分进行排序，最终筛选出排名前十的股票。整个过程将使用Pandas和NumPy库来实现。

---

![](img/75a1d7c3865d821985f711d9aec7a2fd_4.png)

上一节我们介绍了如何获取和计算单个股票因子。本节中，我们来看看如何整合多个因子数据，并基于综合评分进行选股。

![](img/75a1d7c3865d821985f711d9aec7a2fd_6.png)

首先，我们需要将两个包含不同因子数据的DataFrame进行拼接操作。

![](img/75a1d7c3865d821985f711d9aec7a2fd_8.png)

![](img/75a1d7c3865d821985f711d9aec7a2fd_10.png)

以下是拼接操作的步骤：
1.  使用 `pd.concat` 函数。
2.  将第二个DataFrame作为参数传入。
3.  为拼接后的新DataFrame指定一个名称。

![](img/75a1d7c3865d821985f711d9aec7a2fd_12.png)

```python
# 假设 df1 和 df2 是两个需要拼接的DataFrame
combined_df = pd.concat([df1, df2], axis=1)
combined_df.name = 'factor_combined'
```

![](img/75a1d7c3865d821985f711d9aec7a2fd_14.png)

现在，两个DataFrame已经成功拼接为一个名为 `factor_combined` 的新DataFrame。

![](img/75a1d7c3865d821985f711d9aec7a2fd_16.png)

---

![](img/75a1d7c3865d821985f711d9aec7a2fd_18.png)

然而，这个新的DataFrame目前只包含了原始的因子数据，还没有我们需要的综合排名信息。

![](img/75a1d7c3865d821985f711d9aec7a2fd_20.png)

接下来，我们需要在这个DataFrame中新建一列，用于存放每只股票的最终得分。

![](img/75a1d7c3865d821985f711d9aec7a2fd_22.png)

以下是新建得分列的步骤：
1.  在DataFrame中新增一个名为 `score` 的列。
2.  初始时，将该列的所有值设置为0。

![](img/75a1d7c3865d821985f711d9aec7a2fd_24.png)

![](img/75a1d7c3865d821985f711d9aec7a2fd_26.png)

```python
combined_df['score'] = 0
```

---

现在，我们需要计算每只股票的综合得分。综合得分由多个因子得分相加得到。

![](img/75a1d7c3865d821985f711d9aec7a2fd_28.png)

以下是计算总分的步骤：
1.  确定需要参与计算的因子列。
2.  对这些列进行求和操作，得到每只股票的总分。
3.  将总分赋值给之前新建的 `score` 列。

![](img/75a1d7c3865d821985f711d9aec7a2fd_30.png)

假设我们有六个因子列，列名分别为 `factor1` 到 `factor6`。
```python
# 计算六个因子的总和，并赋值给score列
combined_df['score'] = combined_df[['factor1', 'factor2', 'factor3', 'factor4', 'factor5', 'factor6']].sum(axis=1)
```

![](img/75a1d7c3865d821985f711d9aec7a2fd_32.png)

![](img/75a1d7c3865d821985f711d9aec7a2fd_34.png)

---

![](img/75a1d7c3865d821985f711d9aec7a2fd_36.png)

得到总分后，下一步是根据得分进行排序，以找出表现最好的股票。

以下是排序操作的步骤：
1.  使用 `sort_values` 方法对DataFrame进行排序。
2.  指定按 `score` 列排序。
3.  设置 `ascending=False` 以实现降序排列，即分数最高的排在最前面。

![](img/75a1d7c3865d821985f711d9aec7a2fd_38.png)

```python
sorted_df = combined_df.sort_values(by='score', ascending=False)
```

![](img/75a1d7c3865d821985f711d9aec7a2fd_40.png)

![](img/75a1d7c3865d821985f711d9aec7a2fd_42.png)

排序完成后，`sorted_df` 就是一个按照综合得分从高到低排列的DataFrame。

![](img/75a1d7c3865d821985f711d9aec7a2fd_44.png)

---

![](img/75a1d7c3865d821985f711d9aec7a2fd_46.png)

我们的最终目标是获取排名前十的股票代码，而不是所有数据。

![](img/75a1d7c3865d821985f711d9aec7a2fd_48.png)

以下是获取前十名股票代码的步骤：
1.  从排序后的DataFrame中提取索引（通常为股票代码）。
2.  将索引转换为列表。
3.  使用列表切片获取前十个元素。

![](img/75a1d7c3865d821985f711d9aec7a2fd_50.png)

![](img/75a1d7c3865d821985f711d9aec7a2fd_52.png)

```python
top_10_stocks = sorted_df.index[:10].tolist()
```

![](img/75a1d7c3865d821985f711d9aec7a2fd_54.png)

至此，`top_10_stocks` 这个列表就包含了我们根据多个因子综合评分选出的前十名股票代码。

![](img/75a1d7c3865d821985f711d9aec7a2fd_56.png)

---

![](img/75a1d7c3865d821985f711d9aec7a2fd_58.png)

本节课中我们一起学习了如何完成一个完整的选股方法。我们首先将多个因子数据表拼接起来，然后计算每只股票的综合得分，接着根据得分进行降序排序，最后提取出排名前十的股票代码。这个 `get_top_stocks` 函数的核心流程可以概括为：**读取因子数据 -> 进行打分 -> 拼接结果 -> 完成排序**。执行一次该函数，就能返回基于当前所有因子数据评选出的前十名股票。