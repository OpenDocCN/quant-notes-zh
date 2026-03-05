# Python金融分析+量化交易：P57：统计效果展示 📊

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_0.png)

在本节课中，我们将学习如何对处理后的数据进行统计分析，以观察不同特征组合下市场走势（涨跌）的分布规律，并基于此构建一个简单的统计预测策略。

## 数据变换与统计目标

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_2.png)

上一节我们介绍了如何构建特征。结果拿到手之后，接下来需要做统计。统计的目标是观察在特定的特征组合下，哪一种结果（涨或跌）出现的可能性更多。

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_4.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_6.png)

现在数据中，前两列是特征（如“前一天涨跌”、“前两天涨跌”），最后一列是结果（“当天涨跌”）。但当前数据格式是将它们都作为特征组合在了一起。因此，我们需要对数据做一点变换，以便进行分组统计。

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_8.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_10.png)

以下是对结果列进行操作的核心代码：

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_12.png)

```python
# 对数据进行分组，并统计每个特征组合下结果的数量
grouped_result = data.groupby(['feature1', 'feature2'])['result'].size()
```

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_14.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_16.png)

执行后，我们得到一个统计表，它清晰地展示了目标：即在不同特征变化下，最终结果是如何分布的。

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_18.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_20.png)

## 策略制定与统计

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_22.png)

基于上述统计结果，我们现在要制定一个交易策略。策略的核心思想是：找出历史上在特定特征组合下，出现概率更高的结果，并假设未来在相同条件下，该结果更可能发生。

以下是执行策略判断的步骤：

首先，我们需要根据特征计算一个条件。例如，判断“前一天涨”和“前两天涨”这两个条件是否同时满足。

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_24.png)

```python
# 计算特征列的和，判断是否等于2（即前两天都上涨）
condition = (data['feature1'] + data['feature2']) == 2
```

然后，我们根据统计结果来赋值。从之前的统计图可以看出：

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_26.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_28.png)

*   当 `feature1` 和 `feature2` 都为1（上涨）时，结果列中 `-1`（下跌）的数量更多。
*   在其他特征组合下，结果列中 `1`（上涨）的数量更多。

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_30.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_32.png)

因此，我们的策略可以定义为：

```python
# 根据条件判断，生成预测的仓位信号
data['position'] = np.where(condition, -1, 1)
```
这里，`position` 列就是我们基于统计规律生成的预测信号（-1代表看跌，1代表看涨）。

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_34.png)

需要强调的是，`condition == 2` 这个判断纯粹是根据我们当前数据集的统计结果“数出来的”。不同的数据集可能会得到不同的统计规律。

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_36.png)

## 策略效果评估

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_38.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_40.png)

策略制定完成后，我们需要评估它的效果。评估方法是：将我们的预测信号 `position` 与实际的市场走势 `direction` 进行比较，计算预测正确的天数。

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_42.png)

以下是计算预测准确率的代码：

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_44.png)

```python
# 比较预测值与实际值是否一致
correct_predictions = (data['position'] == data['direction'])
# 统计一致（True）和不一致（False）的次数
prediction_counts = correct_predictions.value_counts()
```
假设结果显示，在总共2135天中，预测正确1102天，错误1033天。这说明基于统计的方法，预测正确的次数略多于错误次数。

为了更直观地评估策略的盈利能力，我们可以将其与简单地跟随大盘走势（即“买入并持有”策略）进行对比。计算两种策略的累计收益率：

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_46.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_48.png)

```python
# 计算统计策略的累计收益
statistical_returns = (data['returns'] * data['position']).cumsum()
# 计算大盘走势的累计收益
market_returns = data['returns'].cumsum()
```
结果显示，虽然两种策略最终都可能亏损，但基于统计的策略亏损幅度（例如0.1%）小于单纯跟随大盘的亏损幅度。这表明该统计策略在一定程度上有助于减少损失。

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_50.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_52.png)

## 总结

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_54.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_56.png)

本节课中，我们一起学习了如何利用 `groupby` 进行数据分组统计，从而观察特征与结果之间的关系。基于统计规律，我们构建了一个简单的预测策略，其核心是 **比较不同情况下的可能性大小**，并选择历史概率更高的结果作为预测。

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_58.png)

![](img/07ccdfd6e0daafa336d4a1d0dd509e9e_60.png)

我们演示了如何使用 `np.where` 根据条件生成交易信号，并通过与实际值对比、计算累计收益来评估策略效果。需要指出，这类简单的统计分析策略并不十分可靠，其主要目的是解释策略构建的基本出发点、方法和流程，为后续更复杂的模型打下基础。