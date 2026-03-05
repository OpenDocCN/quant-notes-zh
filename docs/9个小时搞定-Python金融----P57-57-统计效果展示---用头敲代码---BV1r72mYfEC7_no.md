# Python金融量化分析：P57：统计效果展示 📊

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_0.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_2.png)

在本节课中，我们将学习如何对分组后的数据进行统计分析，并基于统计结果制定简单的交易策略。我们将使用 `groupby` 和 `unstack` 等操作来观察特征与结果之间的关系，并计算策略的预测准确率与最终收益。

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_4.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_6.png)

---

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_8.png)

## 数据变换与结果展示

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_10.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_12.png)

上一节我们介绍了如何使用 `groupby` 对数据进行分组。结果拿到手之后，接下来需要做统计，观察哪一种可能性多，哪一种可能性少。

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_14.png)

首先，需要对数据做一点变换。当前数据将两个特征（前一天涨跌和前两天的涨跌）与一个结果（当天涨跌）组合在了一起。我们需要观察这两个特征与结果之间的关系。

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_16.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_18.png)

以下是关键的数据变换步骤：

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_20.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_22.png)

```python
# 对分组结果进行 unstack 操作，填充缺失值为0
result = data.groupby(['feature1', 'feature2', 'result']).size().unstack(fill_value=0)
```

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_24.png)

执行这段代码后，`result` 是一个数据透视表。它的行索引是特征组合（例如 `(1, 1)` 代表前一天涨、前两天也涨），列索引是结果（1代表涨，-1代表跌），表格中的数值是这种组合出现的次数。这样，我们就可以清晰地看到在不同特征组合下，涨跌结果的分布情况。

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_26.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_28.png)

---

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_30.png)

## 制定并应用统计策略

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_32.png)

基于上面的统计结果，我们可以制定一个简单的策略。策略的核心思想是：找出在特定特征组合下，出现概率更高的结果，并依此进行预测。

以下是制定策略的步骤：

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_34.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_36.png)

1.  **观察统计结果**：分析 `result` 透视表，找出每个特征组合下出现次数最多的结果。
2.  **定义策略规则**：根据观察，我们可以制定一条规则。例如，如果发现当 `feature1` 和 `feature2` 都为1（即前两天都上涨）时，结果 `result` 为-1（下跌）的次数更多，那么我们的策略就是：当遇到这种特征组合时，预测接下来会下跌。
3.  **应用策略生成预测列**：根据上述规则，为原始数据中的每一行生成一个预测标签。

以下是应用策略的代码示例：

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_38.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_40.png)

```python
import numpy as np

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_42.png)

# 假设根据统计，我们观察到 (feature1=1, feature2=1) 时，大概率下跌
# 因此制定规则：当两个特征值之和等于2时，预测为-1（跌），否则预测为1（涨）
data['prediction'] = np.where((data['feature1'] + data['feature2']) == 2, -1, 1)
```

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_44.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_46.png)

这段代码使用 `np.where` 进行条件判断，为数据集新增了一列 `prediction`，即我们基于统计策略得到的预测值。

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_48.png)

---

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_50.png)

## 评估策略效果

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_52.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_54.png)

策略制定完成后，需要评估其效果。我们主要从两个方面进行评估：预测准确率和最终收益。

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_56.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_58.png)

以下是评估策略的步骤：

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_60.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_62.png)

1.  **计算预测准确率**：将我们的预测结果 `prediction` 与实际结果 `direction` 进行比较，计算两者一致的次数。

    ```python
    # 计算预测正确的天数
    correct_predictions = (data['prediction'] == data['direction']).value_counts()
    print(correct_predictions)
    ```
    输出结果中 `True` 的数量代表预测正确的天数，`False` 的数量代表预测错误的天数。

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_64.png)

2.  **计算策略收益**：模拟按照我们的预测进行交易（例如，预测涨则买入，预测跌则卖出或空仓），计算累积收益，并与“始终持有”的基准收益进行对比。

    ```python
    # 计算策略收益：预测为1时，获得当日收益率；预测为-1时，收益为0（空仓）
    strategy_returns = data['returns'] * (data['prediction'] == 1)
    cumulative_strategy_return = (1 + strategy_returns).cumprod()

    # 计算基准收益（始终持有）
    cumulative_baseline_return = (1 + data['returns']).cumprod()

    # 输出最终收益
    final_strategy_return = cumulative_strategy_return.iloc[-1]
    final_baseline_return = cumulative_baseline_return.iloc[-1]
    print(f"策略最终收益: {final_strategy_return}")
    print(f"基准最终收益: {final_baseline_return}")
    ```
    通过对比可以发现，基于简单统计的策略可能比盲目持有表现稍好，但未必能获得稳定正收益。

---

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_66.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_68.png)

## 本节总结

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_70.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_72.png)

本节课中我们一起学习了如何对分组数据进行统计分析并制定交易策略。

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_74.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_76.png)

*   我们首先使用 **`groupby().size().unstack()`** 将数据转换为透视表，直观展示特征与结果的关系。
*   接着，通过观察统计结果，我们制定了 **“数概率”** 的简单策略，并使用 **`np.where`** 将其应用于数据。
*   最后，我们从 **预测准确率** 和 **模拟收益** 两个角度评估了策略效果。结果显示，这种基于历史频率的简单统计方法可能有一定参考价值，但并非稳定可靠的策略。

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_78.png)

![](img/30e3a1fcd146f9e0b2250d0f91f381b6_80.png)

这种方法的核心是 **比较不同情况下的可能性大小**，其优点是直观易懂，但缺点在于过于依赖历史模式，对未来变化的适应性较弱。它主要帮助我们理解数据分析的基本流程和 `groupby` 等工具的实际应用。