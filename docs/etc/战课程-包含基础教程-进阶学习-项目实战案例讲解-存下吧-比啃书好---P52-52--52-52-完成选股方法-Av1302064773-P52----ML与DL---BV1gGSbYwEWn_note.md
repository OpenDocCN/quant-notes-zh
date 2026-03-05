# 量化交易教程：P52：完成选股方法 📊

![](img/ee71bb64a6caddb282ab3c8b06dfef77_0.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_2.png)

在本节课中，我们将学习如何将多个股票因子评分表进行合并，并计算综合得分以完成最终的选股排名。我们将使用Pandas库进行数据拼接和排序操作。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_4.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_6.png)

---

![](img/ee71bb64a6caddb282ab3c8b06dfef77_8.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_10.png)

上一节我们介绍了如何为单个股票因子计算排名。本节中，我们来看看如何整合所有因子的评分，并基于综合得分筛选出排名靠前的股票。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_12.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_14.png)

首先，我们需要将两个包含不同因子评分的DataFrame进行拼接操作。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_16.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_18.png)

以下是拼接两个DataFrame的步骤：
1.  使用Pandas的`concat`函数。
2.  将第二个DataFrame作为参数传入第一个DataFrame的`concat`方法中。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_20.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_22.png)

```python
# 假设 df1 和 df2 是两个需要拼接的DataFrame
combined_df = pd.concat([df1, df2], axis=1)
```

![](img/ee71bb64a6caddb282ab3c8b06dfef77_24.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_26.png)

现在，我们已经将两个DataFrame拼接成了一个名为`combined_df`的大表。但是，这个表中还没有综合排名的信息。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_28.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_30.png)

接下来，我们需要在这个大表中新建一列，用于存放计算出的综合得分。初始时，我们可以先将这一列的值全部设为0。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_32.png)

以下是初始化得分列的步骤：
1.  使用`df[‘新列名’]`的语法添加新列。
2.  使用NumPy的`zeros`函数生成一个全零数组来填充该列。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_34.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_36.png)

```python
import numpy as np
# 假设有300个样本数据
combined_df[‘得分值’] = np.zeros((300, 1))
```

![](img/ee71bb64a6caddb282ab3c8b06dfef77_38.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_40.png)

现在，得分列已经构建完成，但其中的数据全是0。我们需要计算每个股票的总分，即对所有因子得分进行求和。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_42.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_44.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_46.png)

计算总分并排序的逻辑如下：
1.  对除得分列之外的所有因子评分列进行横向求和。
2.  将求和结果赋值给“得分值”列。
3.  根据“得分值”列进行降序排序，以找到得分最高的股票。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_48.png)

```python
# 计算总分，假设因子评分列名为 ‘factor1‘, ‘factor2‘, ...
# 注意：需要排除刚添加的‘得分值’列本身
score_columns = [col for col in combined_df.columns if col != ‘得分值‘]
combined_df[‘得分值’] = combined_df[score_columns].sum(axis=1)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_50.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_52.png)

# 根据‘得分值’进行降序排序
sorted_df = combined_df.sort_values(by=‘得分值‘, ascending=False)
```

![](img/ee71bb64a6caddb282ab3c8b06dfef77_54.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_56.png)

排序完成后，我们得到了一个按综合得分从高到低排列的DataFrame。通常，我们只关心排名最靠前的股票。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_58.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_60.png)

以下是提取最终选股结果的步骤：
1.  从排序后的DataFrame中提取我们需要的列，例如股票代码和得分。
2.  获取得分最高的前N只股票的代码列表。

![](img/ee71bb64a6caddb282ab3c8b06dfef77_62.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_64.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_66.png)

```python
# 提取‘得分值’这一列，它是一个Series对象
score_series = sorted_df[‘得分值‘]

![](img/ee71bb64a6caddb282ab3c8b06dfef77_68.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_70.png)

# 获取得分前十的股票的索引（假设索引是股票代码），并转换为列表
top_10_stocks = score_series.index[:10].tolist()
```

![](img/ee71bb64a6caddb282ab3c8b06dfef77_72.png)

![](img/ee71bb64a6caddb282ab3c8b06dfef77_74.png)

本节课中我们一起学习了完成选股方法的关键步骤。我们首先将多个因子评分表拼接成一个大表，然后通过求和计算每个股票的综合得分，最后根据得分进行降序排序，并提取出排名前十的股票代码。这个过程模拟了量化选股中整合多因子并做出投资决策的核心逻辑。