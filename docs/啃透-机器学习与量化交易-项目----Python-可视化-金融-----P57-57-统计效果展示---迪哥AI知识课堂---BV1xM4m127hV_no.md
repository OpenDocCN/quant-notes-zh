# 机器学习与量化交易：P57：统计效果展示 📊

![](img/9f3ad8df59f4e8173f038be8a866d004_0.png)

在本节课中，我们将学习如何对聚类分析的结果进行统计，并基于统计结果制定一个简单的交易策略。我们将通过观察特征组合与结果的关系，统计不同情况下的涨跌概率，并最终评估该策略的预测效果。

![](img/9f3ad8df59f4e8173f038be8a866d004_2.png)

---

![](img/9f3ad8df59f4e8173f038be8a866d004_4.png)

## 数据变换与结果统计

![](img/9f3ad8df59f4e8173f038be8a866d004_6.png)

上一节我们介绍了如何使用聚类方法对数据进行分组。结果拿到手之后，接下来需要对这些分组结果进行统计分析，目的是观察哪一种特征组合（即“可能性”）对应的结果更多或更少。

![](img/9f3ad8df59f4e8173f038be8a866d004_8.png)

![](img/9f3ad8df59f4e8173f038be8a866d004_10.png)

首先，我们需要对数据稍作变换。当前数据中，前两列是特征（如“前一天涨跌”、“前两天涨跌”），最后一列是聚类结果。我们需要将它们组合起来观察。

![](img/9f3ad8df59f4e8173f038be8a866d004_12.png)

以下是关键的数据处理步骤：

![](img/9f3ad8df59f4e8173f038be8a866d004_14.png)

![](img/9f3ad8df59f4e8173f038be8a866d004_16.png)

```python
# 对分组后的结果进行数据透视，填充缺失值为0
result_pivot = grouped_result.unstack(fill_value=0)
```

![](img/9f3ad8df59f4e8173f038be8a866d004_18.png)

执行此操作后，数据会以更清晰的表格形式展示。表格的行和列分别代表两个特征的不同状态（例如，1代表涨，-1代表跌），表格中的值则代表对应特征组合下，被标记为某个特定结果（例如“涨”）的样本数量。这样，我们就能直观地看到不同特征组合下结果的分布情况。

![](img/9f3ad8df59f4e8173f038be8a866d004_20.png)

---

![](img/9f3ad8df59f4e8173f038be8a866d004_22.png)

![](img/9f3ad8df59f4e8173f038be8a866d004_24.png)

## 制定并应用统计策略

![](img/9f3ad8df59f4e8173f038be8a866d004_26.png)

基于上述统计表格，我们可以制定一个简单的策略。核心思想是：**统计在特定特征组合下，出现“涨”或“跌”结果的次数，并选择出现次数更多的结果作为预测**。

![](img/9f3ad8df59f4e8173f038be8a866d004_28.png)

例如，从统计表中我们可能观察到：当“前一天涨”（值为1）且“前两天涨”（值为1）时，接下来一天出现“跌”的结果次数更多。那么，我们的策略就可以定义为：当遇到这种特征组合时，预测下一个交易日会下跌。

以下是实现该策略判断逻辑的代码：

![](img/9f3ad8df59f4e8173f038be8a866d004_30.png)

```python
import numpy as np

![](img/9f3ad8df59f4e8173f038be8a866d004_32.png)

# 假设 data 是包含特征列 ‘feature1‘（前一天）和 ‘feature2‘（前两天）的DataFrame
# 计算两个特征值之和，判断是否等于2（即两者都为1，代表前两天都上涨）
data[‘position‘] = np.where((data[‘feature1‘] + data[‘feature2‘]) == 2, -1, 1)
```

![](img/9f3ad8df59f4e8173f038be8a866d004_34.png)

在这段代码中，我们创建了一个新的列 `position`。当 `feature1` 和 `feature2` 之和等于2（即前两天都上涨）时，我们预测下跌，赋值为 `-1`；其他情况下，我们预测上涨，赋值为 `1`。

---

![](img/9f3ad8df59f4e8173f038be8a866d004_36.png)

## 策略效果评估

![](img/9f3ad8df59f4e8173f038be8a866d004_38.png)

策略制定好后，我们需要评估它的效果。评估方法是：将我们的预测结果 `position` 与实际的市场走势 `direction` 进行比较，计算两者一致的次数。

![](img/9f3ad8df59f4e8173f038be8a866d004_40.png)

![](img/9f3ad8df59f4e8173f038be8a866d004_42.png)

以下是计算预测准确率的代码：

![](img/9f3ad8df59f4e8173f038be8a866d004_44.png)

```python
# 比较预测值(position)与实际值(direction)是否一致
correct_predictions = (data[‘position‘] == data[‘direction‘]).value_counts()

![](img/9f3ad8df59f4e8173f038be8a866d004_46.png)

print(correct_predictions)
```

![](img/9f3ad8df59f4e8173f038be8a866d004_48.png)

输出结果可能显示，在总共2135个交易日中，有1102天预测正确，1033天预测错误。这表明基于此简单统计的策略，其预测准确率略高于50%。

![](img/9f3ad8df59f4e8173f038be8a866d004_50.png)

为了更直观地评估策略的盈利能力，我们可以模拟按照此策略进行交易，并计算累积收益。

![](img/9f3ad8df59f4e8173f038be8a866d004_52.png)

以下是计算策略累积收益的代码示例（需与大盘基准收益对比）：

![](img/9f3ad8df59f4e8173f038be8a866d004_54.png)

![](img/9f3ad8df59f4e8173f038be8a866d004_56.png)

```python
# 计算策略的累积收益：当预测为涨(position=1)时，获得当日市场收益；预测为跌(position=-1)时，不获得收益（或视为空仓）。
strategy_returns = data[‘returns‘] * data[‘position‘].shift(1)  # 使用前一天的预测来决定今天的操作
cumulative_strategy_returns = (1 + strategy_returns).cumprod()

![](img/9f3ad8df59f4e8173f038be8a866d004_58.png)

![](img/9f3ad8df59f4e8173f038be8a866d004_60.png)

# 计算大盘的累积收益
cumulative_market_returns = (1 + data[‘returns‘]).cumprod()

![](img/9f3ad8df59f4e8173f038be8a866d004_62.png)

# 输出最终收益
print(‘策略最终收益：‘, cumulative_strategy_returns.iloc[-1])
print(‘大盘最终收益：‘, cumulative_market_returns.iloc[-1])
```

执行后可能会发现，虽然策略预测准确率仅略高于一半，但其模拟交易的最终亏损 (`0.998`) 要小于单纯持有大盘的亏损 (`0.997`)。这说明该简单统计策略在一定程度上起到了减少损失的作用，尽管它并不能保证盈利。

![](img/9f3ad8df59f4e8173f038be8a866d004_64.png)

![](img/9f3ad8df59f4e8173f038be8a866d004_66.png)

---

![](img/9f3ad8df59f4e8173f038be8a866d004_68.png)

## 本节总结

![](img/9f3ad8df59f4e8173f038be8a866d004_70.png)

![](img/9f3ad8df59f4e8173f038be8a866d004_72.png)

本节课中，我们一起学习了如何对聚类结果进行统计分析，并基于统计规律制定简单的量化交易策略。

![](img/9f3ad8df59f4e8173f038be8a866d004_74.png)

![](img/9f3ad8df59f4e8173f038be8a866d004_76.png)

我们主要掌握了以下内容：
1.  **数据透视**：使用 `unstack` 方法将分组数据转换为易于观察的统计表格。
2.  **策略制定**：通过观察统计表格，找出不同特征组合下占多数的结果，并以此作为预测规则。核心逻辑用 **`np.where(condition, x, y)`** 实现。
3.  **效果评估**：通过比较预测值与真实值计算准确率，并通过模拟交易计算累积收益，与大盘基准进行对比。

![](img/9f3ad8df59f4e8173f038be8a866d004_78.png)

需要强调的是，这种基于简单频率统计的策略**并不十分可靠**，因为它过于简化，没有考虑更多市场因素和风险。本节内容主要目的是解释统计分析在量化交易中的基本应用思路、实现方法以及如何对策略进行初步验证。