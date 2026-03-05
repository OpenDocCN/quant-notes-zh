# 股票数据分析：P57：3-统计效果展示 📊

![](img/e0532b5c3fbffb25bc46f420a27d0514_0.png)

在本节课中，我们将学习如何对股票数据进行统计分析，以探索不同特征组合下股价涨跌的规律。我们将使用`groupby`方法进行分组统计，并根据统计结果制定一个简单的交易策略，最后评估该策略的收益表现。

---

![](img/e0532b5c3fbffb25bc46f420a27d0514_2.png)

![](img/e0532b5c3fbffb25bc46f420a27d0514_4.png)

上一节我们介绍了数据的基本处理，本节中我们来看看如何对处理后的数据进行统计分析。

![](img/e0532b5c3fbffb25bc46f420a27d0514_6.png)

首先，我们需要对数据进行变换，以便观察不同特征组合下的结果分布。具体来说，我们关注“前一天涨跌”和“前两天涨跌”这两个特征，以及它们对应的“当天涨跌”结果。

![](img/e0532b5c3fbffb25bc46f420a27d0514_8.png)

以下是数据变换的步骤：
1.  使用`groupby`方法，按“前一天涨跌”和“前两天涨跌”这两个特征进行分组。
2.  对分组后的“当天涨跌”结果列使用`.size()`方法进行计数。
3.  使用`.unstack()`方法将结果重塑为更易读的表格形式。
4.  使用`fill_value=0`参数填充表格中可能出现的空值。

![](img/e0532b5c3fbffb25bc46f420a27d0514_10.png)

```python
# 对特征组合进行分组统计
grouped_result = data.groupby(['feature_day-1', 'feature_day-2'])['direction'].size().unstack(fill_value=0)
```

![](img/e0532b5c3fbffb25bc46f420a27d0514_12.png)

执行上述代码后，我们得到一个统计表格。表格的行和列分别代表“前一天涨跌”和“前两天涨跌”的特征值（例如，1代表涨，-1代表跌），表格中的数字则代表了在该特征组合下，“当天涨跌”结果出现的次数。

![](img/e0532b5c3fbffb25bc46f420a27d0514_14.png)

---

基于上一步得到的统计表格，我们可以观察不同特征组合下，哪种结果（涨或跌）出现的次数更多。这构成了我们制定策略的基础。

以下是策略制定的逻辑：
1.  观察统计表格，找出每个特征组合下出现次数最多的结果。
2.  根据观察，我们发现当“前一天涨”且“前两天也涨”（即两个特征值之和等于2）时，第三天“跌”的可能性更大。
3.  在其他特征组合下，“涨”的可能性更大。

根据这个观察，我们制定一个简单的策略规则：
*   如果 `(前一天涨跌 + 前两天涨跌) == 2`，则预测当天为“跌”（用-1表示）。
*   否则，预测当天为“涨”（用1表示）。

```python
# 根据统计规律生成预测信号
data['position_stat'] = np.where((data['feature_day-1'] + data['feature_day-2']) == 2, -1, 1)
```

---

![](img/e0532b5c3fbffb25bc46f420a27d0514_16.png)

上一节我们根据统计规律生成了预测信号，本节中我们来看看这个策略的实际效果如何。

我们将策略预测的涨跌方向`position_stat`与实际的涨跌方向`direction`进行比较，计算预测正确的天数。

![](img/e0532b5c3fbffb25bc46f420a27d0514_18.png)

```python
# 计算预测正确的次数
correct_predictions = (data['position_stat'] == data['direction']).value_counts()
print(correct_predictions)
```

![](img/e0532b5c3fbffb25bc46f420a27d0514_20.png)

结果显示，在总共的样本天数中，预测正确的次数略多于预测错误的次数。这表明基于简单统计的策略具有一定的预测能力，但并非完全准确。

![](img/e0532b5c3fbffb25bc46f420a27d0514_22.png)

![](img/e0532b5c3fbffb25bc46f420a27d0514_24.png)

---

最后，我们来评估这个统计策略在模拟交易中的收益表现。我们将基于策略信号进行买卖，并计算其累计收益率，同时与单纯跟随大盘走势的收益率进行对比。

![](img/e0532b5c3fbffb25bc46f420a27d0514_26.png)

![](img/e0532b5c3fbffb25bc46f420a27d0514_28.png)

以下是计算收益的步骤：
1.  策略收益率：使用策略生成的`position_stat`信号（1代表买入/持有，-1代表卖出/做空）乘以股票的实际日收益率。
2.  基准收益率：假设始终持有股票（即信号始终为1），计算其累计收益率。
3.  比较两种收益率的最终结果。

```python
# 计算策略收益率
strategy_returns = data['returns'] * data['position_stat'].shift(1)
cumulative_strategy_returns = (1 + strategy_returns).cumprod()

![](img/e0532b5c3fbffb25bc46f420a27d0514_30.png)

# 计算基准（买入持有）收益率
benchmark_returns = data['returns']
cumulative_benchmark_returns = (1 + benchmark_returns).cumprod()

![](img/e0532b5c3fbffb25bc46f420a27d0514_32.png)

![](img/e0532b5c3fbffb25bc46f420a27d0514_34.png)

# 输出最终累计收益率
print(f"策略最终净值: {cumulative_strategy_returns.iloc[-1]:.4f}")
print(f"基准最终净值: {cumulative_benchmark_returns.iloc[-1]:.4f}")
```

![](img/e0532b5c3fbffb25bc46f420a27d0514_36.png)

![](img/e0532b5c3fbffb25bc46f420a27d0514_38.png)

结果显示，基于统计的策略其最终收益率可能仍然为负（即亏损），但亏损幅度小于单纯跟随大盘走势的基准策略。这说明该简单统计策略起到了一定的风险控制作用，但未能实现盈利。

![](img/e0532b5c3fbffb25bc46f420a27d0514_40.png)

![](img/e0532b5c3fbffb25bc46f420a27d0514_42.png)

---

![](img/e0532b5c3fbffb25bc46f420a27d0514_44.png)

本节课中我们一起学习了如何对股票数据进行统计分析并制定交易策略。我们首先使用`groupby`方法统计了不同特征组合下的结果分布，然后根据“少数服从多数”的原则制定了一个简单的预测规则。最后，我们通过回测发现，这个基于统计的策略虽然比盲目跟随大盘表现稍好，能够减少部分亏损，但总体上并不可靠。本课的主要目的是演示数据分析的基本流程和`groupby`等工具的使用方法，以及理解简单统计策略的构建思路与局限性。