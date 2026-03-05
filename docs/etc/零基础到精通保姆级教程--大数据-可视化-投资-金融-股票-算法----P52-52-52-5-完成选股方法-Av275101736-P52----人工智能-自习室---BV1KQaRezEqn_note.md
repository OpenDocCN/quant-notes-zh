# Python金融量化交易：P52：完成选股方法

![](img/2b9ed79d56112eadae922a44bfdc48c8_0.png)

## 概述 📋

![](img/2b9ed79d56112eadae922a44bfdc48c8_2.png)

在本节课中，我们将学习如何完成一个核心的选股方法。我们将把多个因子评分表拼接成一个综合数据表，计算每只股票的总得分，并根据总得分进行排序，最终筛选出排名前十的股票。这个过程是量化交易策略中构建股票池的关键步骤。

---

## 数据表拼接操作 🔗

![](img/2b9ed79d56112eadae922a44bfdc48c8_4.png)

上一节我们介绍了如何为单个因子生成评分表。本节中我们来看看如何将多个因子的评分表合并，以便进行综合评估。

![](img/2b9ed79d56112eadae922a44bfdc48c8_6.png)

拼接操作是指将一个DataFrame的数据合并到另一个DataFrame中。

以下是实现拼接的代码：

![](img/2b9ed79d56112eadae922a44bfdc48c8_8.png)

```python
# 假设 df1 和 df2 是两个需要拼接的因子评分DataFrame
df_combined = df1.join(df2)
```

![](img/2b9ed79d56112eadae922a44bfdc48c8_10.png)

现在，两个DataFrame已经拼接起来了。我们给这个合并后的新表起一个名字。

![](img/2b9ed79d56112eadae922a44bfdc48c8_12.png)

```python
df_rank = df_combined
```

![](img/2b9ed79d56112eadae922a44bfdc48c8_14.png)

---

## 创建得分列并初始化 🆕

![](img/2b9ed79d56112eadae922a44bfdc48c8_16.png)

我们已经有了一个包含所有因子评分的大表，但表中还没有用于存储最终排名的信息。

![](img/2b9ed79d56112eadae922a44bfdc48c8_18.png)

接下来，我们需要新建一个列来存放最终的排名结果。

![](img/2b9ed79d56112eadae922a44bfdc48c8_20.png)

我们暂时将这个列命名为“得分值”，并先将其所有值初始化为0。

![](img/2b9ed79d56112eadae922a44bfdc48c8_22.png)

以下是创建并初始化得分列的代码：

```python
import numpy as np
# 假设样本数量为300
num_samples = 300
df_rank['得分值'] = np.zeros((num_samples, 1))
```

![](img/2b9ed79d56112eadae922a44bfdc48c8_24.png)

现在，这一列已经构建完成，但其中的数据目前全是0。

![](img/2b9ed79d56112eadae922a44bfdc48c8_26.png)

---

## 计算总分并排序 🧮

每个因子的独立评分已经有了，接下来我们需要将这些指标的分值合并，计算出每只股票的总分。

计算总分其实就是对多个指标列进行求和操作。

![](img/2b9ed79d56112eadae922a44bfdc48c8_28.png)

以下是计算总分并排序的步骤：

![](img/2b9ed79d56112eadae922a44bfdc48c8_30.png)

1.  **计算总分**：对所有的因子评分列进行横向求和。
2.  **进行排序**：根据总分进行降序排列，因为我们需要找到得分最高的股票。

![](img/2b9ed79d56112eadae922a44bfdc48c8_32.png)

以下是实现代码：

![](img/2b9ed79d56112eadae922a44bfdc48c8_34.png)

```python
# 假设因子评分列名为 ‘factor1‘, ‘factor2‘, ..., ‘factor6‘
factor_columns = ['factor1', 'factor2', 'factor3', 'factor4', 'factor5', 'factor6']

# 计算总分，即对指定列进行横向求和
df_rank['总分'] = df_rank[factor_columns].sum(axis=1)

![](img/2b9ed79d56112eadae922a44bfdc48c8_36.png)

# 根据‘总分‘列进行降序排序
df_rank_sorted = df_rank.sort_values(by='总分', ascending=False)
```

排序完成后，我们得到了一个按总分从高到低排列的DataFrame。

![](img/2b9ed79d56112eadae922a44bfdc48c8_38.png)

---

![](img/2b9ed79d56112eadae922a44bfdc48c8_40.png)

## 提取最终选股结果 🏆

![](img/2b9ed79d56112eadae922a44bfdc48c8_42.png)

现在我们已经得到了包含总分的排序结果。但我们的目标不是所有数据，而是排名前十的股票。

![](img/2b9ed79d56112eadae922a44bfdc48c8_44.png)

因此，我们不需要其他指标列，只需要股票的代码和其对应的总分。

以下是提取最终结果的步骤：

![](img/2b9ed79d56112eadae922a44bfdc48c8_46.png)

1.  从排序后的DataFrame中，提取我们需要的列（例如股票代码和总分）。
2.  获取排名前十的股票代码。

![](img/2b9ed79d56112eadae922a44bfdc48c8_48.png)

以下是实现代码：

![](img/2b9ed79d56112eadae922a44bfdc48c8_50.png)

```python
# 提取股票代码（假设索引或列名为‘股票代码‘）和总分
# 这里以索引为股票代码为例
top_10_stocks = df_rank_sorted.head(10).index.tolist()
```

![](img/2b9ed79d56112eadae922a44bfdc48c8_52.png)

这样，我们就完成了 `get_stocks` 函数中的核心操作。

![](img/2b9ed79d56112eadae922a44bfdc48c8_54.png)

---

![](img/2b9ed79d56112eadae922a44bfdc48c8_56.png)

## 流程总结 📝

本节课中我们一起学习了完成选股方法的完整流程：

![](img/2b9ed79d56112eadae922a44bfdc48c8_58.png)

1.  **读取因子数据**：获取各个因子的评分数据。
2.  **数据拼接**：将多个因子的评分表合并成一个综合数据表。
3.  **计算总分**：对每个股票的多个因子评分进行求和，得到综合得分。
4.  **排序筛选**：根据综合得分进行降序排序，并提取排名前十的股票代码作为当期选股结果。

![](img/2b9ed79d56112eadae922a44bfdc48c8_60.png)

执行一次这个函数，就能返回基于当前所有因子评分计算出的、排名前十的股票列表。这是构建自动化量化选股策略非常关键的一步。