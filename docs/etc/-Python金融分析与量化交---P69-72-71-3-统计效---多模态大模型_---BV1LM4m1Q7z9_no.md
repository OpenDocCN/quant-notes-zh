# Python金融分析与量化交易实战：P69：统计效果展示

![](img/aca301140d9aba60a28c644be69d349d_0.png)

## 概述
在本节课中，我们将学习如何对处理后的金融数据进行统计分析，并基于统计结果构建一个简单的交易策略。我们将使用`groupby`方法统计不同特征组合下的结果分布，并根据统计出的高概率事件来制定买卖决策，最后评估该策略的收益表现。

![](img/aca301140d9aba60a28c644be69d349d_2.png)

![](img/aca301140d9aba60a28c644be69d349d_4.png)

---

![](img/aca301140d9aba60a28c644be69d349d_6.png)

![](img/aca301140d9aba60a28c644be69d349d_8.png)

## 数据变换与统计准备

![](img/aca301140d9aba60a28c644be69d349d_10.png)

![](img/aca301140d9aba60a28c644be69d349d_12.png)

上一节我们介绍了如何构建特征数据。本节中，我们来看看如何对这些数据进行统计。

![](img/aca301140d9aba60a28c644be69d349d_14.png)

![](img/aca301140d9aba60a28c644be69d349d_16.png)

现在，我们拥有两个特征列（如前一天的涨跌和前两天的涨跌）和一个结果列（如当天的涨跌）。为了进行统计，我们需要对数据进行分组操作。

![](img/aca301140d9aba60a28c644be69d349d_18.png)

以下是数据变换的核心步骤：
```python
# 假设 df 是我们的DataFrame，包含特征列 'feature1', 'feature2' 和结果列 'result'
grouped_result = df.groupby(['feature1', 'feature2', 'result']).size().unstack(fill_value=0)
```
这段代码的作用是：
1.  使用 `groupby` 按照两个特征列和结果列进行分组。
2.  使用 `.size()` 计算每个分组的大小。
3.  使用 `.unstack(fill_value=0)` 将结果列（‘result’）从行索引转换为列，形成一个透视表。`fill_value=0` 参数确保没有数据的组合显示为0，而不是被隐藏。

执行后，我们得到一个表格，行索引是特征的不同组合（如 `(1, 1)`, `(1, -1)` 等），列索引是结果（如 `1` 代表涨，`-1` 代表跌），表格内的值是该特征组合下出现对应结果的次数。这使我们能够直观地看到在特定历史特征下，未来涨跌的分布情况。

![](img/aca301140d9aba60a28c644be69d349d_20.png)

---

## 基于统计结果的策略制定

![](img/aca301140d9aba60a28c644be69d349d_22.png)

![](img/aca301140d9aba60a28c644be69d349d_24.png)

![](img/aca301140d9aba60a28c644be69d349d_26.png)

在得到统计透视表后，我们可以分析哪种情况出现的概率更高。

![](img/aca301140d9aba60a28c644be69d349d_28.png)

![](img/aca301140d9aba60a28c644be69d349d_30.png)

例如，通过观察表格，我们可能发现：当 `feature1` 为 1（前一天涨）且 `feature2` 为 1（前两天涨）时，结果列中 `-1`（跌）的计数远大于 `1`（涨）的计数。这意味着，在“连续上涨两天”的历史情况下，接下来一天下跌的概率较高。

![](img/aca301140d9aba60a28c644be69d349d_32.png)

![](img/aca301140d9aba60a28c644be69d349d_34.png)

基于这个统计发现，我们可以制定一个简单的策略规则：
- **如果** 前一天和前两天的涨跌信号之和等于 2（即两者都为涨），**则** 预测下一个交易日会跌，我们标记为 `-1`（卖出或看空）。
- **否则**，预测下一个交易日会涨，标记为 `1`（买入或看多）。

![](img/aca301140d9aba60a28c644be69d349d_36.png)

![](img/aca301140d9aba60a28c644be69d349d_38.png)

以下是实现此策略判断的代码：
```python
import numpy as np
# 假设 data 是包含特征列的DataFrame
features = data[['feature1', 'feature2']].values
# 根据特征和是否等于2来生成预测信号
position = np.where((features[:, 0] + features[:, 1]) == 2, -1, 1)
```
这段代码使用 `np.where` 进行条件判断，生成了一个与数据等长的预测信号数组 `position`。

![](img/aca301140d9aba60a28c644be69d349d_40.png)

![](img/aca301140d9aba60a28c644be69d349d_42.png)

---

![](img/aca301140d9aba60a28c644be69d349d_44.png)

![](img/aca301140d9aba60a28c644be69d349d_46.png)

## 策略效果评估

![](img/aca301140d9aba60a28c644be69d349d_48.png)

![](img/aca301140d9aba60a28c644be69d349d_50.png)

制定策略后，我们需要评估它的效果。我们将策略的预测结果与实际的市场走势进行比较。

![](img/aca301140d9aba60a28c644be69d349d_52.png)

首先，计算预测准确的次数：
```python
# 假设 data[‘direction’] 是实际的涨跌列
actual = data['direction']
predicted = position # 上一步生成的预测信号
# 计算预测正确的次数
correct_predictions = (actual == predicted).value_counts()
```
`correct_predictions` 会显示 `True`（预测正确）和 `False`（预测错误）各自的数量，这可以给出策略预测准确率的一个直观感受。

![](img/aca301140d9aba60a28c644be69d349d_54.png)

![](img/aca301140d9aba60a28c644be69d349d_56.png)

![](img/aca301140d9aba60a28c644be69d349d_58.png)

接着，进行更重要的回测，计算若按此策略交易，所能获得的累计收益：
```python
# 假设 data[‘returns’] 是每日的实际收益率
strategy_returns = data['returns'] * predicted.shift(1) # 使用前一天的信号决定今天的操作
cumulative_strategy_returns = (1 + strategy_returns).cumprod()
# 同时计算基准收益（如始终持有）
cumulative_baseline_returns = (1 + data['returns']).cumprod()
```
通过比较 `cumulative_strategy_returns` 与 `cumulative_baseline_returns` 的最终值，我们可以判断这个基于统计的策略是超越了大盘，还是跑输了大盘。

![](img/aca301140d9aba60a28c644be69d349d_60.png)

![](img/aca301140d9aba60a28c644be69d349d_62.png)

![](img/aca301140d9aba60a28c644be69d349d_64.png)

---

![](img/aca301140d9aba60a28c644be69d349d_66.png)

![](img/aca301140d9aba60a28c644be69d349d_68.png)

## 总结
本节课中我们一起学习了量化分析中的一个基础方法：统计分析。
1.  我们使用 `groupby` 和 `unstack` 对特征与结果的关系进行了统计汇总，得到了不同历史情况下的结果分布。
2.  通过观察统计结果，我们制定了“在连续两日上涨后看跌，其余情况看涨”的简单策略，并使用 `np.where` 实现了信号生成。
3.  最后，我们通过对比预测准确率和计算策略累计收益，评估了该策略的实战效果。结果显示，虽然此类简单统计策略可能不会带来巨大盈利，但它提供了一种将数据洞察转化为具体交易逻辑的系统化方法，并可能在一定程度上改善投资表现。这为我们后续学习更复杂的模型奠定了基础。