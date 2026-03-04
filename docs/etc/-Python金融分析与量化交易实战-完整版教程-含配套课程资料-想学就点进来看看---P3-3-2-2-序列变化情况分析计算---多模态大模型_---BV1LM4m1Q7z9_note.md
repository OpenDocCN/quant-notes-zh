# Python金融分析与量化交易实战：P3：3.2.2-序列变化情况分析计算 📈

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_0.png)

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_2.png)

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_4.png)

在本节课程中，我们将学习如何分析时间序列数据的变化情况。具体来说，我们将计算股票数据中每日的差异值和增长率，并学习如何对这些变化进行统计和可视化。

## 计算每日差异值

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_6.png)

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_8.png)

上一节我们介绍了如何加载和查看时间序列数据。本节中，我们来看看如何计算数据中每一天与前一天的差异值。这个指标在金融分析中非常有用，它直接反映了价格或指标的每日波动。

以下是计算每日差异值的步骤：
1.  使用Pandas的`.diff()`方法可以轻松计算序列中相邻元素的差值。
2.  该方法默认计算当前行与前一行（即前一天）的差值。
3.  对于第一行数据，由于没有前一行数据可减，结果会显示为`NaN`（非数字）。

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_10.png)

```python
# 计算每日差异值
daily_diff = data.diff()
print(daily_diff.head())
```

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_12.png)

执行上述代码后，从第二行开始，每个值都等于当天数据减去前一天数据的结果。第一行由于没有前一天的参考数据，其结果为`NaN`。

## 计算每日增长率

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_14.png)

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_16.png)

了解了绝对差异值后，我们进一步探讨相对变化。在金融领域，增长率（或变化率）比绝对差值更能反映变化的幅度和意义。例如，股价从1元涨到2元是100%的增长，而从10000元涨到10001元则几乎可以忽略不计。

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_18.png)

以下是计算每日增长率的步骤：
1.  增长率公式为：`(后一天值 - 前一天值) / 前一天值`。
2.  Pandas提供了`.pct_change()`方法来直接计算这个百分比变化。

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_20.png)

```python
# 计算每日增长率
daily_pct_change = data.pct_change()
print(daily_pct_change.head())
```

计算完成后，数据将显示为每日相对于前一天的百分比变化。通常，股票单日的涨跌幅不会很大，因此这些值通常较小。

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_22.png)

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_24.png)

## 统计与可视化增长率

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_26.png)

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_28.png)

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_30.png)

在得到每日增长率序列后，我们可以对其进行统计分析，以获得整体趋势的洞察。例如，计算各个指标在整个时间段内的平均增长率。

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_32.png)

以下是进行统计和可视化的步骤：
1.  对增长率序列使用`.mean()`方法，可以计算出每个指标的平均日增长率。
2.  为了直观展示，我们可以将平均增长率用条形图绘制出来。

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_34.png)

```python
# 计算平均增长率
mean_pct_change = daily_pct_change.mean()
print(mean_pct_change)

# 可视化平均增长率
mean_pct_change.plot(kind='bar', figsize=(16, 8))
```

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_36.png)

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_38.png)

通过条形图，我们可以一目了然地比较不同指标的平均增长表现。Y轴的值通常较小，这符合金融数据波动的一般规律。

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_40.png)

![](img/33c49ac09d0ab1540fbbdbdeca2e3b2f_42.png)

本节课中我们一起学习了时间序列变化分析的核心操作。我们掌握了如何使用`.diff()`计算每日差异值，以及如何使用`.pct_change()`计算每日增长率。最后，我们还学习了如何对这些变化值进行统计汇总并用条形图进行可视化展示。这些是金融数据预处理和初步分析的基础技能。