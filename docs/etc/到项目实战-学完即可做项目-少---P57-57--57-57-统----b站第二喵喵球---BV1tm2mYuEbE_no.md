# 量化交易实战：P57：统计效果展示 📊

![](img/3956c114e56aa3d2e6c2b1efd991a49c_0.png)

在本节课中，我们将学习如何对量化策略进行统计分析。具体来说，我们将基于历史数据，通过统计不同特征组合下市场涨跌的频率，来构建一个简单的预测策略，并评估其效果。

![](img/3956c114e56aa3d2e6c2b1efd991a49c_2.png)

---

## 数据准备与变换

![](img/3956c114e56aa3d2e6c2b1efd991a49c_4.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_6.png)

上一节我们介绍了如何生成特征数据。本节中，我们来看看如何对这些数据进行处理，以便进行统计分析。

![](img/3956c114e56aa3d2e6c2b1efd991a49c_8.png)

首先，我们拥有包含“前一天涨跌”和“前两天涨跌”两个特征，以及“当日实际涨跌”结果的数据。为了进行统计，我们需要对数据进行透视操作，清晰地展示不同特征组合下的结果分布。

![](img/3956c114e56aa3d2e6c2b1efd991a49c_10.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_12.png)

以下是实现数据透视的代码：

![](img/3956c114e56aa3d2e6c2b1efd991a49c_14.png)

```python
# 假设 df 是包含特征列 ‘feature1‘, ‘feature2‘ 和结果列 ‘result‘ 的DataFrame
pivot_table = df.groupby([‘feature1‘, ‘feature2‘, ‘result‘]).size().unstack(fill_value=0)
```

![](img/3956c114e56aa3d2e6c2b1efd991a49c_16.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_18.png)

执行这段代码后，我们将得到一个数据透视表。这个表格的行由两个特征的不同组合构成，列则是实际的结果（例如涨或跌），表格中的数值代表了该特征组合下出现对应结果的次数。这样，我们就能直观地看到哪种情况出现的频率更高。

![](img/3956c114e56aa3d2e6c2b1efd991a49c_20.png)

---

![](img/3956c114e56aa3d2e6c2b1efd991a49c_22.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_24.png)

## 统计分析与策略制定

![](img/3956c114e56aa3d2e6c2b1efd991a49c_26.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_28.png)

得到数据透视表后，接下来我们需要从中提取规律，并制定交易策略。

我们的目标是：基于“前一天”和“前两天”的涨跌情况，预测“当天”的涨跌。方法就是统计历史中，在特定特征组合下，涨和跌哪种结果出现的次数更多。

以下是观察数据透视表并制定策略的步骤：

![](img/3956c114e56aa3d2e6c2b1efd991a49c_30.png)

1.  **观察频率**：在透视表中，找到特征组合（例如，前一天涨`1`，前两天涨`1`）对应的行，比较“涨”和“跌”两列的数值大小。
2.  **制定规则**：如果“跌”的次数明显多于“涨”，则我们认为在该特征组合下，下一次更可能“跌”，反之亦然。
3.  **代码实现规则**：根据观察到的规律，我们可以用条件判断来生成预测信号。

![](img/3956c114e56aa3d2e6c2b1efd991a49c_32.png)

例如，如果我们发现当 `feature1 == 1` 且 `feature2 == 1` 时，历史数据显示下跌概率大，我们可以用以下代码生成预测列：

```python
import numpy as np
# 生成预测信号：当两天都上涨时，预测下跌(-1)，否则预测上涨(1)
df[‘prediction‘] = np.where((df[‘feature1‘] == 1) & (df[‘feature2‘] == 1), -1, 1)
```

![](img/3956c114e56aa3d2e6c2b1efd991a49c_34.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_36.png)

---

![](img/3956c114e56aa3d2e6c2b1efd991a49c_38.png)

## 策略效果评估

![](img/3956c114e56aa3d2e6c2b1efd991a49c_40.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_42.png)

策略制定完成后，我们必须评估它在历史数据上的表现。

![](img/3956c114e56aa3d2e6c2b1efd991a49c_44.png)

评估的方法是：将我们的预测结果`prediction`与实际的涨跌结果`direction`进行比较，计算预测正确的天数比例。同时，我们还可以模拟按照这个策略进行交易（涨时买入，跌时卖出或空仓）的累计收益，并与简单持有（即跟随大盘涨跌）的收益进行对比。

![](img/3956c114e56aa3d2e6c2b1efd991a49c_46.png)

以下是进行评估的代码示例：

![](img/3956c114e56aa3d2e6c2b1efd991a49c_48.png)

```python
# 1. 计算预测准确率
correct_predictions = (df[‘prediction‘] == df[‘direction‘]).value_counts()
print(“预测正确天数：“, correct_predictions.get(True, 0))
print(“预测错误天数：“, correct_predictions.get(False, 0))

![](img/3956c114e56aa3d2e6c2b1efd991a49c_50.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_52.png)

# 2. 计算策略收益（简化版：预测为涨则获得当日收益率，预测为跌则收益为0）
strategy_returns = df[‘daily_returns‘] * (df[‘prediction‘] == 1).astype(int)
cumulative_strategy_return = (1 + strategy_returns).cumprod().iloc[-1] - 1

![](img/3956c114e56aa3d2e6c2b1efd991a49c_54.png)

# 3. 计算基准收益（简单持有）
cumulative_baseline_return = (1 + df[‘daily_returns‘]).cumprod().iloc[-1] - 1

![](img/3956c114e56aa3d2e6c2b1efd991a49c_56.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_58.png)

print(“策略累计收益：“, cumulative_strategy_return)
print(“基准累计收益：“, cumulative_baseline_return)
```

运行这段代码后，我们可以得到两个关键信息：一是预测的准确率，二是策略相对于简单持有是否创造了超额收益。即使准确率只是略高于50%，如果策略能成功避开大的下跌，其累计收益也可能优于基准。

![](img/3956c114e56aa3d2e6c2b1efd991a49c_60.png)

---

![](img/3956c114e56aa3d2e6c2b1efd991a49c_62.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_64.png)

## 核心概念总结

![](img/3956c114e56aa3d2e6c2b1efd991a49c_66.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_68.png)

本节课中我们一起学习了量化策略中的统计分析方法。

![](img/3956c114e56aa3d2e6c2b1efd991a49c_70.png)

![](img/3956c114e56aa3d2e6c2b1efd991a49c_72.png)

1.  **数据透视**：使用`groupby().size().unstack()`方法，将数据转换为以特征组合为行、以结果为列的频率表，便于直观分析。
2.  **频率统计策略**：其核心思想是**比较历史条件概率**。即，在给定的历史特征`X`下，统计结果`Y`为涨或跌的次数，并选择频率更高的一方作为预测。公式上，即选择使 `P(Y=涨|X)` 或 `P(Y=跌|X)` 更大的那个`Y`值。
3.  **策略评估**：一个策略的好坏不能只看预测准确率，必须通过**回测**计算其在历史数据上的**累计收益**，并与市场基准进行比较，这是量化交易中验证策略有效性的关键步骤。

![](img/3956c114e56aa3d2e6c2b1efd991a49c_74.png)

这种基于统计频率的策略虽然原理简单直接，但它清晰地展示了量化分析的基本流程：**数据处理 -> 规律挖掘 -> 规则制定 -> 回测评估**。它为理解更复杂的模型奠定了基础。