# Python金融量化交易：P57：统计效果展示 📊

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_0.png)

在本节课中，我们将学习如何对量化交易策略的预测结果进行统计分析。我们将使用`groupby`方法对数据进行分组统计，并根据统计结果制定简单的交易策略，最后评估该策略的实际效果。

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_2.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_4.png)

---

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_6.png)

## 数据准备与变换

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_8.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_10.png)

上一节我们介绍了如何生成预测结果，本节中我们来看看如何对这些结果进行统计分析。首先，我们需要对数据进行一些变换，以便清晰地观察特征与结果之间的关系。

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_12.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_14.png)

当前数据中，前两列是特征（例如“前一天涨跌”和“前两天涨跌”），最后一列是预测结果。为了进行分组统计，我们需要将结果列进行重塑。

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_16.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_18.png)

以下是关键的数据变换代码：

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_20.png)

```python
# 对分组后的结果进行计数，并使用unstack将结果重塑为更易读的表格形式
result = data.groupby(['feature1', 'feature2', 'result_column']).size().unstack(fill_value=0)
```

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_22.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_24.png)

执行这段代码后，我们将得到一个数据透视表。这个表格的行由两个特征组合而成，列是不同的结果（例如“涨”或“跌”），单元格内的数值表示该特征组合下对应结果出现的次数。这样，我们就可以直观地看到不同特征组合下，哪种结果出现的可能性更大。

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_26.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_28.png)

---

## 制定统计策略

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_30.png)

在得到统计表格后，接下来我们需要根据统计规律来制定交易策略。核心思想是：找出出现频率最高的结果，并将其作为该特征组合下的预测方向。

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_32.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_34.png)

以下是分析过程：

1.  **观察统计结果**：我们检查表格，找出每个特征组合下，结果“1”（代表上涨）和“-1”（代表下跌）哪个数量更多。
2.  **制定规则**：例如，我们发现当“前一天涨”和“前两天涨”同时发生时（即两个特征值都为1），结果“-1”（下跌）出现的次数更多。
3.  **转化为策略**：根据上述观察，我们可以制定一条规则：如果`(前一天涨跌 + 前两天涨跌) == 2`，则预测下一个交易日下跌；否则，预测上涨。

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_36.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_38.png)

这个判断逻辑可以用以下代码实现：

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_40.png)

```python
import numpy as np
# 根据特征列计算条件和
condition_sum = data[‘feature1’] + data[‘feature2’]
# 制定策略：和为2时预测跌(-1)，否则预测涨(1)
data[‘position’] = np.where(condition_sum == 2, -1, 1)
```

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_42.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_44.png)

---

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_46.png)

## 策略效果评估

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_48.png)

策略制定完成后，我们必须评估其有效性。我们将策略预测的方向与实际的市场走势进行比较。

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_50.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_52.png)

以下是评估步骤：

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_54.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_56.png)

1.  **比较预测与实际**：将我们生成的`position`列与真实的`direction`列进行比较，计算预测正确的天数。
    ```python
    # 计算预测正确的次数
    correct_predictions = (data[‘position’] == data[‘direction’]).value_counts()
    ```
2.  **计算策略收益**：我们不仅要知道预测对错的数量，更要看策略是否能带来正的收益。我们模拟按照策略信号进行交易（例如，预测涨则买入，预测跌则卖出或空仓），并计算累计收益。
    ```python
    # 计算策略收益：策略信号 * 实际收益率
    strategy_returns = data[‘position’].shift(1) * data[‘actual_returns’]
    cumulative_strategy_returns = (1 + strategy_returns).cumprod()
    ```
3.  **与基准对比**：通常，我们会将策略的累计收益与“买入并持有”大盘指数的收益进行对比，以判断策略是否具有超额收益。

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_58.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_60.png)

在我们的示例中，统计结果显示，基于简单统计规律的策略预测正确的天数略多于错误的天数。在收益回测中，该策略虽然未能实现盈利，但其亏损幅度小于单纯跟随大盘走势的亏损幅度。这表明，即使简单的统计分析，也可能提供一定的风险改善作用。

---

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_62.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_64.png)

## 核心要点总结

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_66.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_68.png)

本节课中我们一起学习了量化交易中基于统计分析的策略构建与评估流程：

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_70.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_72.png)

1.  **数据分组统计**：使用`df.groupby().size().unstack()`方法，可以清晰展示不同特征组合下各类结果出现的频率。
2.  **策略规则制定**：核心是**比较概率**。选择每个特征组合下出现频率最高的结果作为预测方向，从而形成“if-then”式的交易规则。
3.  **回测与评估**：策略必须通过历史数据进行回测验证。评估维度包括**预测准确率**和**最终收益**，并且需要与基准（如大盘）进行比较。

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_74.png)

![](img/de88d7aac3ebc51f5da2e3aa1d7852b5_76.png)

需要强调的是，本节演示的是一种非常基础且理想化的统计方法。在实际复杂的金融市场中，这样的简单策略往往不够稳健。本节的目的是展示策略开发的基本思路和`groupby`等Pandas工具在量化分析中的实际应用。