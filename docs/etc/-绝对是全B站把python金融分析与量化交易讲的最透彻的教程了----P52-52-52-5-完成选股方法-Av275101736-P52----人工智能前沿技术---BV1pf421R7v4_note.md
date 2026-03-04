# Python金融量化与股票交易：P52：完成选股方法

![](img/6662e2248d7cc225fbc60a2ead87599c_0.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_2.png)

## 概述
在本节课中，我们将要学习如何将多个股票因子评分表进行合并，并计算综合得分以完成最终的选股排序。我们将通过拼接DataFrame、计算总分和排序等步骤，实现一个能够筛选出排名前十股票的函数。

![](img/6662e2248d7cc225fbc60a2ead87599c_4.png)

---

![](img/6662e2248d7cc225fbc60a2ead87599c_6.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_8.png)

上一节我们介绍了如何为单个股票因子进行打分。本节中，我们来看看如何整合所有因子的评分，并基于综合得分选出最优的股票组合。

![](img/6662e2248d7cc225fbc60a2ead87599c_10.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_12.png)

### 拼接数据表
首先，我们需要将两个包含不同因子评分的数据表合并为一张大表。这个操作在Pandas中称为拼接（Concatenation）。

![](img/6662e2248d7cc225fbc60a2ead87599c_14.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_16.png)

以下是拼接两个DataFrame的代码：
```python
df_merged = df1.concat(df2)
```
我们使用 `concat` 方法将第二个DataFrame（`df2`）拼接到第一个DataFrame（`df1`）的后面。拼接完成后，我们得到一个包含所有因子数据的新DataFrame，可以将其命名为 `df_merged`。

![](img/6662e2248d7cc225fbc60a2ead87599c_18.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_20.png)

### 初始化得分列
现在，我们有了合并后的大表，但表中还没有综合排名的信息。接下来，我们需要新建一列来存放每个股票的综合得分。

以下是创建初始得分列的代码：
```python
df_merged['score'] = np.zeros((300, 1))
```
我们使用NumPy的 `zeros` 函数创建一个300行1列的矩阵，其中所有值初始化为0，并将其赋值给名为 `'score'` 的新列。这里的300代表样本数据的总数。

![](img/6662e2248d7cc225fbc60a2ead87599c_22.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_24.png)

### 计算总分并排序
初始化得分列后，我们需要计算每个股票的综合总分。假设我们有六个指标，总分就是将这六个指标的分数相加。

![](img/6662e2248d7cc225fbc60a2ead87599c_26.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_28.png)

以下是计算总分并排序的步骤代码：
```python
# 计算横向求和，得到每个股票的总分
total_score = df_merged.iloc[:, :6].sum(axis=1)
df_merged['score'] = total_score

![](img/6662e2248d7cc225fbc60a2ead87599c_30.png)

# 根据总分进行降序排序
df_sorted = df_merged.sort_values(by='score', ascending=False)
```
1.  我们使用 `.sum(axis=1)` 对前六列指标进行横向求和，得到每个股票的总分。
2.  然后，使用 `sort_values` 方法，根据 `'score'` 列进行降序排序（`ascending=False`），这样分数最高的股票会排在最前面。

![](img/6662e2248d7cc225fbc60a2ead87599c_32.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_34.png)

### 提取最终结果
排序完成后，我们只需要获取排名前十的股票代码。

![](img/6662e2248d7cc225fbc60a2ead87599c_36.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_38.png)

以下是提取前十名股票代码的代码：
```python
top_10_stocks = df_sorted['score'].head(10).index.tolist()
```
1.  我们从排序后的DataFrame中取出 `'score'` 这一列。
2.  使用 `.head(10)` 方法获取得分前十的行。
3.  通过 `.index` 获取这些行对应的股票代码索引，并用 `.tolist()` 将其转换为列表。

![](img/6662e2248d7cc225fbc60a2ead87599c_40.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_42.png)

至此，我们就得到了当前日期下，根据所有因子综合评分选出的前十只股票。

![](img/6662e2248d7cc225fbc60a2ead87599c_44.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_46.png)

---

![](img/6662e2248d7cc225fbc60a2ead87599c_48.png)

![](img/6662e2248d7cc225fbc60a2ead87599c_50.png)

## 总结
本节课中我们一起学习了完成选股方法的最后几个关键步骤。我们首先将多个因子评分表拼接成一个完整的DataFrame，然后初始化并计算了每个股票的综合得分，接着根据得分进行降序排序，最后提取出排名前十的股票代码。这个 `get_stocks` 函数的核心流程是：**读取因子数据 -> 进行打分 -> 合并数据 -> 计算总分 -> 排序 -> 输出结果**。通过这个方法，我们可以量化地筛选出在特定时间点综合表现最优的股票组合。