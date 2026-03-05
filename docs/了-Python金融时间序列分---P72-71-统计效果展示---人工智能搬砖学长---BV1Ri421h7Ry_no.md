# Python金融时间序列分析与量化交易实战教程：P72：71.统计效果展示 📊

![](img/a9ec277a11a0acaae73d42f7aacaebd3_0.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_2.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_4.png)

在本节课中，我们将学习如何对聚类分析的结果进行统计，并基于统计结果制定一个简单的交易策略。我们将通过观察特征组合与最终涨跌结果的关系，来判断在特定市场条件下买入或卖出的可能性，并评估该策略的预测准确性和最终收益。

---

![](img/a9ec277a11a0acaae73d42f7aacaebd3_6.png)

上一节我们介绍了如何通过聚类算法对股票数据进行分组。本节中，我们来看看如何对这些分组结果进行统计分析，并基于统计规律制定交易策略。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_8.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_10.png)

结果拿到手之后，接下来需要做统计。统计的目的是观察哪一种可能性多，哪一种可能性少。接下来，还需要对数据做一点变化。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_12.png)

因为现在需要观察的是两个特征（前一天涨跌和前两天的涨跌）与一个结果（当天涨跌）的关系。但目前数据中，它们被组合在一起作为一个特征。这里对数据稍作变换：对当前`group`中的最后一列（即结果列）进行操作，使用`.unstack()`方法。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_14.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_16.png)

```python
# 对分组结果进行数据透视，便于观察特征组合与结果的关系
result = data.groupby(['feature1', 'feature2', 'result']).size().unstack(fill_value=0)
```
`fill_value=0`参数的意思是，如果某些组合没有对应的值，则用0填充。这样能确保所有可能的特征组合都被展示出来。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_18.png)

此时得到的结果是一个以两个特征为索引、以不同结果为列的表格。这清晰地展示了在不同特征变化下，最终结果的分布情况。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_20.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_22.png)

---

![](img/a9ec277a11a0acaae73d42f7aacaebd3_24.png)

以下是基于统计结果制定策略的步骤。

接下来需要执行策略。使用`np.where`函数进行条件判断。传入的数据`data`是指定好的、包含“前一天”和“前两天”涨跌特征的两列。

```python
import numpy as np
# 判断是否满足“前一天涨”且“前两天涨”的条件
condition = np.where((data['feature1'] == 1) & (data['feature2'] == 1), True, False)
```
计算这两个特征的和是否等于2。等于2意味着前两天都是上涨。然后，将这个判断结果展示出来。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_26.png)

观察这个结果可以发现：当前一天涨了，且前两天也涨了的情况下，接下来一天大概率是下跌的。这似乎与实际市场中的“连续上涨后可能回调”的预感相似。其他情况下则更可能是上涨。

现在进行具体统计。将结果用不同颜色可视化：用绿色表示下跌（-1），用红色表示上涨（1）。通过对比可以发现，在“前两天都涨”这个条件下，下跌的计数更多。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_28.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_30.png)

这意味着，如果前一天和前两天都是上涨，那么第三天大概率会下跌。其他情况下则更可能上涨。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_32.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_34.png)

最后，基于这个统计规律创建一个新的预测列。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_36.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_38.png)

```python
# 根据统计规律生成交易信号
data['position'] = np.where((data['feature1'] + data['feature2']) == 2, -1, 1)
```
这里`(data[‘feature1’] + data[‘feature2’]) == 2`的判断，纯粹是根据前面统计观察数出来的结果。大家使用自己的数据时，统计出的结果可能不同。这里只是演示统计方法。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_40.png)

---

![](img/a9ec277a11a0acaae73d42f7aacaebd3_42.png)

统计方法完成后，我们来评估一下这个策略的预测效果。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_44.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_46.png)

做完之后，来算一算我们预测的走势与实际走势`data[‘direction’]`有多少是一致的。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_48.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_50.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_52.png)

```python
# 比较预测值与实际值的一致性
comparison = (data['position'] == data['direction'])
match_counts = comparison.value_counts()
```
`True`的次数代表预测正确的天数，`False`的次数代表预测错误的天数。结果显示，有1102天预测正确，1033天预测错误。看起来预测准确性略高于一半，基于统计的方法稍微多预测对了几次。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_54.png)

---

![](img/a9ec277a11a0acaae73d42f7aacaebd3_56.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_58.png)

既然预测准确性略有提升，最后我们来看看按照这个策略进行交易，最终的收益结果如何。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_60.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_62.png)

还是使用之前计算累积收益的代码，稍作修改。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_64.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_66.png)

```python
# 计算基于统计策略的累积收益
strategy_returns = data['returns'] * data['position'].shift(1)
cumulative_strategy_returns = (1 + strategy_returns).cumprod()
```
这里`data[‘position’].shift(1)`是因为需要使用前一天的信号来决定今天的操作。执行计算后，可以得到策略的累积收益曲线。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_68.png)

结果算出来了。`returns`是按实际大盘走势持有的收益，结果是亏损的。而基于统计的方法，虽然也是亏损，但赔得稍微少一点。最终收益小于1，肯定是赔了，但如果你不按照这个统计策略，可能会赔得更多。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_70.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_72.png)

---

![](img/a9ec277a11a0acaae73d42f7aacaebd3_74.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_76.png)

本节课中我们一起学习了在量化分析中如何运用统计方法。主要说明了在统计分析中可以用哪些方法，其实质就是比较各种情况发生的可能性大小，选择概率更大的那一种。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_78.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_80.png)

这节课主要介绍了`groupby`操作该如何使用，以及对结果该如何进行指定和统计。方法非常简单，就是通过观察和计数来完成。

![](img/a9ec277a11a0acaae73d42f7aacaebd3_82.png)

![](img/a9ec277a11a0acaae73d42f7aacaebd3_84.png)

这就是本节课中，如何用Python代码进行简单的统计分析并制定策略。需要说明的是，这种基于简单统计的策略并不是特别靠谱，本节主要目的是解释其基本含义、操作的基本出发点，以及展示一个简单的结果。