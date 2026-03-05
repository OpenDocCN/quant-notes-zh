# Python金融分析与量化交易实战：P57：统计效果展示 📊

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_0.png)

在本节课中，我们将学习如何对之前处理好的股票数据进行统计分析，以观察不同特征组合下股价涨跌的规律，并基于统计结果构建一个简单的交易策略。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_2.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_4.png)

## 数据准备与变换

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_6.png)

上一节我们介绍了如何对数据进行分组和聚合。本节中我们来看看如何对聚合后的数据进行变换，以便进行统计分析。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_8.png)

当前数据中，前两列（如“前一天涨跌”、“前两天涨跌”）是特征，最后一列是结果（如“当天涨跌”）。我们需要将它们组合起来观察。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_10.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_12.png)

首先，对结果列进行操作，使用 `unstack()` 方法将数据转换为更清晰的透视表格式。这会将缺失值填充为0，确保所有特征组合都被展示出来。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_14.png)

```python
# 假设 df 是分组聚合后的DataFrame
result_pivot = df['result_column'].unstack(fill_value=0)
```

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_16.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_18.png)

执行后，我们得到一个表格，行和列分别是两个特征的不同状态，表格内的值是对应的结果计数。这清晰地展示了特征与结果之间的关系。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_20.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_22.png)

## 统计分析与策略制定

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_24.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_26.png)

数据变换完成后，接下来需要进行统计分析，观察规律。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_28.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_30.png)

以下是基于透视表的观察步骤：
1.  **识别特征组合**：查看表格中行和列对应的特征值（例如，1代表上涨，-1代表下跌）。
2.  **统计结果分布**：对于每一个特征组合（如“前一天涨，前两天涨”），查看其对应的结果列中，上涨（1）和下跌（-1）的计数各是多少。
3.  **判断大概率事件**：比较每个特征组合下，上涨和下跌的计数。计数多的结果即为该特征组合下“大概率”发生的事件。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_32.png)

根据示例中的统计，我们可能发现一个规律：如果前一天上涨且前两天也上涨，那么第三天大概率下跌。其他特征组合下，则大概率上涨。

## 构建交易信号

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_34.png)

基于上述统计规律，我们可以构建一个简单的交易策略信号。

具体方法是，遍历数据，判断每一天的“前一天”和“前两天”特征组合，并根据统计出的大概率事件，生成一个预测的涨跌信号（`position`）。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_36.png)

```python
import numpy as np

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_38.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_40.png)

# 假设 data 是原始数据，包含 ‘feature1‘ (前一天), ‘feature2‘ (前两天) 列
# 根据统计规律：当两天都上涨（值都为1）时，预测下跌(-1)，否则预测上涨(1)
data[‘position‘] = np.where((data[‘feature1‘] + data[‘feature2‘]) == 2, -1, 1)
```

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_42.png)

这段代码的核心逻辑是：**如果两个特征值之和等于2（即两者都为1，代表连续上涨），则预测信号为-1（看跌）；否则预测信号为1（看涨）**。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_44.png)

## 策略效果评估

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_46.png)

策略构建完成后，需要评估其有效性。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_48.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_50.png)

我们将策略预测的涨跌信号（`position`）与实际发生的涨跌情况（`direction`）进行比较，计算预测正确的天数。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_52.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_54.png)

```python
# 比较预测值与实际值
correct_predictions = (data[‘position‘] == data[‘direction‘]).value_counts()
print(correct_predictions)
```

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_56.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_58.png)

输出结果会显示 `True`（预测正确）和 `False`（预测错误）的计数。通过对比，可以直观看出基于此简单统计规律的策略，其预测准确率是否优于随机猜测（例如50%）。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_60.png)

最后，我们可以将此策略的收益曲线与单纯持有股票（或大盘）的收益曲线进行对比，观察策略是否能带来超额收益或减少损失。计算累计收益的公式如下：

**累计收益 = (1 + 每日收益率).cumprod()**

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_62.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_64.png)

在示例中，基于此统计策略的累计收益虽然仍为亏损，但亏损幅度小于单纯持有股票的收益曲线，表明该简单统计策略在一定程度上有过滤风险的效果。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_66.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_68.png)

## 总结

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_70.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_72.png)

本节课中我们一起学习了如何对金融数据进行统计分析并构建简易交易策略。
1.  我们使用 `unstack()` 方法对分组聚合后的数据进行透视，清晰展示了特征与结果的关系。
2.  通过观察透视表，我们统计了不同特征组合下结果值的分布，找出“大概率”事件。
3.  基于统计规律，我们使用 `np.where()` 函数构建了交易信号。
4.  最后，通过对比预测准确率和收益曲线，我们对策略效果进行了初步评估。

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_74.png)

![](img/4b842cdaed0e7d657a7bbc166e8d9c33_76.png)

这种方法的核心思想是**比较不同情况下的可能性，并遵循概率更大的路径**。它展示了 `groupby` 与统计思维在量化分析中的基础应用。需要注意的是，这类简单统计策略通常不够稳健，主要用于理解分析流程和策略构建的基本概念。