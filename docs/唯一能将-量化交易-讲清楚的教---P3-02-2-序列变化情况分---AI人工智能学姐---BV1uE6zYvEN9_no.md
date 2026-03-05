# Python金融量化+股票交易：P3：02-2-序列变化情况分析计算 📈

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_0.png)

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_2.png)

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_4.png)

在本节课中，我们将要学习如何分析时间序列数据的变化情况，包括计算每日差异值、增长率以及相关的统计指标。这些是量化分析中理解数据动态的基础操作。

上一节我们介绍了如何获取和查看股票数据，本节中我们来看看如何计算和分析这些数据的变化。

## 计算每日差异值

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_6.png)

首先，我们需要计算每一天指标与前一天的差异值。这个指标反映了数据每日的绝对变化量。

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_8.png)

在Pandas中，我们可以使用 `diff()` 函数轻松实现这个计算。该函数会计算当前行与前一行的差值。

```python
# 计算每日差异值
diff_data = data.diff()
print(diff_data.head())
```

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_10.png)

执行上述代码后，你会看到一个新的DataFrame。例如，1月5日的数据是0.052，这个值是由1月5日的数据减去1月4日的数据得到的。由于1月4日没有前一天的数据，所以其结果为NaN（Not a Number）。

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_12.png)

`diff()` 函数默认计算与前一天的差异。我们也可以指定参数来计算间隔多天的差异。

以下是使用 `diff()` 函数时需要注意的几点：
*   函数返回一个新的DataFrame，其中包含每日的差异值。
*   第一行数据由于没有前一行数据，其差异值结果为NaN。
*   可以通过设置参数（如 `periods`）来计算非相邻日期间的差异。

## 计算增长率

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_14.png)

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_16.png)

差异值反映的是绝对变化，但在金融分析中，我们更常关注相对变化，即增长率。增长率能更好地反映变化的幅度，尤其是在比较不同基数的数据时。

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_18.png)

增长率的计算公式为：`(后一天的值 - 前一天的值) / 前一天的值`。

Pandas提供了 `pct_change()` 函数来直接计算这个百分比变化。

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_20.png)

```python
# 计算每日增长率
pct_change_data = data.pct_change()
print(pct_change_data.head())
```

计算增长率后，我们可以对结果进行格式化，例如保留三位小数，使其更易读。

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_22.png)

```python
# 格式化显示，保留三位小数
print(pct_change_data.head().round(3))
```

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_24.png)

## 分析统计指标

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_26.png)

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_28.png)

在得到每日的增长率序列后，我们可以进一步计算其统计特性，例如平均值，来了解该指标在观察期内的整体表现。

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_30.png)

我们可以直接对增长率序列使用 `mean()` 函数来计算其平均值。

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_32.png)

```python
# 计算各列增长率的平均值
mean_pct_change = pct_change_data.mean()
print(mean_pct_change)
```

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_34.png)

为了更直观地展示各个指标的平均增长率，我们可以将其可视化。由于平均值是单个数值，适合用条形图来展示。

以下是绘制平均增长率条形图的步骤：
1.  使用 `mean()` 函数计算平均增长率。
2.  调用 `plot()` 方法并指定图形类型为条形图（`kind='bar'`）。
3.  设置合适的图形尺寸以便观察。

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_36.png)

```python
# 绘制平均增长率的条形图
mean_pct_change.plot(kind='bar', figsize=(16, 8))
```

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_38.png)

通过这个条形图，我们可以一目了然地比较不同指标在观察期内的平均增长表现。

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_40.png)

![](img/a8fcd83d53af8bc90caebbaaf6a8e4b5_42.png)

本节课中我们一起学习了时间序列变化分析的核心操作。我们掌握了如何使用 `diff()` 函数计算每日差异值，以及如何使用 `pct_change()` 函数计算增长率。最后，我们还学习了如何计算增长率的统计平均值并将其通过条形图可视化。这些技能是进行更深入金融数据分析和量化策略构建的重要基础。