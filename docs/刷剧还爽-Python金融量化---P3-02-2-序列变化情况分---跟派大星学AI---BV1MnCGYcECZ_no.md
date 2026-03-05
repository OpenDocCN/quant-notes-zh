# 量化交易全教程：P3：02-2-序列变化情况分析计算 📈

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_0.png)

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_2.png)

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_4.png)

在本节课中，我们将学习如何分析时间序列数据的变化情况，包括计算每日差异值、增长率以及相关的统计指标。这些是量化分析中理解数据动态的基础工具。

上一节我们介绍了数据的基本操作，本节中我们来看看如何量化数据的变化。

## 计算每日差异值

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_6.png)

首先，我们需要计算每一天指标相对于前一天的差异值。这可以帮助我们了解数据每日的绝对变化量。

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_8.png)

具体做法是：用后一天的数据减去前一天的数据。在Pandas中，这可以通过 `diff()` 方法轻松实现。

```python
# 计算每日差异值
daily_diff = data.diff()
print(daily_diff.head())
```

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_10.png)

执行上述代码后，你会看到一个新的DataFrame。例如，1月5日的数据是0.052，这个值是由1月5日的数据减去1月4日的数据得到的。1月4日对应的值是NaN，因为其前一天没有数据可供相减。

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_12.png)

这个指标在时间序列分析中非常常用。我们计算的是单日差异，你也可以通过指定 `periods` 参数来计算隔天、隔周等不同周期的差异。

养成查阅API文档的习惯非常重要。遇到不熟悉的函数或参数，可以通过复制函数名进行搜索来快速学习。

## 计算增长率

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_14.png)

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_16.png)

差异值反映的是绝对变化，但有时我们更关心相对变化，即增长率。例如，股价从1元涨到2元是100%的增长，而从10000元涨到10001元则增长甚微。

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_18.png)

增长率的计算公式是：`(后一个值 - 前一个值) / 前一个值`。

Pandas同样提供了现成的方法 `pct_change()` 来计算这个值。

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_20.png)

```python
# 计算每日增长率
daily_pct_change = data.pct_change()
print(daily_pct_change.head())
```

计算后，数据会从绝对差异变为百分比增长率。通常股价的日增长率数值较小，这符合市场的一般规律。

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_22.png)

## 分析统计指标

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_24.png)

在得到差异值或增长率序列后，我们常常需要分析其整体统计特性。

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_26.png)

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_28.png)

以下是计算增长率序列均值的方法：

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_30.png)

```python
# 计算增长率的平均值
mean_pct_change = daily_pct_change.mean()
print(mean_pct_change)
```

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_32.png)

这行代码会输出各个维度的平均增长率。

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_34.png)

## 可视化分析结果

为了更直观地理解这些统计指标，我们可以将其可视化。例如，平均增长率是一个单值指标，适合用条形图展示。

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_36.png)

```python
# 绘制平均增长率的条形图
mean_pct_change.plot(kind='bar', figsize=(16, 8))
```

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_38.png)

通过图表，各个指标的平均表现一目了然，便于我们进行快速分析和比较。

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_40.png)

![](img/fdca8d6df0b9c0f8fb3350fba5fa45f6_42.png)

本节课中我们一起学习了时间序列变化分析的核心方法。我们掌握了如何计算每日差异值和增长率，理解了绝对变化与相对变化的区别，并学会了计算相关统计指标以及进行简单的可视化。这些是构建更复杂量化模型的重要基础步骤。