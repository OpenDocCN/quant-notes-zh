# Python金融分析量化交易：P57：3-统计效果展示 📊

![](img/aceb17b69c6a2554db02cd81b23ba612_0.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_2.png)

在本节课中，我们将学习如何对分组后的数据进行统计分析，以观察不同特征组合下结果的分布规律，并基于统计结果制定简单的交易策略。

![](img/aceb17b69c6a2554db02cd81b23ba612_4.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_6.png)

---

![](img/aceb17b69c6a2554db02cd81b23ba612_8.png)

上一节我们介绍了如何使用 `groupby` 对数据进行分组。本节中，我们来看看如何对分组结果进行统计和可视化，并基于统计规律制定策略。

![](img/aceb17b69c6a2554db02cd81b23ba612_10.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_12.png)

首先，我们需要对分组后的数据进行一点变换，以便更清晰地观察特征与结果之间的关系。当前的数据将两个特征（前一天涨跌和前两天的涨跌）与最终结果组合在了一起。

![](img/aceb17b69c6a2554db02cd81b23ba612_14.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_16.png)

以下是对结果列进行操作以展示所有可能组合的代码：

![](img/aceb17b69c6a2554db02cd81b23ba612_18.png)

```python
# 对分组结果进行数据透视，填充缺失值为0
result = data.groupby(['feature1', 'feature2', 'result']).size().unstack(fill_value=0)
```

![](img/aceb17b69c6a2554db02cd81b23ba612_20.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_22.png)

执行这段代码后，我们得到一个数据透视表。这个表格清晰地展示了当前两个特征（`feature1`和`feature2`）取不同值时，对应的最终结果（涨或跌）的计数。这样，我们就可以直观地看到哪种特征组合下，上涨或下跌的可能性更大。

![](img/aceb17b69c6a2554db02cd81b23ba612_24.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_26.png)

---

![](img/aceb17b69c6a2554db02cd81b23ba612_28.png)

接下来，我们需要基于观察到的统计规律来制定一个简单的策略。我们的目标是：根据“前一天涨跌”和“前两天的涨跌”这两个特征，预测第三天的走势。

![](img/aceb17b69c6a2554db02cd81b23ba612_30.png)

以下是执行策略判断的代码：

![](img/aceb17b69c6a2554db02cd81b23ba612_32.png)

```python
import numpy as np

![](img/aceb17b69c6a2554db02cd81b23ba612_34.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_36.png)

# 判断条件：当‘前一天’和‘前两天’都上涨（值均为1）时，我们认为第三天会下跌（-1），否则为上涨（1）
data['position'] = np.where((data['feature1'] + data['feature2']) == 2, -1, 1)
```

这段代码的逻辑是：计算两个特征值的和。如果和等于2（即两个特征值都为1，代表前两天都上涨），则我们预测下一个交易日会下跌，将`position`标记为`-1`；在其他所有情况下，我们预测下一个交易日会上涨，将`position`标记为`1`。这个判断纯粹是基于我们刚才从统计表中“数出来”的规律。

![](img/aceb17b69c6a2554db02cd81b23ba612_38.png)

---

![](img/aceb17b69c6a2554db02cd81b23ba612_40.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_42.png)

策略制定好后，我们需要评估它的效果。我们将策略预测的走势（`position`列）与实际走势（`direction`列）进行比较。

![](img/aceb17b69c6a2554db02cd81b23ba612_44.png)

以下是计算预测准确率的代码：

![](img/aceb17b69c6a2554db02cd81b23ba612_46.png)

```python
# 比较预测值与实际值，统计预测正确的天数
correct_predictions = (data['position'] == data['direction']).value_counts()
print(correct_predictions)
```

![](img/aceb17b69c6a2554db02cd81b23ba612_48.png)

执行后，我们可能会得到类似 `True: 1102, False: 1033` 的结果。这表示在总共2135个交易日中，我们的策略正确预测了1102天，错误预测了1033天。虽然准确率只是略高于50%，但说明这个简单的统计策略比随机猜测稍微好一点。

![](img/aceb17b69c6a2554db02cd81b23ba612_50.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_52.png)

---

![](img/aceb17b69c6a2554db02cd81b23ba612_54.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_56.png)

最后，我们来计算一下，如果严格按照这个统计策略进行交易，最终的收益回报会是多少，并与单纯跟随大盘走势的收益进行对比。

![](img/aceb17b69c6a2554db02cd81b23ba612_58.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_60.png)

以下是计算策略收益的代码：

```python
# 计算策略收益：预测的买卖方向 * 实际收益率
strategy_returns = data['position'].shift(1) * data['returns']
# 计算累积收益
cumulative_strategy_returns = (1 + strategy_returns).cumprod()

![](img/aceb17b69c6a2554db02cd81b23ba612_62.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_64.png)

# 计算大盘基准收益
cumulative_market_returns = (1 + data['returns']).cumprod()
```

![](img/aceb17b69c6a2554db02cd81b23ba612_66.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_68.png)

执行计算后，我们可能会发现，基于统计策略的累积收益曲线虽然最终也可能亏损，但亏损幅度小于单纯跟随大盘的收益曲线。这验证了我们的统计策略具有一定的风险缓释作用，尽管它并非一个可以稳定盈利的“圣杯”。

![](img/aceb17b69c6a2554db02cd81b23ba612_70.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_72.png)

---

![](img/aceb17b69c6a2554db02cd81b23ba612_74.png)

![](img/aceb17b69c6a2554db02cd81b23ba612_76.png)

本节课中我们一起学习了如何对分组数据进行统计分析和可视化，并基于观察到的统计规律构建了一个简单的量化交易策略。我们了解到，这种基于历史频率统计的方法核心思想是**比较不同情况下的可能性大小**，其实现主要依赖于`groupby`、数据透视以及`np.where`条件判断。需要强调的是，这类策略相对简单，并不十分可靠，但它清晰地展示了量化分析的基本出发点和工作流程：从数据中寻找规律，并将规律转化为可执行的代码逻辑。