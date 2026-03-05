# Python金融分析与量化交易实战：P52：5-完成选股方法 📊

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_0.png)

在本节课中，我们将学习如何实现一个完整的选股方法。核心步骤包括：拼接多个因子数据表、为每个因子计算得分、汇总总分并进行排序，最终筛选出排名靠前的股票。我们将使用Pandas和NumPy库来完成这些操作。

---

## 拼接数据表 🔗

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_2.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_3.png)

上一节我们介绍了如何获取和处理单个因子数据。本节中，我们来看看如何将多个因子的数据表合并成一张大表，以便进行后续的综合评分。

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_4.png)

拼接操作的本质是使用一个DataFrame去连接另一个DataFrame。

以下是实现拼接的代码：

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_6.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_7.png)

```python
# 假设 df1 和 df2 是两个需要拼接的因子数据DataFrame
df_combined = df1.join(df2)
```

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_8.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_9.png)

现在，两个DataFrame已经拼接完成。我们为这个合并后的新表指定一个变量名，例如 `df_combined`。

---

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_11.png)

## 创建得分列并初始化 📝

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_12.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_13.png)

现在，我们有了包含所有因子数据的大表，但表中还没有用于排名的综合得分信息。

接下来，我们需要新建一个列来存放每个股票的得分值。首先，将这个得分列初始化为零。

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_15.png)

以下是创建并初始化得分列的代码：

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_16.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_17.png)

```python
# 在合并后的DataFrame中新建一个名为‘得分值’的列，并初始化为0
df_combined[‘得分值’] = 0
```

初始化后，我们需要实际计算这个得分值。计算前，我们可能需要准备一个与数据行数匹配的向量。例如，使用NumPy创建一个全为1的列向量。

```python
import numpy as np
# 假设有300个样本数据，创建一个300行1列的全1矩阵
ones_column = np.ones((300, 1))
```

---

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_19.png)

## 计算总分并排序 🧮

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_20.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_21.png)

每个因子的得分已经有了。本节的目标是将所有因子的得分相加，得到每个股票的总分，然后根据总分进行排序。

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_22.png)

计算总分就是对所有因子得分列执行求和操作。

以下是计算总分并排序的代码：

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_24.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_25.png)

```python
# 假设因子得分列名分别为 ‘factor1‘, ‘factor2‘, ..., ‘factor6‘
# 计算所有因子得分的总和，并赋值给‘得分值’列
df_combined[‘得分值’] = df_combined[[‘factor1‘, ‘factor2‘, ‘factor3‘, ‘factor4‘, ‘factor5‘, ‘factor6‘]].sum(axis=1)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_26.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_27.png)

# 根据‘得分值’列进行降序排序，分数最高的排在最前面
df_sorted = df_combined.sort_values(by=‘得分值‘, ascending=False)
```

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_28.png)

排序完成后，`df_sorted` 就是一个按照综合得分从高到低排列的DataFrame。

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_30.png)

---

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_31.png)

## 提取最终选股结果 🎯

排序之后，我们通常只关心排名最靠前的股票。例如，我们需要筛选出总分前十的股票。

我们不需要其他指标，只需要股票代码和它们的得分。因此，我们从排序后的DataFrame中提取股票代码索引。

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_33.png)

以下是提取前十名股票代码的代码：

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_34.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_35.png)

```python
# 获取得分前十的股票代码（索引），并转换为列表
top_10_stocks = df_sorted.index[:10].tolist()
```

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_36.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_37.png)

至此，`top_10_stocks` 列表就包含了当前根据综合因子评分选出的前十只股票。

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_38.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_39.png)

---

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_40.png)

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_41.png)

## 总结 📚

![](img/3a408f2da7a23afc5ab9ec714b2e5f03_42.png)

本节课中我们一起学习了如何完成一个基础的选股方法。我们首先将多个因子数据表拼接成一个综合数据表。然后，我们创建得分列，并通过求和计算每个股票的综合得分。接着，我们对所有股票按得分进行降序排序。最后，我们提取出排名前十的股票代码作为最终的选股结果。这个过程是量化选股策略中的一个核心环节，为后续的回测和交易执行奠定了基础。