# Python金融分析与量化交易实战：P47：5-完成选股方法 📊

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_0.png)

在本节课中，我们将学习如何完成一个核心的选股方法。具体来说，我们会将多个股票因子数据表进行拼接，计算综合得分，并根据得分进行排序，最终筛选出排名前十的股票。

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_2.png)

---

## 数据表的拼接操作

上一节我们介绍了如何获取和处理单个因子数据。本节中，我们来看看如何将多个因子数据表合并成一张大表，以便进行综合评分。

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_4.png)

以下是拼接两个DataFrame的步骤：

1.  使用 `pd.concat()` 函数进行拼接。
2.  将拼接后的新DataFrame赋值给一个变量，例如 `df_rank`。

```python
# 假设 df1 和 df2 是两个需要拼接的因子数据DataFrame
df_rank = pd.concat([df1, df2], axis=1)
```

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_6.png)

通过以上操作，我们成功将两个DataFrame在列方向上拼接起来，创建了一个名为 `df_rank` 的新数据表。

---

## 计算综合得分并排序

现在，我们已经有了包含所有因子数据的大表 `df_rank`。接下来，我们需要为每只股票计算一个综合得分，并根据得分进行排序。

首先，我们需要在 `df_rank` 中新建一个名为“得分”的列，用于存放计算结果。

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_8.png)

```python
# 新建一个全为零的‘得分’列，其长度与数据行数相同
df_rank[‘得分’] = np.zeros((len(df_rank), 1))
```

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_10.png)

接着，我们需要计算综合得分。假设我们有六个因子指标，综合得分就是将这六个指标的数值相加。

```python
# 计算综合得分：将多个因子列的值横向求和
# 假设六个因子列名分别为 ‘factor1’， ‘factor2’， …， ‘factor6’
factor_columns = [‘factor1’， ‘factor2’， ‘factor3’， ‘factor4’， ‘factor5’， ‘factor6’]
df_rank[‘得分’] = df_rank[factor_columns].sum(axis=1)
```

得分计算完成后，我们需要根据得分进行排序，以找到表现最好的股票。这里我们使用降序排列，因为得分越高代表股票越好。

```python
# 根据‘得分’列进行降序排序
df_rank_sorted = df_rank.sort_values(by=‘得分’， ascending=False)
```

排序完成后，我们只需关注排名结果。我们的目标是获取得分前十的股票代码。

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_12.png)

```python
# 获取排序后前10行的索引（即股票代码），并转换为列表
top_10_stocks = df_rank_sorted.index[:10].tolist()
```

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_14.png)

---

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_16.png)

## 方法流程总结

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_18.png)

本节课中我们一起学习了如何构建一个完整的选股函数 `get_stocks`。其核心流程可以总结为以下三步：

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_20.png)

1.  **读取与拼接**：读取多个单因子数据表，并使用 `pd.concat()` 将它们拼接成一张综合数据表。
2.  **计算与打分**：在综合数据表中，通过横向求和（`.sum(axis=1)`）计算每只股票的综合得分。
3.  **排序与筛选**：根据综合得分进行降序排序（`.sort_values()`），并选取排名前十的股票代码作为最终输出。

![](img/0b7a34ea7da09db9e3590b0bd3e4948b_22.png)

通过这个方法，我们就能根据设定的多个因子，在特定日期筛选出当前最值得关注的十只股票，为量化交易决策提供数据支持。