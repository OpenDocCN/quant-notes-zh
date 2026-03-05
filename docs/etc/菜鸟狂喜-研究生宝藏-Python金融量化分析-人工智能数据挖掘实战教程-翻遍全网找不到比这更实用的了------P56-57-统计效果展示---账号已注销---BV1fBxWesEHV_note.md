# Python金融量化分析：P56：统计效果展示 📊

![](img/a47d67f14a698b8858def4904f1ac770_0.png)

![](img/a47d67f14a698b8858def4904f1ac770_2.png)

在本节课中，我们将学习如何对金融数据进行统计分析，以探索不同特征组合下市场走势（涨跌）的规律。我们将使用`groupby`和统计计数的方法，构建一个简单的预测策略，并评估其效果。

![](img/a47d67f14a698b8858def4904f1ac770_4.png)

![](img/a47d67f14a698b8858def4904f1ac770_6.png)

---

![](img/a47d67f14a698b8858def4904f1ac770_8.png)

## 数据准备与变换

![](img/a47d67f14a698b8858def4904f1ac770_10.png)

上一节我们介绍了如何构建特征。本节中，我们来看看如何对数据进行变换，以便进行统计分析。

![](img/a47d67f14a698b8858def4904f1ac770_12.png)

![](img/a47d67f14a698b8858def4904f1ac770_14.png)

当前数据中，前两列是特征（例如“前一天涨跌”和“前两天涨跌”），最后一列是目标结果（例如“当天涨跌”）。为了进行统计，我们需要将特征组合视为一个整体来观察对应的结果分布。

![](img/a47d67f14a698b8858def4904f1ac770_16.png)

![](img/a47d67f14a698b8858def4904f1ac770_18.png)

以下是数据变换步骤：

![](img/a47d67f14a698b8858def4904f1ac770_20.png)

1.  使用`groupby`方法，按照前两个特征列进行分组。
2.  对分组后的“结果”列应用`size`方法，统计每种特征组合出现的次数。
3.  使用`unstack`方法将结果重新排列，使数据更易于观察。

![](img/a47d67f14a698b8858def4904f1ac770_22.png)

![](img/a47d67f14a698b8858def4904f1ac770_24.png)

```python
# 假设 df 是包含特征和结果的DataFrame
# feature1, feature2 是特征列名，result是结果列名
grouped_result = df.groupby([‘feature1‘, ‘feature2‘])[‘result‘].size().unstack(fill_value=0)
```

![](img/a47d67f14a698b8858def4904f1ac770_26.png)

执行以上代码后，我们将得到一个表格。表格的行和列索引分别代表两个特征的不同状态（例如1代表涨，-1代表跌），表格中的数值则代表了在对应特征组合下，结果为“涨”或“跌”的次数。这使我们能够直观地看到不同特征组合下结果的分布情况。

![](img/a47d67f14a698b8858def4904f1ac770_28.png)

![](img/a47d67f14a698b8858def4904f1ac770_30.png)

---

![](img/a47d67f14a698b8858def4904f1ac770_32.png)

![](img/a47d67f14a698b8858def4904f1ac770_34.png)

## 制定与执行统计策略

在得到统计分布表格后，接下来我们需要制定一个简单的交易策略。策略的核心思想是：选择在历史上出现概率更高的结果作为预测。

以下是策略制定过程：

![](img/a47d67f14a698b8858def4904f1ac770_36.png)

![](img/a47d67f14a698b8858def4904f1ac770_38.png)

1.  **观察统计结果**：分析`grouped_result`表格，找出对于每一种特征组合，结果是“涨”的次数多还是“跌”的次数多。
2.  **定义策略规则**：根据观察，定义一条预测规则。例如，如果发现“前一天涨(`feature1=1`)且前两天涨(`feature2=1`)”时，历史上“跌”的次数更多，则当该组合出现时，我们预测下一个交易日会“跌”。
3.  **生成预测列**：根据定义好的规则，为原始数据中的每一行生成一个新的预测列（`position`）。

![](img/a47d67f14a698b8858def4904f1ac770_40.png)

```python
import numpy as np

# 根据统计观察定义规则：当两个特征值之和为2时（即都为1），预测为-1（跌），否则预测为1（涨）
df[‘position‘] = np.where(df[[‘feature1‘, ‘feature2‘]].sum(axis=1) == 2, -1, 1)
```

![](img/a47d67f14a698b8858def4904f1ac770_42.png)

![](img/a47d67f14a698b8858def4904f1ac770_44.png)

通过这段代码，我们为数据集中的每一天都生成了一个基于历史统计的涨跌预测信号。

![](img/a47d67f14a698b8858def4904f1ac770_46.png)

![](img/a47d67f14a698b8858def4904f1ac770_48.png)

---

![](img/a47d67f14a698b8858def4904f1ac770_50.png)

## 策略效果评估

策略制定完成后，我们必须评估其有效性。我们将预测结果与实际市场走势进行比较。

![](img/a47d67f14a698b8858def4904f1ac770_52.png)

以下是评估步骤：

![](img/a47d67f14a698b8858def4904f1ac770_54.png)

![](img/a47d67f14a698b8858def4904f1ac770_56.png)

1.  **计算预测准确率**：将预测列(`position`)与实际结果列(`direction`)进行比较，统计预测正确的天数。
2.  **计算策略收益**：基于预测信号进行简单的模拟交易（例如，预测涨则买入，预测跌则卖出或空仓），计算累积收益，并与单纯持有（或跟随大盘）的收益进行对比。

![](img/a47d67f14a698b8858def4904f1ac770_58.png)

![](img/a47d67f14a698b8858def4904f1ac770_60.png)

```python
# 1. 计算预测准确率
correct_predictions = (df[‘position‘] == df[‘direction‘]).value_counts()
print(f“预测正确的天数: {correct_predictions.get(True, 0)}“)
print(f“预测错误的天数: {correct_predictions.get(False, 0)}“)

![](img/a47d67f14a698b8858def4904f1ac770_62.png)

![](img/a47d67f14a698b8858def4904f1ac770_64.png)

# 2. 计算策略收益 (简化模型：假设每次都以收盘价全仓交易)
df[‘strategy_returns‘] = df[‘position‘].shift(1) * df[‘market_returns‘] # market_returns 是市场日收益率
cumulative_strategy_returns = (1 + df[‘strategy_returns‘]).cumprod()
cumulative_market_returns = (1 + df[‘market_returns‘]).cumprod()

![](img/a47d67f14a698b8858def4904f1ac770_66.png)

![](img/a47d67f14a698b8858def4904f1ac770_68.png)

# 输出最终收益对比
print(f“策略累计收益: {cumulative_strategy_returns.iloc[-1]:.4f}“)
print(f“市场累计收益: {cumulative_market_returns.iloc[-1]:.4f}“)
```

评估结果可能显示，基于简单统计的策略其预测准确率可能略高于50%，但累计收益未必能持续跑赢市场。这揭示了简单历史统计模型的局限性。

![](img/a47d67f14a698b8858def4904f1ac770_70.png)

---

![](img/a47d67f14a698b8858def4904f1ac770_72.png)

![](img/a47d67f14a698b8858def4904f1ac770_74.png)

## 总结

![](img/a47d67f14a698b8858def4904f1ac770_76.png)

![](img/a47d67f14a698b8858def4904f1ac770_78.png)

本节课中我们一起学习了如何利用`groupby`进行数据分组统计，并基于统计结果构建一个简单的量化交易策略。

![](img/a47d67f14a698b8858def4904f1ac770_80.png)

我们首先对特征和结果数据进行了变换与重组，使其便于观察规律。接着，通过分析不同特征组合下结果的历史分布，我们制定了一条“少数服从多数”的预测规则。最后，我们通过计算预测准确率和模拟收益，对该统计策略的效果进行了评估。

![](img/a47d67f14a698b8858def4904f1ac770_82.png)

![](img/a47d67f14a698b8858def4904f1ac770_84.png)

需要强调的是，这种基于简单历史频率统计的策略**通常不够稳健**，因为它假设历史模式会在未来简单重演，而忽略了市场的复杂性和变化性。本课的主要目的是展示数据分析的基本流程和方法（`groupby`, `unstack`, `np.where`），以及策略回测的基本概念，为后续学习更复杂的模型打下基础。