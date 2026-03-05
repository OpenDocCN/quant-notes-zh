# 量化交易教程：P57：统计效果展示 📊

![](img/cf1968fa9eb5a1097bcf89a44b490cad_0.png)

在本节课中，我们将学习如何对量化策略的预测结果进行统计分析，并展示其效果。我们将使用`groupby`操作来统计不同特征组合下的结果分布，并基于统计结果制定简单的交易策略，最后评估该策略的收益表现。

![](img/cf1968fa9eb5a1097bcf89a44b490cad_2.png)

---

## 数据准备与变换

![](img/cf1968fa9eb5a1097bcf89a44b490cad_4.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_6.png)

上一节我们介绍了如何生成特征数据，本节中我们来看看如何对这些数据进行变换，以便进行统计分析。

![](img/cf1968fa9eb5a1097bcf89a44b490cad_8.png)

首先，我们需要关注的是前两个特征（如前一天的涨跌和前两天的涨跌）以及最终的结果（如当天的涨跌）。当前数据可能被组合在一起，我们需要对结果列进行操作。

![](img/cf1968fa9eb5a1097bcf89a44b490cad_10.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_12.png)

以下是关键的数据变换步骤，使用`groupby`和`unstack`方法：

![](img/cf1968fa9eb5a1097bcf89a44b490cad_14.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_16.png)

```python
# 对分组后的结果进行计数，并使用unstack将结果重塑为更易读的格式
result = data.groupby(['feature1', 'feature2', 'result']).size().unstack(fill_value=0)
```

![](img/cf1968fa9eb5a1097bcf89a44b490cad_18.png)

执行此操作后，`fill_value=0`参数确保即使某些组合没有出现，也会以0值显示。这样我们得到一个清晰的表格，其中行代表不同的特征组合，列代表对应的结果（如涨或跌）及其出现次数。

![](img/cf1968fa9eb5a1097bcf89a44b490cad_20.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_22.png)

---

![](img/cf1968fa9eb5a1097bcf89a44b490cad_24.png)

## 策略制定与统计

![](img/cf1968fa9eb5a1097bcf89a44b490cad_26.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_28.png)

数据变换完成后，接下来我们需要基于统计结果制定交易策略。

以下是制定策略的核心步骤。我们统计在特定特征组合下，哪种结果出现的可能性更大。

1.  **计算特征和**：我们首先计算两个特征值的和。例如，特征值1（前一天涨跌）和特征值2（前两天涨跌）都用1表示上涨，用0或其他值表示下跌。
    ```python
    # 假设‘feature1’和‘feature2’是已经计算好的特征列
    condition = (data['feature1'] + data['feature2'] == 2)
    ```

![](img/cf1968fa9eb5a1097bcf89a44b490cad_30.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_32.png)

2.  **根据统计结果分配信号**：根据之前的统计表格观察，当两个特征值之和为2（即前两天都上涨）时，第三天更可能下跌；其他情况下更可能上涨。
    ```python
    import numpy as np
    # 根据条件生成交易信号：1代表看跌，-1代表看涨（或反之，根据实际定义调整）
    data['position'] = np.where(condition, 1, -1)
    ```

这个策略纯粹基于历史数据的频率统计：选择出现次数更多的结果作为预测信号。

![](img/cf1968fa9eb5a1097bcf89a44b490cad_34.png)

---

![](img/cf1968fa9eb5a1097bcf89a44b490cad_36.png)

## 策略效果评估

![](img/cf1968fa9eb5a1097bcf89a44b490cad_38.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_40.png)

策略制定后，我们需要评估其预测准确性以及与市场实际走势的对比。

![](img/cf1968fa9eb5a1097bcf89a44b490cad_42.png)

首先，我们比较策略预测的信号与实际涨跌方向的一致性：

![](img/cf1968fa9eb5a1097bcf89a44b490cad_44.png)

```python
# 比较预测位置‘position’与实际方向‘direction’的一致性
correct_predictions = (data['position'] == data['direction']).value_counts()
print(correct_predictions)
```

![](img/cf1968fa9eb5a1097bcf89a44b490cad_46.png)

输出结果可能显示预测正确的天数和错误的天数，例如（1102次正确，1033次错误），这可以给出策略预测准确率的直观感受。

![](img/cf1968fa9eb5a1097bcf89a44b490cad_48.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_50.png)

接着，我们计算按照此策略进行交易的理论收益，并与简单跟随大盘的收益进行对比：

![](img/cf1968fa9eb5a1097bcf89a44b490cad_52.png)

```python
# 计算策略收益：假设‘returns’是每日收益率
strategy_returns = data['returns'] * data['position'].shift(1)  # 使用前一天的信号
cumulative_strategy_return = (1 + strategy_returns).cumprod()

![](img/cf1968fa9eb5a1097bcf89a44b490cad_54.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_56.png)

# 计算简单买入持有的收益
buy_hold_return = (1 + data['returns']).cumprod()

![](img/cf1968fa9eb5a1097bcf89a44b490cad_58.png)

# 输出最终累计收益进行比较
print(f"策略累计收益: {cumulative_strategy_return.iloc[-1]}")
print(f"买入持有累计收益: {buy_hold_return.iloc[-1]}")
```

![](img/cf1968fa9eb5a1097bcf89a44b490cad_60.png)

执行后，可能会发现基于统计的策略其累计收益（例如0.998）虽然也为负，但亏损幅度小于简单的买入持有策略（例如0.985）。这说明该统计策略在一定程度上减少了损失。

![](img/cf1968fa9eb5a1097bcf89a44b490cad_62.png)

---

![](img/cf1968fa9eb5a1097bcf89a44b490cad_64.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_66.png)

## 核心要点总结

![](img/cf1968fa9eb5a1097bcf89a44b490cad_68.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_70.png)

本节课中我们一起学习了量化策略中的统计分析方法。

![](img/cf1968fa9eb5a1097bcf89a44b490cad_72.png)

![](img/cf1968fa9eb5a1097bcf89a44b490cad_74.png)

*   **核心方法**：我们使用 **`DataFrame.groupby().size().unstack()`** 来统计不同特征组合下结果的频率分布。
*   **策略逻辑**：策略基于历史统计概率，为出现频率高的结果分配交易信号，公式表示为：**`position = np.where((feature1 + feature2 == 2), 1, -1)`**。
*   **评估方式**：通过比较预测信号与实际方向的吻合度，以及计算策略的累计收益来评估效果。

![](img/cf1968fa9eb5a1097bcf89a44b490cad_76.png)

这种基于统计的策略虽然逻辑简单直接，但其效果严重依赖于历史数据的规律能否在未来重现，通常作为理解市场行为和策略构建的起点。本节主要目的是演示如何使用Python进行基础的数据分组统计，并将统计结果转化为可执行的交易逻辑。