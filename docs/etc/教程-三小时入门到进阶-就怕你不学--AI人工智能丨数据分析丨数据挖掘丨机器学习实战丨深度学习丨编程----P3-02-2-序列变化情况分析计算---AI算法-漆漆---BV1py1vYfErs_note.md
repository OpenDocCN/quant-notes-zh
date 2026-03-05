# Python金融分析与量化交易实战教程：P3：02-2：序列变化情况分析计算 📈

![](img/a1de00cee22c527793454fc2a95dad01_0.png)

![](img/a1de00cee22c527793454fc2a95dad01_2.png)

![](img/a1de00cee22c527793454fc2a95dad01_4.png)

在本节课中，我们将学习如何分析时间序列数据的变化情况，包括计算每日差异值、增长率以及进行简单的统计分析。这些是金融数据分析中非常基础和重要的技能。

上一节我们介绍了时间序列数据的基本结构和查看方法，本节中我们来看看如何计算和分析序列的变化。

![](img/a1de00cee22c527793454fc2a95dad01_6.png)

## 计算每日差异值

![](img/a1de00cee22c527793454fc2a95dad01_8.png)

首先，我们需要计算每一天指标相对于前一天的差异值。这可以帮助我们了解数据每日的绝对变化量。

在Pandas中，我们可以使用 `diff()` 函数轻松实现这个计算。该函数会计算当前行与前一行的差值。

![](img/a1de00cee22c527793454fc2a95dad01_10.png)

```python
# 计算每日差异值
daily_diff = data.diff()
print(daily_diff.head())
```

![](img/a1de00cee22c527793454fc2a95dad01_12.png)

执行上述代码后，你会看到一个新的DataFrame。例如，1月5日的数据等于1月5日的原始值减去1月4日的原始值。1月4日的数据由于没有前一天的数据可供比较，其差异值会显示为 `NaN`（Not a Number）。

## 计算每日增长率

![](img/a1de00cee22c527793454fc2a95dad01_14.png)

![](img/a1de00cee22c527793454fc2a95dad01_16.png)

仅仅知道绝对差异值有时不够直观。例如，股价从1元涨到2元和从10000元涨到10001元，虽然都涨了1元，但意义完全不同。因此，我们常常需要计算增长率（或变化率）。

![](img/a1de00cee22c527793454fc2a95dad01_18.png)

增长率的计算公式为：`(后一天的值 - 前一天的值) / 前一天的值`。

Pandas同样提供了现成的函数 `pct_change()` 来计算这个百分比变化。

![](img/a1de00cee22c527793454fc2a95dad01_20.png)

```python
# 计算每日增长率
daily_pct_change = data.pct_change()
print(daily_pct_change.head())
```

计算完成后，数据将显示为小数形式（例如0.05表示增长5%）。你可以使用 `.round(3)` 等方法将结果保留指定位数的小数，使其更易读。

![](img/a1de00cee22c527793454fc2a95dad01_22.png)

![](img/a1de00cee22c527793454fc2a95dad01_24.png)

## 进行统计分析

![](img/a1de00cee22c527793454fc2a95dad01_26.png)

![](img/a1de00cee22c527793454fc2a95dad01_28.png)

在得到每日增长率序列后，我们可能希望了解其整体的统计特性，例如平均增长率。

![](img/a1de00cee22c527793454fc2a95dad01_30.png)

以下是计算平均增长率的方法：

![](img/a1de00cee22c527793454fc2a95dad01_32.png)

![](img/a1de00cee22c527793454fc2a95dad01_34.png)

```python
# 计算各列的平均增长率
mean_pct_change = daily_pct_change.mean()
print(mean_pct_change)
```

为了更直观地展示各个指标的平均增长率，我们可以将其可视化。由于平均增长率是每个指标的一个单一数值，使用条形图（bar chart）比较合适。

```python
# 绘制平均增长率的条形图
mean_pct_change.plot(kind='bar', figsize=(16, 8))
```

![](img/a1de00cee22c527793454fc2a95dad01_36.png)

![](img/a1de00cee22c527793454fc2a95dad01_38.png)

通过图表，我们可以一目了然地看出哪个指标在观察期内的平均增长表现最好或最差。

![](img/a1de00cee22c527793454fc2a95dad01_40.png)

![](img/a1de00cee22c527793454fc2a95dad01_42.png)

本节课中我们一起学习了时间序列变化分析的核心操作：使用 `diff()` 计算差异值，使用 `pct_change()` 计算增长率，以及对结果进行统计分析和可视化。掌握这些方法，是进行更深入金融数据分析和量化交易策略研究的基础。