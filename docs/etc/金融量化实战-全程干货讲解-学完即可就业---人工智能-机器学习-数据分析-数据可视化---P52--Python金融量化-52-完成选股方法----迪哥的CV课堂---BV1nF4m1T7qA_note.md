# Python金融量化：P52：完成选股方法

![](img/2754c184e11cedcd643c4a502930ef93_0.png)

在本节课中，我们将学习如何将多个股票因子评分表进行合并，并计算综合得分以完成最终的选股排序。我们将使用Pandas库进行数据拼接与计算，最终筛选出得分最高的前十只股票。

![](img/2754c184e11cedcd643c4a502930ef93_2.png)

## 数据拼接操作

上一节我们介绍了如何为单个因子进行评分。本节中，我们来看看如何将多个因子的评分结果合并到一张表中。

![](img/2754c184e11cedcd643c4a502930ef93_4.png)

拼接操作的核心是使用Pandas的`concat`函数，将一个DataFrame与另一个DataFrame进行合并。

![](img/2754c184e11cedcd643c4a502930ef93_6.png)

以下是拼接操作的代码实现：

![](img/2754c184e11cedcd643c4a502930ef93_8.png)

```python
# 假设 df1 和 df2 是两个已经计算好因子评分的DataFrame
combined_df = pd.concat([df1, df2], axis=1)
```

![](img/2754c184e11cedcd643c4a502930ef93_10.png)

现在，两个DataFrame已经拼接起来了。我们为这个新的DataFrame指定一个名字，例如`df_combined`。

![](img/2754c184e11cedcd643c4a502930ef93_12.png)

## 计算综合得分并排序

![](img/2754c184e11cedcd643c4a502930ef93_14.png)

在合并后的DataFrame中，我们还没有综合排名信息。接下来，我们需要新建一列来计算所有因子的综合得分。

![](img/2754c184e11cedcd643c4a502930ef93_16.png)

![](img/2754c184e11cedcd643c4a502930ef93_18.png)

首先，我们新建一个名为“得分值”的列，并暂时将其初始化为零。

```python
df_combined['得分值'] = 0
```

![](img/2754c184e11cedcd643c4a502930ef93_20.png)

![](img/2754c184e11cedcd643c4a502930ef93_22.png)

然后，我们需要实际计算这个得分值。计算总分其实就是将所有因子的评分进行求和。

![](img/2754c184e11cedcd643c4a502930ef93_24.png)

以下是计算总分并排序的步骤：

![](img/2754c184e11cedcd643c4a502930ef93_26.png)

1.  确定样本数量，例如我们有300只股票。
2.  构建一个维度为`(300, 1)`的NumPy数组来存放初始得分（这里初始化为1，但后续会被覆盖）。
3.  对每一行（即每一只股票）的所有因子评分进行求和，得到该股票的总分。
4.  根据总分进行降序排序，以找到得分最高的股票。

以下是实现代码：

```python
# 假设我们有6个因子列，列名分别为‘factor1’到‘factor6’
factor_columns = ['factor1', 'factor2', 'factor3', 'factor4', 'factor5', 'factor6']

# 计算每一行的总分（沿axis=1横向求和）
df_combined['得分值'] = df_combined[factor_columns].sum(axis=1)

![](img/2754c184e11cedcd643c4a502930ef93_28.png)

![](img/2754c184e11cedcd643c4a502930ef93_30.png)

# 根据‘得分值’列进行降序排序
df_sorted = df_combined.sort_values(by='得分值', ascending=False)
```

![](img/2754c184e11cedcd643c4a502930ef93_32.png)

排序完成后，`df_sorted`就是按照综合得分从高到低排列的股票数据。

![](img/2754c184e11cedcd643c4a502930ef93_34.png)

## 获取最终选股结果

![](img/2754c184e11cedcd643c4a502930ef93_36.png)

我们不需要所有数据，只需要排名前十的股票代码。

![](img/2754c184e11cedcd643c4a502930ef93_38.png)

因此，我们从排序后的DataFrame中提取股票代码索引，并转换为列表，然后取前十个元素。

![](img/2754c184e11cedcd643c4a502930ef93_40.png)

以下是获取最终结果的代码：

![](img/2754c184e11cedcd643c4a502930ef93_42.png)

```python
# 获取排序后DataFrame的索引（即股票代码），并转换为列表
top_stock_list = df_sorted.index.tolist()

![](img/2754c184e11cedcd643c4a502930ef93_44.png)

# 选取前十名
top_10_stocks = top_stock_list[:10]
```

至此，`top_10_stocks`就是我们根据多个因子综合评分选出的前十只股票。

![](img/2754c184e11cedcd643c4a502930ef93_46.png)

![](img/2754c184e11cedcd643c4a502930ef93_48.png)

## 函数封装与流程总结

![](img/2754c184e11cedcd643c4a502930ef93_50.png)

我们可以将上述步骤封装成一个函数`get_top_stocks`。

![](img/2754c184e11cedcd643c4a502930ef93_52.png)

以下是该函数的核心流程总结：

![](img/2754c184e11cedcd643c4a502930ef93_54.png)

1.  **读取因子数据**：获取各个因子的评分数据。
2.  **数据拼接**：使用`pd.concat`将所有因子评分表合并。
3.  **计算综合得分**：对每个股票的所有因子评分求和。
4.  **排序**：根据综合得分进行降序排序。
5.  **输出结果**：提取排名前十的股票代码并返回。

![](img/2754c184e11cedcd643c4a502930ef93_56.png)

执行一次这个函数，就能返回基于当前所有因子评分计算出的、综合得分最高的前十只股票列表。

![](img/2754c184e11cedcd643c4a502930ef93_58.png)

本节课中我们一起学习了如何整合多个股票因子评分，通过计算综合得分并排序，最终完成一个基础的量化选股方法。这是构建自动化交易策略的关键一步。